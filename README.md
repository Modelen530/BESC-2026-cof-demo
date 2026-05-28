# KGFM COF Demo

This README is a companion document for the recorded system demonstration video included in this repository.

The project is a private research demo artifact prepared for the BESC 2026 Industry & Demo Track, Demo Sub-Track. It presents an interactive AI-for-Science prototype for knowledge-guided covalent organic framework (COF) design and analysis.

This repository is not intended as an open-source software release. It is provided as supporting material for understanding the demo video, the system workflow, and the implemented prototype.

## Demonstration Video

A recorded demonstration video of the system is included in this repository.

The video shows the main user-facing workflow, including graph-based scientific question answering, COF design analysis, monomer library exploration, and interactive 3D molecular visualization.

## What the Demo Shows

- **Graph-based COF question answering:** the system accepts natural-language scientific questions, maps them to graph queries, retrieves structured evidence, and generates an answer.
- **COF design analysis:** the system analyzes node/linker monomer pairs and presents stepwise domain reasoning about reaction compatibility, COF formability, stability-related signals, and HER-related assessment.
- **Monomer library exploration:** the interface supports searching and inspecting monomer records, functional groups, roles, data sources, and related COF structures.
- **3D molecular visualization:** the system provides interactive previews for monomers and generated dimer structures.
- **Evidence-centered interaction:** the interface exposes reasoning steps, generated queries, graph triples, and structured outputs to make the demo process inspectable.

## System Summary

The prototype combines four types of components:

- A web-based demo interface for scientific QA, design analysis, monomer search, and visualization.
- A backend service that coordinates graph queries, monomer resolution, COF analysis, and response generation.
- Local/generated COF knowledge assets, including monomer records, COF records, graph triples, and knowledge-base fragments.
- Molecular visualization utilities for rendering monomer and dimer structures.

## Main Repository Contents

```text
.
├── cof_system_demo/                 # Web demo application and backend routes
├── cof-design-analysis-toolkit-v3/   # COF design analysis toolkit
├── monomer_3d_viewer/               # Monomer and dimer 3D visualization module
├── rag/                             # Retrieval and literature-related local resources
├── run_cof_demo.bat                 # Local demo startup script
└── README_COF_DEMO.md               # Local development notes
```

## Demo Workflow

The recorded video follows a typical use path:

1. Open the local demo interface.
2. Ask a COF-related scientific question in the graph QA page.
3. Inspect the generated graph query, graph evidence, and final answer.
4. Run a COF design analysis task using selected or manually entered monomers.
5. Review the stepwise reasoning chain and structured analysis output.
6. Preview monomer or dimer structures in the 3D viewer.
7. Explore monomer records and related COF structures in the monomer library.

## Demo-Track Relevance

The system is designed as an interactive research prototype rather than a static script. It demonstrates:

- A domain-specific intelligent information system for COF research.
- Integration of knowledge graphs, molecular data, LLM-assisted reasoning, and 3D visualization.
- A practical workflow for scientific evidence inspection and material-design decision support.
- A runnable prototype that supports a screencast-based demo submission.

## Notes

- The repository is mainly used as supporting material for the demonstration video.
- Some runtime features depend on locally configured services or credentials, such as graph databases, molecular toolkits, or optional model APIs.
- Sensitive configuration values, such as API keys and database passwords, are not included in this repository.
- The code and data are not released under an open-source license.

---

# KGFM COF Demo 中文说明

本 README 是仓库中系统演示视频的配套说明文档。

本项目是为 BESC 2026 Industry & Demo Track 中 Demo Sub-Track 准备的私有研究 demo 材料，展示一个面向 COF（Covalent Organic Framework，共价有机框架）设计与分析的知识引导型 AI for Science 交互原型系统。

本仓库不是开源软件发布包，主要用于帮助评审或读者理解演示视频中的系统流程、核心功能和原型实现。

## 演示视频

本仓库包含录制好的系统演示视频。

视频展示了系统的主要交互流程，包括基于知识图谱的科学问答、COF 设计分析、单体库检索和交互式 3D 分子可视化。

## 演示内容

- **基于知识图谱的 COF 问答：** 系统接收自然语言科学问题，将问题映射为图查询，检索结构化证据，并生成回答。
- **COF 设计分析：** 系统对节点单体和连接体组合进行分析，并展示反应兼容性、COF 可形成性、稳定性相关信号和 HER 相关评估。
- **单体库检索：** 界面支持检索和查看单体记录、官能团、角色、数据来源以及相关 COF 结构。
- **3D 分子可视化：** 系统支持单体和生成二聚体结构的交互式 3D 预览。
- **证据导向交互：** 界面展示推理步骤、生成查询、图谱三元组和结构化结果，便于理解系统的分析过程。

## 系统概览

该原型系统主要由四类组件组成：

- 面向科学问答、设计分析、单体检索和可视化的 Web demo 界面。
- 协调图查询、单体解析、COF 分析和答案生成的后端服务。
- 本地/已生成的 COF 知识资产，包括单体记录、COF 记录、图谱三元组和知识库片段。
- 用于渲染单体和二聚体结构的分子可视化工具。

## 主要仓库内容

```text
.
├── cof_system_demo/                 # Web demo 应用与后端路由
├── cof-design-analysis-toolkit-v3/   # COF 设计分析工具包
├── monomer_3d_viewer/               # 单体和二聚体 3D 可视化模块
├── rag/                             # 检索与文献相关本地资源
├── run_cof_demo.bat                 # 本地 demo 启动脚本
└── README_COF_DEMO.md               # 本地开发说明
```

## 视频中的演示流程

录制视频展示了一个典型使用路径：

1. 打开本地 demo 界面。
2. 在图谱问答页面输入一个 COF 相关科学问题。
3. 查看系统生成的图查询、图谱证据和最终回答。
4. 使用选择或手动输入的单体运行 COF 设计分析。
5. 查看分步骤推理链和结构化分析结果。
6. 预览单体或二聚体的 3D 结构。
7. 在单体库页面检索单体记录并查看相关 COF 结构。

## 与 Demo Track 的关联

本系统是一个交互式研究原型，而不是静态分析脚本。它体现了：

- 面向 COF 科学研究的领域特定智能信息系统。
- 知识图谱、分子数据、LLM 辅助推理和 3D 可视化的系统集成。
- 面向科学证据检查和材料设计决策支持的实用工作流。
- 可配合录屏演示提交的可运行系统原型。

## 说明

- 本仓库主要作为演示视频的配套材料使用。
- 部分运行功能依赖本地配置的服务或凭据，例如图数据库、分子工具包或可选模型 API。
- API Key、数据库密码等敏感配置不会包含在本仓库中。
- 本仓库中的代码和数据未按开源许可证发布。
