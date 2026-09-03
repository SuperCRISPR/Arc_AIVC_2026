# S00 数据接收、QC 与人工诊断进度

## 快照

- 阶段状态：`blocked`
- 当前任务：`S00.T00 数据工程方法调研与复用决策`
- 最近完成：`none`
- 下一任务：R00 获批且用户完成数据拷贝后，结合真实输入执行 S00.T00
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称阶段完成：`否`
- 原因：当前仓库没有上游数据副本，所有数据接收、转换、QC 和视觉评审均未执行

## 任务状态

- S00.T00 数据工程方法调研与复用决策：`blocked`，等待 R00 release 与用户手动拷贝
- S00.T01 接收核验：`blocked`，等待用户手动拷贝
- S00.T02 数据卡与字段字典对齐：`pending`
- S00.T03 Canonical 转换器与小样本 dry run：`pending`
- S00.T04 数据清洗、QC flags 与 guide/condition 诊断：`pending`
- S00.T05 训练资格筛选、分流与基因规范化：`pending`
- S00.T06 冻结诊断视图：`pending`
- S00.T07 人工诊断闸门：`pending`
- S00.T08 发布冻结：`pending`
- S00.A.R / S00.B.R / S00.C.R：`pending`

## 已实现与已验证

- 已实现：完成本阶段 design/plan/progress 初始化，并扩写 design.md 的输入输出、核心方法、人工判断和完成含义；未实现阶段代码或数据产物。
- 已验证：未验证任何数据、代码或真实产物。
- 未验证：上游 manifest、checksum、可读性、schema、转换、QC、UMAP、人工决定和 release。

## Blocker 与声明边界

- Blocker：`Stage_0-raw_training_data/` 尚未由用户拷贝到当前项目。
- Blocker：R00 尚未执行，S00 没有实际 `method_reuse_review` 或 `reuse_decision`。
- 允许声明：S00 工程合同已初始化。
- 禁止声明：任何数据集已收到、完整、合规、可训练或通过 QC；科学假设状态为 `not_tested`。

## 恢复点

1. 读取根 AGENTS/plan/progress、复用治理规则和本阶段三份文档。
2. 确认 R00 release 已批准且输入根存在；任一缺失时保持 blocked，不下载。
3. 先执行 S00.T00 并取得 `reuse_gate: pass`，再执行 S00.T01。

## 合同修订

- 2026-08-31：新增 S00.T00；静态 R00/方法卡不能替代真实输入到位后的现场格式、QC、可视化与 adapter 调研。
- 2026-09-03：补充 field dictionary 的 `measurement_timing` 与 `covariate_role`，登记 assignment/selection/interference 元数据；KD 后 QC 等字段默认不获准作为普通混杂调整变量。真实数据仍未接收。

## 阶段复核

- review_performed：`no`
- review_result：`pending`
- evidence：`none`
- mismatch_or_blocker：R00 未执行且输入尚未拷贝
- next_correction：完成 R00 和数据拷贝后执行 S00.T00
- continuation_status：`approval_required`
- user_confirmation_required_now：`yes`
