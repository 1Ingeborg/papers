# Agent 自我进化论文清单（按进化对象分类）

> 来源：小红书帖子「Agent自我进化论文汇总了，求推荐和避雷论文」及其评论区（评论时间 09-06 ~ 10-04）。整理于 2026-11-08。
> 「综述」指 *From Existing Methods to New Research Ideas: Two Perspectives on Recursive Self-Improvement*，编号为该综述的引用序号；「—」表示不在综述 Table 2 中。
> 截图 1 的评论区分辨率过低（136px 宽），未能转录，下表只含正文与截图 2 的评论。

## 一、楼主的分类框架

| 范式 | 简称 | 论文 | 链接 | 综述 |
|---|---|---|---|---|
| Skill evolution | SkillOpt | SkillOpt: Executive Strategy for Self-Evolving Agent Skills | [arXiv:2605.23904](https://arxiv.org/abs/2605.23904) | — |
| Policy evolution | R-Zero | R-Zero: Self-Evolving Reasoning LLM from Zero Data | [arXiv:2508.05004](https://arxiv.org/abs/2508.05004) | [133] |
| Memory evolution | MemEvolve | MemEvolve: Meta-Evolution of Agent Memory Systems | [arXiv:2512.18746](https://arxiv.org/abs/2512.18746) | — |
| Policy + Skill co-evolution | SkillRL | SkillRL: Evolving Agents via Recursive Skill-Augmented Reinforcement Learning | [arXiv:2602.08234](https://arxiv.org/abs/2602.08234) | — |
| Whole harness evolution | Continual Harness | Continual Harness: Online Adaptation for Self-Improving Foundation Agents | [arXiv:2605.09998](https://arxiv.org/abs/2605.09998) | [87] |
| Environment evolution | EnvHarness | EnvHarness: Awakening Static Worlds for Agent Learning | [arXiv:2608.19880](https://arxiv.org/abs/2608.19880) | [95] |
| Recursive self-improvement | Hyperagents | Hyperagents | [arXiv:2603.19461](https://arxiv.org/abs/2603.19461) | [17] |
| Multi-agent evolution | CORAL | CORAL: Towards Autonomous Multi-Agent Evolution for Open-Ended Discovery | [arXiv:2604.01658](https://arxiv.org/abs/2604.01658) | — |

### 楼主补充（入门基础）

| 范式 | 简称 | 论文 | 链接 | 综述 | 备注 |
|---|---|---|---|---|---|
| Optimization paradigm | GEPA | GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning | [arXiv:2507.19457](https://arxiv.org/abs/2507.19457) | [18] | 楼主：许多 self-evolution 论文建立在 GEPA 式反思文本优化上，只是优化对象不同；推荐作第一篇入门 |
| GEPA 的前身 | TextGrad | TextGrad: Automatic Differentiation via Text | [arXiv:2406.07496](https://arxiv.org/abs/2406.07496) | [9] | 文本反馈反向传播 |
| Context evolution | ACE | Agentic Context Engineering: Evolving Contexts for Self-Improving Language Models | [arXiv:2510.04618](https://arxiv.org/abs/2510.04618) | [15] | — |

## 二、评论区推荐

| # | 简称 | 论文 | 链接 | 综述 | 推荐人 / 备注 |
|---|---|---|---|---|---|
| 1 | HarnessForge | HarnessForge: Joint Harness and Policy Evolution for Adaptive Agent Systems | [arXiv:2606.01779](https://arxiv.org/abs/2606.01779) | — | 五道口夜神：「Harness+model coevolution」 |
| 2 | MCE | Meta Context Engineering via Agentic Skill Evolution | [arXiv:2601.21557](https://arxiv.org/abs/2601.21557) | [118] | 麦克斯韦妖：context engineering 方向觉得 MCE 比 ACE 好 |
| 3 | Cogito, Ergo Ludo | Cogito, Ergo Ludo: An Agent that Learns to Play by Reasoning and Planning | [arXiv:2509.25052](https://arxiv.org/abs/2509.25052) | — | momo；反思得到规则库与策略手册 + GRPO |
| 4 | EnvHarness | （同上） | [arXiv:2608.19880](https://arxiv.org/abs/2608.19880) | [95] | momo（疑似作者）再次推荐，称为 environment evolution |
| 5 | GEA | Group-Evolving Agents: Open-Ended Self-Improvement via Experience Sharing | [arXiv:2602.04837](https://arxiv.org/abs/2602.04837) | [105] | Jimmy（作者自荐）：以群体为单位对 harness 自我改进；楼主问归哪类，可归 multi-agent evolution |
| 6 | AntOmniEvo | Mara Chain: Rethinking Failure as a Stepping Stone for AI System Auto-Evolution | [arXiv:2609.35855](https://arxiv.org/abs/2609.35855) · [GitHub](https://github.com/ant-research/AntOmniEvo) | — | 土豆；标题取自 README，摘要页未核实 |
| 7 | EvoTest | EvoTest: Evolutionary Test-Time Learning for Self-Improving Agentic Systems | [arXiv:2510.13220](https://arxiv.org/abs/2510.13220) | — | momo：「我们组的」 |
| 8 | JitRL | Just-In-Time Reinforcement Learning: Continual Learning in LLM Agents Without Gradient Updates | [arXiv:2601.18510](https://arxiv.org/abs/2601.18510) | — | 同上 |
| 9 | Meta-TTL | Learning to Learn-at-Test-Time: Language Agents with Learnable Adaptation Policies | [arXiv:2604.00830](https://arxiv.org/abs/2604.00830) | — | 同上；评论写作 metattl |
| 10 | Agentic ESOpt | Agentic ESOpt: Fine-Tuning Long-Horizon LLM Agents with Minimal GPU Requirements | [arXiv:2608.17310](https://arxiv.org/abs/2608.17310) | — | Zheng Zhi（作者自荐）：高效参数进化 |

## 三、与预算分配方向相关

- 楼主的「进化对象」分类与综述的 Targets 维度（P/H/C/M/S/O）基本对应；Environment（O）与 Multi-agent（拓扑）是额外的两类。
- 明确做**多分支联合进化**的：SkillRL（policy + skill）、HarnessForge（harness + policy）、Agentic ESOpt（parameter + context）——需细读它们如何（或是否）在分支间分配预算。

## 四、待办

- 截图 1 评论区约 30 条未转录，需高清截图补充。
- 未收录：广告类评论（arXiv Agent 开源项目、Scholay）、无具体论文的讨论。
