# R00 文献、已有研究与可复用实现基线进度

## 快照

- 阶段状态：`pending`
- 当前任务：`R00.T01 项目与参考资料 inventory`
- 最近完成：`none`
- 下一任务：规划修订获用户复核后执行本地只读 inventory
- 更新时间：`2026-09-03 14:11:09 CST`
- 可否宣称阶段完成：`否`
- 原因：当前仅建立治理规则和执行合同，尚未执行实际 R00 调研、registry 或校验器实现

## 任务状态

- R00.T01 项目与参考资料 inventory：`pending`
- R00.T02 本地依赖与 stage capability map：`pending`
- R00.T03 当前文献与官方实现调研：`pending`
- R00.T04 新方法归档与既有方法卡补全：`pending`
- R00.T05 复用 registry 与阶段 T00 最低合同：`pending`
- R00.T06 复用 gate 校验器：`pending`
- R00.T07 R00 发布冻结与人工闸门：`pending`
- R00.A.R / R00.B.R / R00.C.R：`pending`

## 已实现与已验证

- 已实现：R00 design/plan/progress 文档及项目级复用治理规则/模板。
- 已验证：尚未验证项目/环境 inventory、实时文献、method cards、registry 或 gate checker。

## 声明边界

- 允许声明：R00 与 stage T00 的职责、产物和 gate 已形成持久合同。
- 禁止声明：当前 registry 已建立、所有成熟方法已覆盖、任何依赖/版本/许可已核验、任何 stage 已通过 reuse gate。
- 科学状态：所有模型/方法适配性仍为 `not_tested`。

## 合同修订

- 2026-08-31：新增 R00 项目级复用基线，并规定它不能替代 S00–S08 实际开工时的 T00 调研。
- 2026-09-03：因果审阅将 E1-E4、hierarchical effect synthesis、covariate timing/role、guide-IV 可行性、biology/protocol split、predictive graph/evidence separation 与 bridge/anchor robustness 纳入 R00 capability inventory；实际 T00 调研仍未执行。

## 恢复与阶段复核

- 恢复点：先读取根 AGENTS、根 plan/progress、`参考资料库/rules.md` 和本阶段三份文档，再从 R00.T01 开始。
- review_performed：`no`
- review_result：`pending`
- evidence：`none`
- mismatch_or_blocker：无执行证据；等待规划修订复核
- next_correction：执行 R00.T01 本地 inventory
- continuation_status：`phase_review`
- user_confirmation_required_now：`yes`
