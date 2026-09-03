# S04 候选 GRN 进度

## 快照

- 阶段状态：`pending`
- 当前任务：`S04.T00 GRN 方法、图工具与先验资源调研`
- 最近完成：`none`
- 下一任务：S02/S03 获批后先执行 S04.T00
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称阶段完成：`否`

## 任务状态

- S04.T00 GRN 方法、图工具与先验资源调研：`pending`
- S04.T01 节点宇宙与图层级：`pending`
- S04.T02 数据驱动候选生成：`pending`
- S04.T03 Perturbation edge evidence：`pending`
- S04.T04 稳定性选择与候选合并：`pending`
- S04.T05 外部先验 registry：`pending`
- S04.T06 可选 BackShift/多环境辅助分数：`pending`
- S04.T07 真实先验、随机先验与无图资产：`pending`
- S04.T08 Graph release 冻结：`pending`
- S04.A.R / S04.B.R：`pending`

## 已实现与已验证

- 已实现：完成本阶段文档初始化，并扩写 design.md 的三层图、候选生成、稳定性、先验和随机图对照说明；未生成任何候选图。
- 已验证：无 candidate graph、prior registry、stability 或消融资产。

## 依赖与声明边界

- 依赖：S00–S02 获批产物；S03 可并行但晋级比较依赖其 fallback release。
- 允许声明：候选图与机制图边界已定义。
- 禁止声明：任何边为因果、共享或真实调控关系；任何先验有预测增益。
- 科学状态：`not_tested`。

## 合同修订

- 2026-08-31：新增 S04.T00；GENIE3/GRNBoost2/SCENIC 等静态清单不等于已选实现，必须按实际 nodes/evidence 和当前许可现场复核。
- 2026-09-03：将 S04 输出明确拆为 candidate graph、interventional total-effect evidence、`G_predictive` 和解释图；增加异质性/等效性/时间支持字段和 ICP/JCI/BackShift 条件边界；未生成候选图。

## 恢复与阶段复核

- 恢复点：上游获批后从 S04.T00 开始；reuse gate 通过后执行 S04.T01，所有图按 fold 重建。
- review_performed：`no`
- review_result：`pending`
- evidence：`none`
- mismatch_or_blocker：上游未完成
- next_correction：等待 S02/S03 phase gates
- continuation_status：`phase_review`
- user_confirmation_required_now：`no`
