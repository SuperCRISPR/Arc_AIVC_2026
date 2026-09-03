# S02 表征与跨环境证据进度

## 快照

- 阶段状态：`pending`
- 当前任务：`S02.T00 表征、匹配与因果证据方法调研`
- 最近完成：`none`
- 下一任务：S01 evaluation contract 获批后先执行 S02.T00
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称阶段完成：`否`

## 任务状态

- S02.T00 表征、匹配与因果证据方法调研：`pending`
- S02.T01 Feature universe 与 normalization 合同：`pending`
- S02.T02 Program basis 基线：`pending`
- S02.T03 NTC state 摘要：`pending`
- S02.T04 Matched NTC condition effects：`pending`
- S02.T05 Overlap 连接图与可运输性审计：`pending`
- S02.T06 稳定平衡权重：`pending`
- S02.T07 可选双重稳健 effect summary：`pending`
- S02.T08 Evidence store 与发布冻结：`pending`
- S02.A.R / S02.B.R：`pending`

## 已实现与已验证

- 已实现：完成本阶段文档初始化，并扩写 design.md 的 program 表征、matched NTC、overlap、权重和 evidence store 说明；未实现阶段代码或真实证据产物。
- 已验证：无真实 representation、matched NTC、overlap、weight 或 evidence artifact。
- 未验证：所有代码、统计假设、真实数据支持域和内存路径。

## 依赖与声明边界

- 依赖：S00 data release 与 S01 fold/evaluation contract。
- 允许声明：证据生成合同已定义。
- 禁止声明：当前数据存在充分 overlap、batch 偏差已消除、共享机制被支持或任何因果边成立。
- 科学状态：`not_tested`。

## 合同修订

- 2026-08-31：新增 S02.T00；必须基于真实 data/fold interface 现场刷新表征、matching、weighting 与稳健估计候选。
- 2026-09-03：增加双 DAG、E1-E4 estimand contract、covariate-role/selection/interference manifest；将 hierarchical effect synthesis 提升为默认跨环境汇总，weighting/DR/guide-IV/proximal/sensitivity 保持条件触发；evidence schema 增加异质性、prediction interval 和校准来源。

## 恢复与阶段复核

- 恢复点：上游获批后从 S02.T00 开始；reuse gate 通过后才进入 S02.T01，且不复用跨 fold fitted basis。
- review_performed：`no`
- review_result：`pending`
- evidence：`none`
- mismatch_or_blocker：上游未完成
- next_correction：等待 S01 phase gate
- continuation_status：`phase_review`
- user_confirmation_required_now：`no`
