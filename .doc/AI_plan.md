# 项目级 AI 执行总计划

本文仅负责阶段路由、依赖与人工闸门。详细工程任务、产物和验证方法只在对应阶段的 `.doc/AI_plan.md` 中定义。

## 总目标与边界

- 目标：先建立可审计的方法调研与复用基线，再从用户手动拷贝已获取数据之后开展数据、评测、基线、共享机制建模和竞赛提交工程。
- 范围：`stages/R00_method_reuse_baseline` 与 `stages/00_data_acceptance_qc` 至 `stages/08_competition_delivery`。
- 范围外：数据集调研、审批、下载或远程补齐；未经授权的外部提交；使用隐藏 perturbation response 做调参或人工修订。
- 外部前置：R00 不依赖训练数据拷贝；S00 及其后续数据任务以用户将批准的数据资产完整拷贝到 `Stage_0-raw_training_data/` 为前置。
- 防御等级：`L3`。
- 执行策略：`phase_boundary`。

## 阶段路由

| 阶段 | 模块 | 详细合同 | 进入下一阶段的人工闸门 |
| --- | --- | --- | --- |
| R00 | 文献、已有研究、可复用模型与实现基线 | [`stages/R00_method_reuse_baseline/.doc/AI_plan.md`](../stages/R00_method_reuse_baseline/.doc/AI_plan.md) | 批准复用 registry、阶段能力映射和治理校验合同 |
| S00 | 数据接收、标准化、QC、筛选、UMAP 与人工评估 | [`stages/00_data_acceptance_qc/.doc/AI_plan.md`](../stages/00_data_acceptance_qc/.doc/AI_plan.md) | 批准数据发布版及隔离清单 |
| S01 | 官方契约、评测指标与泄漏安全 | [`stages/01_evaluation_contract/.doc/AI_plan.md`](../stages/01_evaluation_contract/.doc/AI_plan.md) | 批准冻结的评测/提交合同 |
| S02 | fold 内表征、E1-E3 证据与 E4 接口 | [`stages/02_representation_evidence/.doc/AI_plan.md`](../stages/02_representation_evidence/.doc/AI_plan.md) | 批准可用证据域及不可运输域 |
| S03 | 强基线、层级效应与保底推理路径 | [`stages/03_baselines/.doc/AI_plan.md`](../stages/03_baselines/.doc/AI_plan.md) | 批准基线下限和晋级比较协议 |
| S04 | 候选图、干预证据与预测耦合输入 | [`stages/04_candidate_grn/.doc/AI_plan.md`](../stages/04_candidate_grn/.doc/AI_plan.md) | 批准候选图版本；不得升级为因果结论 |
| S05 | 共享预测耦合线性模型 | [`stages/05_shared_linear_model/.doc/AI_plan.md`](../stages/05_shared_linear_model/.doc/AI_plan.md) | 判断共享有限时间预测结构的证据状态与后续路线 |
| S06 | 共享非线性预测 ODE | [`stages/06_shared_nonlinear_model/.doc/AI_plan.md`](../stages/06_shared_nonlinear_model/.doc/AI_plan.md) | 判断非线性/连续结构是否保留 |
| S07 | 有门控的 E4 预测扩展 | [`stages/07_gated_extensions/.doc/AI_plan.md`](../stages/07_gated_extensions/.doc/AI_plan.md) | 每一扩展分支单独批准或保持关闭 |
| S08 | E4 验证/终测推理、格式验证与提交 | [`stages/08_competition_delivery/.doc/AI_plan.md`](../stages/08_competition_delivery/.doc/AI_plan.md) | 每次外部提交前批准；最终归档复核 |

## 固定转移规则

1. 每阶段必须由该阶段进度文件记录直接文件/产物审阅；仅有计划、脚手架或测试替身不能晋级。
2. 根进度文件只在当前阶段/模块、阶段状态或下一阶段发生变化时更新。
3. R00 必须建立项目级复用基线；R00 的 registry 和静态方法卡只提供起点，不能替代 S00–S08 各阶段实际启动时的最新调研。
4. S00–S08 每个阶段必须先完成该阶段 `T00` 方法调研与复用决策；`reports/reuse_decision.yaml` 未达到 `reuse_gate: pass` 时，不得进入该阶段 `T01+` 的实现、依赖引入或真实运行。
5. 任何新文献、方法、模型、科学软件或外部实现一旦进入候选比较、影响设计或准备复用，必须先按 [`参考资料库/rules.md`](../参考资料库/rules.md) 归档、索引并核验官方来源、当前版本和许可。
6. 选择 `minimal_new` 最小自建实现时，必须记录已评估的成熟候选、不适配证据、最小自建范围与替换条件；涉及模型、核心算法、scorer、solver 或数据转换的自建例外需要人工批准。
7. S00 数据发布版未获人工批准前，不进入 S01 之后的真实数据拟合。
8. S01 评测合同未冻结前，不比较或选择模型。
9. S03 必须先形成可复现的保底路径；S04 以后任何失败不得破坏该路径。
10. S06 只有在 S05 的预注册比较允许时进入；S07 的各分支只有对应触发条件成立时进入。
11. S08 可使用 S03、S05、S06 或经批准的 S07 模型；按冻结的内部 OOD 证据选择，不按模型复杂度选择。
12. S02-S08 必须沿用 E1-E4 的语义边界：E1-E3 evidence/calibration/transport 不能被 E4 预测结果反向改写；E4 可在 E2/E3 不可识别时使用获批 fallback，但必须保留该状态。

## 强制人工闸门

- R00 复用基线、阶段能力映射和治理校验合同的首次冻结。
- 拒绝可用成熟实现而选择自建核心模型、算法、scorer、solver、数据转换器或等价科学模块。
- 用户数据尚未拷贝、拷贝范围不明或 manifest 不一致。
- S00 UMAP/人工诊断后的 accept/quarantine/reprocess 决策。
- 官方规则与本地合同冲突且会改变提交或评分解释。
- 共享机制、非线性结构、知识先验、adapter/MoE 的路线晋级。
- E1-E3 估计对象、层级效应汇总、guide-IV、bridge/anchor 语义或 `G_predictive`/干预证据对象的持久变更。
- 任何破坏性操作、长时高资源真实训练、受限许可证资产使用或外部提交。
- 科学结论需要由项目专家裁决，或执行发现需修改持久计划合同。

## 总体验收

- 根进度与各阶段进度可从真实文件恢复当前状态，无重复或矛盾的任务叙述。
- 每个已执行阶段均有实际开工时生成的调研报告和复用决策；不存在只引用静态文献清单、却没有现场版本/许可/适配性复核的科学实现。
- 每个进入代码、配置或实验的外部方法都能追溯到参考资料库方法卡、官方来源、版本/commit、许可、复用 interface 和 stage-owned decision ID。
- 至少一条模型路径具有冻结的数据发布版、fold、配置、种子、代码/环境标识、内部 OOD 报告和官方 dry-run 证据。
- 最终交付满足当时官方 schema，并保存模型生成链与提交记录。
- 未完成、未验证、未启用或科学上未解决的部分保持显式状态，不因比赛提交成功而自动升级。
- 任何直接调控边主张都保持 `unresolved`，除非另行登记独立的时间、组合扰动、rescue 或正交证据。

## 计划变更规则

仅在范围、依赖、产物路径、完成闸门、验证方法、禁止事项或任务分解发生持久变化时修改本文件；实际结果、指标、时间、命令输出和恢复点只写入进度、日志或阶段证据。

<!-- File structure: project routing, cross-stage dependencies, human gates, acceptance criteria and durable change rules. -->
