# S05 共享线性机制模型进度

## 快照

- 阶段状态：`pending`
- 当前任务：`S05.T00 共享线性机制与数值模块调研`
- 最近完成：`none`
- 下一任务：S03 fallback 与 S04 graph release 获批后先执行 S05.T00
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称阶段完成：`否`

## 任务状态

- S05.T00 共享线性机制与数值模块调研：`pending`
- S05.T01 线性模型接口与参数化：`pending`
- S05.T02 损失与 condition weighting：`pending`
- S05.T03 解析/合成数值测试：`pending`
- S05.T04 单 fold dry run 与资源边界：`pending`
- S05.T05 全 LOCO/LOPO/双留出运行：`pending`
- S05.T06 State/共享性反事实诊断：`pending`
- S05.T07 与 S03 基线的预注册比较：`pending`
- S05.T08 原因分析与科学闸门：`pending`
- S05.A.R / S05.B.R：`pending`

## 已实现与已验证

- 已实现：完成本阶段文档初始化，并扩写 design.md 的共享线性模型、soft intervention、训练诊断和科学闸门说明；未实现或运行模型。
- 已验证：无模型、测试、dry run、真实 fold 或科学 gate 证据。

## 依赖与声明边界

- 依赖：S00–S04 获批，且 S01/S03 比较合同已冻结。
- 允许声明：核心假设的最小工程检验已定义。
- 禁止声明：共享机制成立、线性/ODE 有增益、任何 GRN 边成立或 S06 已获授权。
- 科学状态：`not_tested`。

## 合同修订

- 2026-08-31：新增 S05.T00；PerturbODE/线性系统/训练模块需按 frozen interfaces 实际复核，核心模型或 solver 自建需例外批准。
- 2026-09-03：统一 `G_predictive`、`rho_source` 与 S02 E1/E2 evidence 接口；明确 evidence weights 与 bridge/anchor regularization 的职责分离、联合消融和 inner-fold gamma 规则；模型仍未实现。

## 恢复与阶段复核

- 恢复点：上游获批后从 S05.T00 开始；reuse gate 通过后执行 S05.T01，先 dry run 后申请长时执行。
- review_performed：`no`
- review_result：`pending`
- evidence：`none`
- mismatch_or_blocker：上游与人工 gate 未完成
- next_correction：等待 S03/S04 phase gates
- continuation_status：`phase_review`
- user_confirmation_required_now：`no`
