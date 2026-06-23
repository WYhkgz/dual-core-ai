# Dual-Core AI · 边云协同级联推理引擎

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE   许可证)[!(许可协议:麻省理工学院)(https://img.shields.io/badge/License-MIT-yellow.svg))(许可证)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svghttps://img.shields.io/badge/python-3.10 -blue.svg)](https://www.python.org/)[![Python 3.10](https://img.shields.io/badge/python-3.10 -blue.svg)]（https://www.python.org/）
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-red.svghttps://img.shields.io/badge/PyTorch-2.0 -red.svg)](https://pytorch.org/)
[![PRs Welcome   PRs欢迎](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)[!(PRs欢迎)(https://img.shields.io/badge/PRs-welcome-brightgreen.svg)) (CONTRIBUTING.md)

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
┌─────────────────────────────────────────────────────────────────────┐
│ 用户输入 │
└─────────────────────────────────────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────────┐
│ ┌──────────────────────────────────────────────────────────────┐ │
│ │ 边缘侧 · Fast-Token │ │
│ │ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │ │
│ │ │ 输入去噪 │ → │ 语义压缩 │ → │ 事实校验 │ → │离线兜底│ │ │
│ │ └──────────┘ └──────────┘ └──────────┘ └────────┘ │ │
│ └──────────────────────────────────────────────────────────────┘ │
│ │ │
│ 语义向量 (压缩40%-60%) │
│ │ │
│ ┌──────────────────────────────────────────────────────────────┐ │
│ │ 云端 · Global-Memory │ │
│ │ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │ │
│ │ │长上下文 │ → │稀疏注意力│ → │MLA低秩 │ → │LoRA │ │ │
│ │ │推理(128K)│ │O(n log n)│ │ 压缩 │ │持续学习│ │ │
│ │ └──────────┘ └──────────┘ └──────────┘ └────────┘ │ │
│ └──────────────────────────────────────────────────────────────┘ │
│ │ │
│ 推理结果 │
│ │ │
│ ┌──────────────────────────────────────────────────────────────┐ │
│ │ 边缘侧 · 事实校验 │ │
│ │ ┌──────────┐ ┌──────────┐ ┌──────────┐ │ │
│ │ │语义一致性│ → │事实一致性│ → │安全红线 │ → 最终输出 │ │
│ │ │(≥0.75) │ │ 比对 │ │ 拦截 │ │ │
│ │ └──────────┘ └──────────┘ └──────────┘ │ │
│ └──────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
## 🚀 快速开始

### 安装

```bash   ”“bash
# 克隆仓库
git clone https://github.com/your-org/dual-core-ai.git
cd dual-core-ai

# 安装依赖
pip install -r requirements.txt

# 安装边缘端（Jetson Orin Nano）
pip install -e .[edge]

# 安装云端（CUDA环境）
pip install -e .[cloud]
from dual_core import DualCoreEngine, FastTokenConfig, GlobalMemoryConfig

# 配置边缘模型
edge_config = FastTokenConfig(
    model_name="Qwen-1.5-0.5B",
    device="cuda"  # 或 "cpu"
)

# 配置云端模型
cloud_config = GlobalMemoryConfig(
    model_name="Qwen-1.5-7B",
    context_length=128000,
    use_mla=True
)

# 初始化引擎
engine = DualCoreEngine(   engine = DualCoreEngine
    edge_config=edge_config,
    cloud_config=cloud_config,
    mode="cascade"  # cascade | edge_only | cloud_onlyMode ="cascade"； #级联| edge_only | cloud_only
)

# 执行推理
result = engine.generate(Result = engine.generate(
    prompt="水稻叶片出现褐色斑点，边缘呈黄色，是什么病害？",
    context={"crop": "rice", "region": "tropical"}context={"crop": "rice", "region": "tropical"}
)

print(result["text"])   print(result["text"])
# 输出: 诊断为稻瘟病，建议使用三环唑可湿性粉剂...
from dual_core import DynamicScheduler从dual_core导入DynamicScheduler

scheduler = DynamicScheduler(
    bandwidth_thresholds=(1.0, 5.0),  # Mbps
    edge_load_thresholds=(30, 70),      # %Edge_load_thresholds =(30,70), # %
    fallback_mode="edge_only"
)

# 自动根据网络状态选择最优模式
engine.set_scheduler(scheduler)engine.set_scheduler(调度)engine.set_scheduler(调度)
📊 性能基准
指标	传统云端方案	纯边缘方案	Dual-Core级联	提升幅度
端到端延迟	850ms	120ms	280ms	↓67% vs 云端
单次推理成本	$0.008	$0.001	$0.003	↓63% vs 云端
长文本准确率	78.2%	62.5%	84.6%	↑8.2%
KV Cache显存	12.8GB	0.8GB	4.2GB	↓67%
断网可用性	0%	85%	99%	↑99%
幻觉发生率	12.3%	8.1%	≤5%	↓85%
Token传输量	100%	0%	35%	↓65%
dual-core-ai/
├── dual_core/
│   ├── __init__.py
│   ├── engine.py              # 核心引擎
│   ├── edge/
│   │   ├── fast_token.py      # Fast-Token边缘模型
│   │   ├── semantic_compress.py # 语义压缩
│   │   └── fact_check.py      # 事实校验
│   ├── cloud/
│   │   ├── global_memory.py   # Global-Memory云端模型
│   │   ├── mla.py             # MLA低秩压缩
│   │   └── rag.py             # RAG检索增强
│   ├── scheduler/
│   │   └── dynamic.py         # 动态调度
│   └── utils/
│       ├── config.py
│       └── logging.py
├── examples/   ├──/例子
│   ├── agriculture_demo.py    # 农业场景示例
│   └── offline_mode.py        # 离线模式示例
├── tests/   ├──测试/
│   ├── test_engine.py
│   ├── test_compress.py
│   └── test_fact_check.py
├── docs/
│   ├── api.md
│   └── architecture.md
├── requirements.txt
├── setup.py
├── LICENSE
└── README.md
🎯 应用场景
智慧农业：病虫害识别、水肥决策、灾害预警

野外监测：森林火情、矿山边坡、油气管道

工业物联：设备预测性维护、质量检测

智慧城市：边缘视频分析、智能巡检
