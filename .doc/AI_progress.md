# 项目级 AI 进度

本文只记录当前阶段/模块与阶段级恢复点；详细任务状态见对应模块进度文件。

## 当前快照

- 项目实现状态：`not_started`
- 当前阶段：`R00 / 文献、已有研究与可复用实现基线`
- 当前模块：`R00_method_reuse_baseline`
- 最近完成：`完成因果审阅驱动的跨阶段合同修订，保持所有执行阶段未启动`
- 下一动作：人工复核本次因果合同修订；批准后执行 `R00.T01 项目与参考资料 inventory`
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称项目完成：`否`
- 原因：当前只完成治理/计划修订，尚未执行 R00 实际调研、数据、模型或提交验证；训练数据仍未拷贝

## 阶段索引

| 阶段 | 状态 | 模块进度 |
| --- | --- | --- |
| R00 方法与复用基线 | `pending` | [`stages/R00_method_reuse_baseline/.doc/AI_progress.md`](../stages/R00_method_reuse_baseline/.doc/AI_progress.md) |
| S00 外部输入前置 | `blocked`：等待用户手动拷贝 `Stage_0-raw_training_data/` | 本文件 |
| S00 数据接收与 QC | `pending` | [`stages/00_data_acceptance_qc/.doc/AI_progress.md`](../stages/00_data_acceptance_qc/.doc/AI_progress.md) |
| S01 评测契约 | `pending` | [`stages/01_evaluation_contract/.doc/AI_progress.md`](../stages/01_evaluation_contract/.doc/AI_progress.md) |
| S02 表征、E1-E3 证据 | `pending` | [`stages/02_representation_evidence/.doc/AI_progress.md`](../stages/02_representation_evidence/.doc/AI_progress.md) |
| S03 强基线、层级效应 | `pending` | [`stages/03_baselines/.doc/AI_progress.md`](../stages/03_baselines/.doc/AI_progress.md) |
| S04 候选图与干预证据 | `pending` | [`stages/04_candidate_grn/.doc/AI_progress.md`](../stages/04_candidate_grn/.doc/AI_progress.md) |
| S05 共享预测耦合线性模型 | `pending` | [`stages/05_shared_linear_model/.doc/AI_progress.md`](../stages/05_shared_linear_model/.doc/AI_progress.md) |
| S06 共享非线性预测 ODE | `pending` | [`stages/06_shared_nonlinear_model/.doc/AI_progress.md`](../stages/06_shared_nonlinear_model/.doc/AI_progress.md) |
| S07 有门控的 E4 预测扩展 | `pending` | [`stages/07_gated_extensions/.doc/AI_progress.md`](../stages/07_gated_extensions/.doc/AI_progress.md) |
| S08 E4 竞赛交付 | `pending` | [`stages/08_competition_delivery/.doc/AI_progress.md`](../stages/08_competition_delivery/.doc/AI_progress.md) |

## 恢复说明

1. 从任意子目录恢复时，先读取根 `AGENTS.md`、本文件、根 plan 和 `参考资料库/rules.md`。
2. 读取 R00 design/plan/progress；规划修订获批后从 R00.T01 开始实际 inventory。
3. R00 release 获批后，确认 `Stage_0-raw_training_data/` 已由用户完成拷贝；不得由 agent 自行下载或补齐。
4. 每个 S00–S08 阶段先执行自己的 T00；复用 gate 未通过时不得进入 T01+。
5. 根文件只记录阶段路由；调研结果和小任务状态写入 R00/active-stage progress 与 reports。

## 计划修订记录

- 2026-08-31：新增 R00 项目级文献/已有研究/可复用实现基线；新增参考资料库治理规则与归档模板；S00–S08 各阶段新增必须现场执行的 T00 调研和 `reuse_gate`。执行影响：项目下一恢复点由等待数据改为先完成 R00；数据工程仍等待用户手动拷贝。
- 2026-09-03：根据因果矫正分析审阅修订 E1-E4 estimand、协变量角色、层级效应汇总、biology/protocol 辅助拆分、B2H 基线、干预证据与 `G_predictive` 分离、weight/bridge 联动及 E4 交付边界；未执行任何阶段、调研、数据或模型运行。

## 当前闸门

- 闸门类型：`因果审阅合同修订 phase review`
- 用户确认要求：`是`
- 当前允许的最强结论：因果证据与预测交付的持久合同已修订并写入阶段文件；项目科学假设仍为 `not_tested`
- 禁止结论：不得称 R00 registry/实时调研已完成、任何 stage 已通过 reuse gate、数据已接收、模型已实现、因果估计已运行或比赛路径已验证
- 可安全交接：`是`
