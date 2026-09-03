# S01 官方契约与泄漏安全评测进度

## 快照

- 阶段状态：`pending`
- 当前任务：`S01.T00 评测、切分与提交工具调研`
- 最近完成：`none`
- 下一任务：S00 数据发布版获批后，先执行 S01.T00 现场工具/方法调研
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称阶段完成：`否`

## 任务状态

- S01.T00 评测、切分与提交工具调研：`pending`
- S01.T01 官方规则快照与冲突登记：`pending`
- S01.T02 官方 bundle 只读 schema 核验：`pending`
- S01.T03 Submission contract fixture：`pending`
- S01.T04 Fold registry 与拟合边界：`pending`
- S01.T05 六指标 harness 与辅助指标：`pending`
- S01.T06 模型晋级比较协议：`pending`
- S01.T07 泄漏与不变量测试：`pending`
- S01.A.R / S01.B.R：`pending`

## 已实现与已验证

- 已实现：完成本阶段文档初始化，并扩写 design.md 的官方合同、OOD 切分、指标、提交 fixture 和人工闸门说明；未实现评测代码或本地合同产物。
- 已验证：2026-08-30 官方公开信息已形成独立研究快照，但尚未形成执行时锁版合同。
- 证据：`参考资料库/竞赛资料/2026_VCC_官方信息核验_2026-08-30.md`
- 未验证：用户本地 official bundle、CLI dry-run、scorer、fold、leakage guards。

## 合同修订

- 2026-08-31：新增 S01.T00；必须按执行当日官方 bundle/CLI/scorer 和当前方法重新调研，通过 reuse gate 后才可执行 S01.T01+。
- 2026-09-03：增加同-study LOCO、同-cell-model 跨-study、leave-one-study-out 和 leave-one-lineage-out 的条件式原因定位拆分；主 LOCO、官方合同和执行状态不变。

## Blocker 与声明边界

- 依赖：S00 已批准 release；用户提供/确认 official validation bundle。
- 允许声明：当前官方规则研究快照存在，并识别 U-001/U-002 等冲突。
- 禁止声明：提交 schema 已在本机通过、六指标已复现、fold 无泄漏或模型可参赛。

## 恢复与阶段复核

- 恢复点：完成 S00 后，从 S01.T00 开始，不能直接复用 R00 或 2026-08-30 快照。
- review_performed：`no`
- review_result：`pending`
- evidence：`none`
- mismatch_or_blocker：上游阶段与本地 bundle 尚未就绪
- next_correction：等待 S00 phase gate
- continuation_status：`phase_review`
- user_confirmation_required_now：`no`
