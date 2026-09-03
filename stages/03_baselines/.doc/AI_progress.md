# S03 强基线与保底路径进度

## 快照

- 阶段状态：`pending`
- 当前任务：`S03.T00 基线与单细胞 count generator 调研`
- 最近完成：`none`
- 下一任务：S02 evidence release 获批后先执行 S03.T00
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称阶段完成：`否`

## 任务状态

- S03.T00 基线与单细胞 count generator 调研：`pending`
- S03.T01 B0 No-change：`pending`
- S03.T02 B1 Matched/source mean delta：`pending`
- S03.T03 B2 Ridge/elastic-net：`pending`
- S03.T04 B3 Reduced-rank program linear：`pending`
- S03.T05 通用 raw-count cell generator：`pending`
- S03.T06 全 fold 评测与稳定性：`pending`
- S03.T07 冻结保底模型与晋级协议数值化：`pending`
- S03.A.R / S03.B.R：`pending`

## 已实现与已验证

- 已实现：完成本阶段文档初始化，并扩写 design.md 的基线阶梯、raw-count generator、公平比较和 fallback 说明；未实现任何 baseline 或预测产物。
- 已验证：无 baseline、count generator、fold result 或 fallback release。

## 依赖与声明边界

- 依赖：S00–S02 获批产物。
- 允许声明：baseline 阶梯和统一输出合同已定义。
- 禁止声明：存在可提交模型、简单模型已恢复信号、任何复杂模型需要或不需要。
- 科学状态：`not_tested`。

## 合同修订

- 2026-08-31：新增 S03.T00；强基线和 count generator 必须先复核成熟实现、版本、接口与输出语义。
- 2026-09-03：增加 B2H hierarchical conditional-effect baseline，并纳入 biology/protocol 辅助拆分；B0-B3、generator、fallback 仍未实现或运行。

## 恢复与阶段复核

- 恢复点：上游获批后从 S03.T00 开始；reuse gate 通过后再运行 S03.T01 的单 fold dry run。
- review_performed：`no`
- review_result：`pending`
- evidence：`none`
- mismatch_or_blocker：上游未完成
- next_correction：等待 S02 phase gate
- continuation_status：`phase_review`
- user_confirmation_required_now：`no`
