# Dual-Core AI · 边云协同级联推理引擎

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-red.svg)](https://pytorch.org/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

**Dual-Core AI** 是一个面向边缘-云端协同推理的轻量级AI推理框架，通过「边缘预处理→云端深度生成→边缘事实校验」三段式级联架构，实现**算力消耗降低40%–60%、推理延迟降低30%–70%、幻觉率降至≤5%** 的技术效果。

---

## ✨ 核心特性

- **⚡ 双芯级联架构**：边缘侧Fast-Token轻量模型（0.5B–2B）负责输入去噪、语义压缩与离线兜底；云端侧Global-Memory大模型（7B–14B）负责长上下文推理与深度生成
- **🧠 语义压缩**：基于注意力权重聚类的智能压缩，压缩率40%–60%，信息保留率>95%
- **🛡️ 三重事实校验**：语义一致性检查 + 事实一致性比对 + 安全红线拦截，幻觉率从12.3%降至≤5%
- **🔄 动态调度**：根据网络带宽、边缘算力、任务复杂度实时决策计算分配，断网200ms内自动降级
- **📉 KV Cache优化**：MLA低秩压缩技术，128K上下文KV Cache显存从12.8GB降至0.9GB，降幅93%
- **🌐 农业RAG系统**：基于Milvus构建10M+文档知识库，混合检索召回率提升18%

---

## 🏗️ 架构概览
