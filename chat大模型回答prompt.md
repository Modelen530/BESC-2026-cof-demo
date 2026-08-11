def _answer_with_combined_evidence(
    question: str,
    cypher: str,
    graph: dict[str, Any],
    kb_fragments: list[dict[str, Any]],
) -> str:
    triples = graph.get("triples", [])
    graph_text = json.dumps(_compact_chat_graph_for_llm(graph), ensure_ascii=False)
    rag_text = json.dumps(_compact_chat_rag_for_llm(kb_fragments), ensure_ascii=False)
    messages = [
        {
            "role": "system",
            "content": (
                "你是 COF 材料领域问答助手。你的任务是把内部图谱证据和文献片段总结成面向普通科研用户的最终回答。"
                "内部证据只用于判断事实和形成结论，不要在最终答案中暴露检索链路或分析过程。"
                "禁止出现这些过程性表述：Neo4j、RAG、知识库检索结果、返回子图、Cypher、三元组、证据来源、文献[1]、文献[2]、文献[3]、文献[4]、基于提供的图谱和文献证据。"
                "不要把答案写成分析步骤，不要逐条说明每条证据来自哪里；如果需要体现依据，请改写成自然表述，例如“相关研究显示”“检索到的材料表明”。"
                "只输出中文纯文本自然段，不使用 Markdown，不使用星号、井号、项目符号、编号标题或加粗标记，不输出 JSON。"
                "回答结构应是：先用一句话直接回答问题，再用一小段概括关键原因、性能或限制。内容要自然通顺、简洁可读，并且只能依据内部证据；证据不足时要明确说现有证据不足以确认。"
                "输出前自检一次：如果答案里仍有 Markdown 符号、编号标题、Neo4j/RAG/Cypher/知识库检索结果/文献编号等内部词，请重写成纯自然语言。"
            ),
        },
        {
            "role": "user",
            "content": (
                "以下内容是内部证据，只用于推理，不要复述字段名、检索过程或文献编号。\n"
                f"用户问题：{question}\n"
                f"内部 Cypher：{cypher}\n"
                f"内部图谱证据：{graph_text}\n"
                f"内部文献片段：{rag_text}\n"
                "请只输出给用户看的最终答案。"
            ),
        },
    ]
    return _require_spark_completion(messages, temperature=0.2, stage="Chat 最终回答")
