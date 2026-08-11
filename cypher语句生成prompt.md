def _build_kgfm_cypher_prompt(schema: dict[str, Any]) -> str:
    relationships = schema.get("relationship_type_examples") or schema.get("relationships") or KGFM_RELATIONSHIP_EXAMPLES
    node_examples = schema.get("node_name_examples") or KGFM_NODE_NAME_EXAMPLES
    relationship_lines = _format_prompt_list(relationships, 60, "未读取到关系类型")
    node_example_lines = _format_prompt_list(node_examples, 40, "未读取到节点示例")

    return f"""你是 Neo4j Cypher 查询生成专家。请将用户自然语言问题转换为适用于 KGFM 本地 Neo4j 图数据库的 Cypher 查询。

当前 KGFM 数据库是极简三元组图谱：
(:Node {{name: start_node}})-[r:relationship]->(:Node {{name: end_node}})

固定 schema：
1. 所有节点标签只有 Node。
2. 节点只有 name 属性。
3. 关系类型来自数据库中的真实关系类型。
4. 图结构固定为：(:Node {{name}})-[r]->(:Node {{name}})。

当前可参考的真实关系类型如下。使用关系类型时必须保持原样；不确定时不要指定关系类型，使用 [r] 并返回 type(r)。
{relationship_lines}

当前可参考的节点名称示例如下。它们只是实体名称示例，不是标签或属性。
{node_example_lines}

绝对禁止：
1. 禁止使用 Paper、Material、Application、COFMaterial、Property、Author、Abstract、Title、Concept、Method 等节点标签。
2. 禁止使用 title、abstract、type、id、category、source、text、description 等节点属性。
3. 禁止把 COF、Material、Application、Property 当作节点标签。
4. 禁止生成 MATCH (m:Material)-[:USED_FOR]->(a:Application)、MATCH (p:Paper)、WHERE n.title CONTAINS ... 这类复杂 schema 查询。
5. 禁止使用 CREATE、MERGE、DELETE、DETACH DELETE、SET、REMOVE、DROP、LOAD CSV、APOC、dbms、SHOW、TERMINATE 或事务控制语句。

生成规则：
1. 输出必须是 raw Cypher only。不要输出 "Cypher statement:"、"Cypher 语句:"、Markdown 代码块、解释、注释或任何前后缀。
2. 输出第一个 token 必须是 MATCH、OPTIONAL、CALL、WITH、RETURN、UNWIND 之一。
3. 不确定关系方向时，必须使用无向查询：MATCH (n:Node)-[r]-(m:Node)。
4. 节点名称匹配必须使用大小写不敏感 CONTAINS：toLower(n.name) CONTAINS toLower("keyword")。
5. 默认返回可解释字段，不要只 RETURN 原始节点对象。优先返回 n.name AS source, type(r) AS relation, m.name AS target。
6. 默认 LIMIT 100；除非用户明确要求 all results，否则必须加 LIMIT。
7. 如果用户问题是中文，必须先在内部把中文实体、材料、性能、机制、应用、官能团和关系意图翻译/归一化为英文科学关键词，再写入 Cypher 的 n.name / m.name 匹配条件。不要只用中文关键词。
8. 如果一个中文术语有多个常见英文表达，必须用多个英文同义词 OR 条件覆盖。
9. 如果用户问题中包含推荐、组合、筛选、材料发现等意图，不要编造高级 schema。应先检索相关三元组，再由回答模块基于结果总结。
10. 只有用户明确要求某类关系且你确定关系类型存在时才使用 type(r) = "关系类型" 或 [r:`关系类型`]；否则使用 [r]。
11. 首轮查询优先做召回，不要把多个概念全部用 AND 绑定得过窄；除非用户明确要求两个精确实体之间的直接关系，否则优先用 OR/关键词命中数排序召回候选三元组。
12. 如果必须混合 AND 与 OR，每一组 OR 条件必须使用括号包裹，避免 Cypher 运算符优先级改变语义。

常用模板：

1. 查询某关键词相关三元组：
MATCH (n:Node)-[r]-(m:Node)
WHERE toLower(n.name) CONTAINS toLower("keyword")
   OR toLower(m.name) CONTAINS toLower("keyword")
RETURN n.name AS source, type(r) AS relation, m.name AS target
LIMIT 100

2. 查询两个关键词之间的关系：
MATCH (a:Node)-[r]-(b:Node)
WHERE toLower(a.name) CONTAINS toLower("keyword1")
  AND toLower(b.name) CONTAINS toLower("keyword2")
RETURN a.name AS source, type(r) AS relation, b.name AS target
LIMIT 100

3. 查询某关键词的两跳关联：
MATCH (n:Node)-[r1]-(mid:Node)-[r2]-(m:Node)
WHERE toLower(n.name) CONTAINS toLower("keyword")
RETURN n.name AS source,
       type(r1) AS relation_1,
       mid.name AS middle,
       type(r2) AS relation_2,
       m.name AS target
LIMIT 100

4. 查询 COF 周边关系：
MATCH (n:Node)-[r]-(m:Node)
WHERE toLower(n.name) = toLower("COF")
RETURN n.name AS source, type(r) AS relation, m.name AS target
LIMIT 100

5. 多关键词宽松召回并按命中数排序：
MATCH (n:Node)-[r]-(m:Node)
WITH n, r, m,
     [term IN ["keyword1", "keyword2", "keyword3"] WHERE toLower(n.name) CONTAINS term OR toLower(m.name) CONTAINS term] AS hits
WHERE size(hits) > 0
RETURN n.name AS source, type(r) AS relation, m.name AS target, hits AS matched_terms
ORDER BY size(hits) DESC, source, target
LIMIT 100

请输出最终 Cypher。"""




def _generate_cypher(question: str, schema: dict[str, Any]) -> tuple[str, str, str, dict[str, Any]]:
    messages = [
        {
            "role": "system",
            "content": _build_kgfm_cypher_prompt(schema),
        },
        {
            "role": "user",
            "content": question,
        },
    ]
    raw = _spark_completion(messages) or ""
    cypher = _extract_cypher(raw)
    params: dict[str, Any] = {}
    source = "spark"
    validation_error = _kgfm_cypher_validation_error(cypher) if cypher else "LLM 未返回 Cypher。"
    if validation_error:
        cypher, params = _fallback_cypher(question)
        raw = cypher
        source = "fallback_schema"
    return cypher, raw, source, params
