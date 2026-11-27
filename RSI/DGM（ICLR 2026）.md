# DGM（ICLR 2026）

![image-20261126051458605](./Image/DGM%EF%BC%88ICLR%202026%EF%BC%89/image-20261126051458605.png)

## ABSTRACT

**如何让 AI 系统在不断提升相关问题求解能力的同时，实现无止境的自我改进？**

元学习（Meta-learning）能够自动发现新的算法，但其局限在于只能实现一阶改进（First-order Improvements），并且依赖人类设计合适的搜索空间。

我们提出了**达尔文哥德尔机（Darwin Gödel Machine，DGM）**，一种新型的自我改进系统。该系统通过迭代修改自身代码，并利用 coding benchmarks评测对每一次修改进行经验性验证（Empirical Validation）。受达尔文进化论和开放式演化（Open-endedness）研究的启发，DGM 不断扩充一个由生成的编程智能体组成的档案库（Archive）。它从档案库中采样智能体，这些智能体通过自我修改，产生具有新颖价值的新版本。

## **Introduction**

废话太多了，不写了，没啥意义，真tm会编

### **1. Empirical Validation（经验性验证）**

传统 Gödel Machine 要求通过数学证明保证每次自我修改都是有益的，但这在实际 AI 系统中难以实现。

DGM 使用 **Benchmark 评测替代形式化证明**，通过实际运行结果衡量修改的有效性。

### **2. Self-referential Self-improvement（自指式自我改进）**

传统 Agent 优化方法使用固定的优化器：

\[ A_{t+1}=\operatorname{Optimizer}(A_t) \]

DGM 则让 **Agent 自己修改自己的源代码**（感觉这个是更以往的工作不同的主要diff）：

\[ A_{t+1}=A_t.\operatorname{modify}(A_t) \]

优化对象包括 Prompt、Tools、Workflow 等 Agent 组件，基础模型参数保持冻结。

**核心假设：** 由于下游任务和自我修改都是编程任务，因此编程能力的提升也意味着自我改进能力的提升，从而形成递归改进的正反馈循环。

[Automated Design of Agentic Systems (ADAS)](https://openreview.net/forum?id=t9U3LW7JVX&utm_source=chatgpt.com)使用一个固定的元智能体（Meta-Agent）生成下游智能体，在目标 Benchmark 上评估这些智能体，并利用评估反馈改进后续生成的智能体。相比之下，DGM 是一个统一的系统，它既能够解决下游任务（即编程问题），也能够改进自身的实现（即自身代码库）。（感觉这段有点牵强了，你就一个coding benchmark，人家好多benchmark，有点难评价，不过DGM应该是从一个很基础很基础的agent进化的）

### **3. Open-ended Exploration（开放式探索）**

维护一个历史 Agent 档案库（Archive），而不是仅保留当前最优 Agent。

- 从 Archive 中采样父代 Agent。
- 父代修改自身代码，生成子代。
- 评估子代的编程能力。
- 将有效子代加入 Archive。

允许暂时性能较差的 Agent 被保留，作为未来创新的踏脚石（Stepping Stones），避免陷入局部最优。

DGM 与 [A Self-Improving Coding Agent (SICA)](https://arxiv.org/abs/2504.15228?utm_source=chatgpt.com)， [Self-Taught Optimizer (STOP): Recursively Self-Improving Code Generation](https://arxiv.org/abs/2310.02304?utm_source=chatgpt.com)、以及[Gödel Agent: A Self-Referential Agent Framework for Recursive Self-Improvement](https://arxiv.org/abs/2410.04444?utm_source=chatgpt.com)的主要区别在于：**DGM 引入了开放式探索循环（Open-ended Exploration Loop），鼓励系统进行不局限于即时性能收益的自我修改，从而避免停滞在次优状态。**

## DARWIN GÖDEL MACHINE

![image-20261127095929253](./Image/DGM%EF%BC%88ICLR%202026%EF%BC%89/image-20261127095929253.png)

哥德尔机（Gödel Machine）基本思想是构建一种能够递归修改自身的 AI 系统。但是，它要求每次自我修改之前，必须通过形式化证明，确保修改对系统效用是有益的。

DGM 放宽了哥德尔机中不切实际的要求（主要是通过形式化证明确保修改对系统效用是有益的），即从理论上证明某项修改能够改进系统，转向要求通过实验获得**经验性证据（Empirical Evidence）**，以证明提出的新版本能够提升性能。

此外，由于 DGM 依赖经验性证据来判断改进效果，因此它可能在庞大的系统搜索空间（即所有可计算算法）中陷入局部最优。为了解决这一问题，DGM 在搜索过程中维护一个由已发现解决方案组成的档案库（Archive），从而实现开放式探索（Open-ended Exploration），而不是仅依赖单个解决方案的持续演化。

### **Self-referential Self-improvement of Coding Agents（coding agent的自指式自我改进）**

### **Population-based Open-ended Exploration（基于种群的开放式探索）**

DGM 从单个初始编程智能体开始，逐步构建一个包含所有已发现智能体的档案库（Archive）。在每次迭代中，DGM 从档案库（Archive）中选择父代智能体（Parent Agents），使其进行自我修改，并通过分支生成新的智能体。

父代智能体的选择概率大致：

- 与该智能体的性能分数成正比；
- 与其已生成的、具备代码库编辑能力的子代数量成反比（Appendix C.2）。

这种设计倾向于选择性能较高、但尚未得到充分探索的智能体（即现有子代较少的智能体），从而同时促进：

**Exploitation（利用）：** 充分利用表现优秀的智能体。

**Exploration（探索）：** 探索具有潜力但较少被采样的演化分支。

每一个被选中的父代智能体都会分析自身在benchmark 评测中的评估日志，提出下一项需要实现的功能，并将该提议作为一个需要执行的问题描述（Problem Statement）（Appendix C.3）。随后，父代智能体将所建议的功能实现到自身代码库中，从而生成一个新的编程智能体（为子代智能体）。**只有能够成功编译，并且仍然具备编辑给定代码库能力的子代智能体，才会被加入 DGM 的档案库（Archive）**

#### Appendix C.2 Parent Selection：

在 DGM 的每次迭代中，我们从档案库（Archive）中选择 $k$ 个智能体作为父代，使其进行自我修改生成新的子代智能体。

每个父代智能体 $a_i^t$ 的权重：

$$
w_i=
\frac{\operatorname{Sigmoid}(\exp(-\lambda(\alpha_i-\alpha_0))}
{1+n_i}
    =
\underbrace{
\frac{1}{1+\exp(-\lambda(\alpha_i-\alpha_0))}
}_{\text{Exploitation}}
\cdot
\underbrace{
\frac{1}{1+n_i}
}_{\text{Exploration}}
$$

其中：

- $\alpha_i$ 表示智能体 $a_i^t$在 Benchmark 上的性能分数
- $n_i$ 表示智能体 $a_i^t$已经生成的仍具备代码库编辑功能的子代数量。
- 本文 $\lambda=10,\qquad \alpha_0=0.5$

整个父代选择机制可以简化为：

- **Exploitation：** $\alpha_i\uparrow\Rightarrow w_i\uparrow$，优先探索高性能 Agent。
- **Exploration：** $n_i\uparrow\Rightarrow w_i\downarrow$，避免过度探索同一个 Agent。
- **Open-endedness：** 对所有符合条件的 Agent，均有 $p_i>0$，保留潜在的演化路径。

根据父代选取概率，sampling k次（SWE-bench 取 k=2，Polyglot 取 k=4。），父代选取概率为：

$$
p_i=\frac{w_i}{\sum_{j=1}^{N}w_j}
$$

#### **Appendix C.3 Self-Improve Prompts**：

整个过程分为两个阶段：**Diagnosis（诊断）和 Implementation（实现）。**

**1. Diagnosis：诊断改进方向**

DGM 使用一个独立的基础模型（OpenAI o1）分析父代 Agent 的失败日志，并提出改进方案。

**Self-improvement 粒度**：从失败任务集合中随机选择一个任务，提出一个改进方案，修改自身代码，生成一个新的 Agent。（就从这么多的失败例子之中选取一个任务呀进行改进agent，是不是一个改进点？）

**输入信息（Input）**

| 变量              | 内容                           |
| ----------------- | ------------------------------ |
| `code`            | 当前 Agent 的完整代码实现      |
| `md_log`          | Agent 解决任务失败时的运行日志 |
| `github_issue`    | 原始编程任务描述               |
| `predicted_patch` | Agent 生成的代码补丁           |
| `test_patch`      | SWE-bench 官方隐藏测试代码     |
| `eval_log`        | 官方测试的执行结果             |

除此之外，Prompt 还提供了 Agent 的代码结构说明，包括 `coding_agent.py`、`forward()`、`tools/` 和 `utils/` 等。

**输出信息（Output）**

| 字段                        | 作用                                          |
| --------------------------- | --------------------------------------------- |
| `log_summarization`         | 总结 Agent 的求解过程、工具使用情况及失败原因 |
| `potential_improvements`    | 提出多个潜在改进方向                          |
| `improvement_proposal`      | 选择**一个**高价值的改进方向                  |
| `implementation_suggestion` | 给出具体的功能实现建议                        |
| `problem_description`       | 将改进方案组织成 GitHub Issue 的形式          |

其中，作者特别强调：改进方案必须提升 Agent 的**通用编程能力**，而不是针对某个具体 GitHub Issue 或代码仓库进行优化。

**2. Implementation：执行自我修改**

从 o1 的输出中提取两个字段：

- `implementation_suggestion`
- `problem_description`

将它们作为新的编程任务交给父代 Agent，由父代 Agent 修改自身代码库，生成子代 Agent。

## **Experiments**

### **Experiment Setup 和 **Benchmarks

初始 Agent 可以使用两种工具：

- **Bash Tool**：执行 Bash 命令。
- **Edit Tool**：查看目录以及查看、编辑完整文件（Appendix C.1）。

智能体接收一个输入 Prompt，其中指定目标代码库的位置、问题描述以及代码库的测试框架（Appendix C.1）。**我们有意将初始智能体设计得十分轻量，以便提供一个简单的起点，从而清晰地展示自我改进的效果。**

| 实验配置                | SWE-bench               | Polyglot                |
| ----------------------- | ----------------------- | ----------------------- |
| 初始 Agent 工具         | Bash + Edit             | Bash + Edit             |
| Self-modification Model | Claude 3.5 Sonnet (New) | Claude 3.5 Sonnet (New) |
| Evaluation Model        | Claude 3.5 Sonnet (New) | o3-mini                 |
| 总迭代次数              | 80                      | 80                      |

**SWE-bench**：默认指 SWE-bench Verified，只有 python。

**Polyglot**：多种编程语言的任务，例如 C++、Rust 和 Python 等

**Staged Evaluation Strategy（分阶段评估策略）**

由于在完整 Benchmark 上进行评测的成本很高（Appendix E.1），我们采用分阶段评估策略来估计编程智能体的性能。

**SWE-bench：**

| 阶段    | 任务数量      | 目的及进入条件                          |
| ------- | ------------- | --------------------------------------- |
| Stage 1 | 10            | 验证 Agent 的基本代码编辑能力           |
| Stage 2 | 60（10+50）   | 通过基本功能检查后，估计整体编程能力    |
| Stage 3 | 200（60+140） | 成功率超过 40%，且在 Archive 中排名前二 |

**Polyglot：**

| 阶段    | 任务数量 | 目的及进入条件      |
| ------- | -------- | ------------------- |
| Stage 1 | 10       | 初步评估 Agent 性能 |
| Stage 2 | 50       | 初始成功率超过 40%  |

### baseline

| 方法                       | 修改者         | 被修改对象          | Archive |
| -------------------------- | -------------- | ------------------- | ------- |
| DGM                        | 父代 Agent     | 父代自身            | 保留    |
| w/o Self-improve（ADAS）   | 固定初始 Agent | 采样的父代 Agent    | 保留    |
| w/o Open-ended Exploration | 最新有效 Agent | 最新有效 Agent 自身 | 不保留  |

用公式表示：

$$
\begin{aligned}
\text{DGM:}\quad
&c=p.\operatorname{modify}(p)\\
\text{w/o Self-improve:}\quad
&c=g_0.\operatorname{modify}(p)\\
\text{w/o Open-ended:}\quad
&c=g_{\mathrm{latest}}.\operatorname{modify}
(g_{\mathrm{latest}})
\end{aligned}
$$

其中， $p$ 为采样得到的父代 Agent， $g_0$ 为初始 Agent。

### **Results**

![image-20261127115844059](./Image/DGM%EF%BC%88ICLR%202026%EF%BC%89/image-20261127115844059.png)

![image-20261127123140540](./Image/DGM%EF%BC%88ICLR%202026%EF%BC%89/image-20261127123140540.png)

figure2：在 Benchmark 子集上面迭代的效果

![image-20261127121322274](./Image/DGM%EF%BC%88ICLR%202026%EF%BC%89/image-20261127121322274.png)

figure3：DGM Archive Tree 在 Staged Evaluation Strategy 的搜索进化图

![image-20261127122049657](./Image/DGM%EF%BC%88ICLR%202026%EF%BC%89/image-20261127122049657.png)

### **Generalization（泛化能力）**

**① Model Transfer（跨模型迁移）** 图一： 不同的 FMs 上面 DMG的表现成果

**② Benchmark Transfer（跨基准测试迁移）** 图二：将在一个 Benchmark 上发现的最佳 DGM agent，放到另一个完全未参与优化的 Benchmark 上进行评估

**③ Task Transfer（跨编程语言迁移）** 图三：开展两种 DGM任务 1. 仅使用 Polyglot 中的 Python 任务 2.使用 Polyglot 中所有编程语言任务。这表明DGM改进并没有过拟合到某一种特定的编程语言。

## 成本（附录 E.1）

| 项目 | 成本 |
|---|---|
| **SWE-bench 上完整跑一次 DGM**（80 轮） | **约 22,000 美元**，耗时约 **2 周** |
| 两个 baseline（去掉自我改进 / 去掉开放式探索）各跑一次 | 各约 10,000 美元 |
| 单个 agent 在 SWE-bench 上评测 60 题（Claude 3.5 Sonnet） | 约 **350 美元** |
| 单个 agent 在 Polyglot 上评测 60 题（o3-mini） | 约 **5 美元** |
