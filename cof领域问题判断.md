def _chat_domain_check_uncached(question: str) -> dict[str, Any]:
    messages = [
        {
            "role": "system",
            "content": (
                "你是 COF-System AI4S demo 的领域入口守卫。"
                "判断用户问题是否属于本 demo 支持的 COF、共价有机框架、有机化学、有机/多孔材料、"
                "单体/连接体/连接键、合成条件、稳定性、光电/光催化/HER 等材料知识问答范围。"
                "如果问题需要这些领域知识或可以通过 COF/材料知识图谱检索回答，判定为 true。"
                "如果是天气、新闻、编程、金融、闲聊、医学、通用常识、数学题或其他无关主题，判定为 false。"
                "只输出严格 JSON，不要 Markdown，不要解释。格式："
                "{\"in_domain\": true, \"reason\": \"一句中文理由\"}"
            ),
        },
        {"role": "user", "content": question},
    ]    def _unsupported_domain_answer(domain_check: dict[str, Any]) -> str:
    reason = str(domain_check.get("reason") or "该问题不属于 COF/有机材料领域。")
    return (
        "这个问题不属于本 demo 支持的 COF、共价有机框架、有机化学或材料知识问答范围，"
        "因此我不会生成 Cypher，也不会检索图谱。"
        f"判断依据：{reason} 请换成 COF 单体、连接键、合成条件、稳定性、性能或应用等相关问题。"
    )
