# S01 官方契约、评测指标与泄漏安全计划

## 目标与边界

- 目标：在模型训练前冻结可重放的官方提交合同、内部 OOD fold 与指标实现。
- 输入：S00 已批准 `release_id`；官方 validation control bundle（由用户提供/已授权本地资产）；[`2026 VCC 官方信息核验`](../../../参考资料库/竞赛资料/2026_VCC_官方信息核验_2026-08-30.md)。
- 输出：`stages/01_evaluation_contract/derived/<contract_id>/`、`reports/`、共享 evaluation 代码与测试。
- 范围外：用 validation leaderboard 选生物机制；下载未提供的 authenticated bundle；模型开发；外部提交。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [`2026 VCC 官方信息核验`](../../../参考资料库/竞赛资料/2026_VCC_官方信息核验_2026-08-30.md)：一手规则、六指标、schema、版本和冲突索引。
- [`scPerturBench 方法卡`](../../../参考资料库/文献/scPerturBench/方法卡.md)：context/perturbation generalization 与多指标评测边界。
- [`Linear Baselines 方法卡`](../../../参考资料库/文献/Linear-baselines/方法卡.md)：不可跳过的简单模型比较。

## Phase S01.0：实际开工调研与复用闸门

### S01.T00 评测、切分与提交工具调研

- 作用域前置：从任意子目录启动时先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)。
- 这是实际开工任务：基于当时 authenticated bundle、官方规则和当前 releases，重新调研项目存量、已安装工具、官方文档/仓库与相关方法；2026-08-30 快照、R00 和静态方法卡不能替代刷新。
- 最低 capabilities：`vcc-cli` submission validation、`cell-eval2 vcc2026`、group-safe LOCO/LOPO/双留出、同-study LOCO、同-cell-model 跨-study、leave-one-study-out、leave-one-lineage-out、nested/inner split、leakage guard、cluster-aware bootstrap、AnnData schema fixtures。
- 选择要求：官方 CLI/scorer 和成熟 split/metric implementation 优先；不得自写近似官方 scorer。若 wrapper/adapter 必要，interface 只负责版本锁定、输入规范化和结果 provenance。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`，含官方冲突、versions/commits/licenses、当前 bundle fit 与新增资料归档。
- 完成闸门：所有 capabilities 有 decision ID；官方工具版本和许可/使用方式明确；`reuse_gate: pass`；任何 scorer 或 fold engine 自建例外已人工批准。
- 禁止：直接沿用旧指标口径；gate 未通过时进入 S01.T01+。
- 检查点：更新本阶段 progress；通过后继续 S01.A。

## Phase S01.A：官方规则和文件合同

### S01.T01 官方规则快照与冲突登记

- 目标：把执行当日的 Rules、Data、Evaluation、FAQ、CLI、`cell-eval2` 版本和配置转成机器可追踪合同。
- 预期产物：`official_contract.yaml`、`source_snapshot.md`、`unresolved_official_conflicts.md`、版本/commit/config hash 记录。
- 必须记录：partition、contexts、panel ID、18,533 gene order、每 target cell 数、NTC 约束、library/entry 上限、CLI/scorer/rule version、anchor set 语义。
- 已知冲突 U-001：官网/CLI 的 exact 400/no NTC 与 metric spec 的 unconstrained/include NTC；操作上以当时 `vcc prep --dry-run` 和服务端验收为准，同时保留冲突并建议取得书面澄清。
- 已知冲突 U-002：文档参考 `cell-eval2 0.15.0/rule_version 3` 与后续 0.16.0；不得以“最新版”代替精确锁版。
- 完成闸门：所有当前官方约束都有一手来源、访问日期和机器字段；未解决项不会被静默推断。
- 禁止：猜测匿名 context 身份；恢复 v2 中无依据的 H1 禁用 gate。

### S01.T02 官方 bundle 只读 schema 核验

- 目标：对用户提供的 validation bundle 读取真实 manifest、gene list、target panel、context H5AD 与 obs/var schema。
- 预期产物：`bundle_inventory.tsv`、`bundle_schema.json`、`bundle_verification.md`。
- 检查：checksums、shape、raw-count、context labels、NTC IDs、gene symbols/order、panel membership、稀疏 dtype；与网页快照逐项 diff。
- 完成闸门：真实 bundle 与 `official_contract.yaml` 的一致/不一致项均记录；关键不一致触发人工闸门。
- 禁止：修改 A/B/C 标签或按表达聚类重命名；把控制 profiles 加入 S00 外部训练域，除非有明确、预注册且不使用隐藏响应的推理适配步骤。

### S01.T03 Submission contract fixture

- 目标：在任何主模型之前，构造一份模型无关的稀疏 360,000-row validation fixture 并通过当前 CLI dry-run。
- 目标代码：`src/arc_vcc/evaluation/submission_schema.py`、`tests/evaluation/`；产物只在 stage `derived/fixtures/`。
- 要求：三个 context、完整 300 targets、每 target/context 400 cells、官方 gene order、raw finite non-negative integer-valued counts、无 NTC rows、每 cell library 和 stored-entry 上限满足当前合同；显式零清除。
- fixture 只能验证 shape/schema，可用 NTC-resampling 或确定性 toy generator，不得被称作科学 baseline。
- L3 反向测试：context swap、panel 缺失/额外、gene order mismatch、float 非整值、负值/NaN、NTC row、401/399 cells、dense stored-entry 超限。
- 完成闸门：本地校验与 `vcc prep --dry-run` 都通过并保存版本证据；未运行官方 CLI 时只能标记 `not_validated_officially`。

### S01.A.R 阶段审阅

- 审阅实际 bundle、fixture 和官方 dry-run 证据。
- 若官方冲突会改变操作路径，停止并请求用户/主办方澄清。

## Phase S01.B：内部 OOD 切分与指标

### S01.T04 Fold registry 与拟合边界

- 目标：在真实训练前生成稳定、可重放且 group-safe 的 fold manifests。
- 主 fold：leave-one-cell-context-out；辅助 fold：leave-one-perturbation-out、context+perturbation 双留出；随机 cell split 只作实现 smoke test。
- 原因定位 fold：在 registry 支持时增加同 study 内 LOCO、同 cell model 跨 study/protocol、leave-one-study-out 和 leave-one-lineage-out；每类需报告可用 group、连接性和无法构造的原因。
- 分组单位：至少为 `study × cell_model/context × batch/replicate × condition`，同一供体/细胞模型的关联样本不得跨训练/验证泄漏；具体 group key 由 S00 registry 冻结。
- fold 内拟合项：gene eligibility、HVG、normalization 参数、program basis、candidate graph thresholds、balance models、early stopping、hyperparameters、calibration 和 ensemble weights。
- 预期产物：`folds/<fold_id>.yaml`、`fold_summary.tsv`、`fit_scope_contract.yaml`、`biology_protocol_split_availability.tsv` 和 `biology_protocol_separation_contract.md`。
- 完成闸门：每条 record 在每个适用 scheme 中归属唯一；训练/验证 group 不交叉；每个 validation context 只向模型暴露其 NTC 输入；无法构造的辅助 scheme 显式为 `not_estimable`，不影响主 LOCO 合同成立。
- 验证：group-disjoint 性质测试、目标标签 access guard、重复记录/同源样本泄漏扫描。

### S01.T05 六指标 harness 与辅助指标

- 目标：直接调用锁版 `cell-eval2 vcc2026`，不自行近似重写官方六指标。
- 官方指标：`pds_cosine`、`expr_mse_unbiased_capped_norm`、`de_wilcoxon_lfc_nmae`、`de_wilcoxon_direction_fidelity_yield_raw`、`de_wilcoxon_direction_reach_raw`、`de_wilcoxon_sig_jaccard`，保存 raw/scaled/context/overall 与 anchor stamps。
- 辅助指标：delta correlation、gene/program error、distribution distance、library-size/zero-fraction/variance calibration、target-level coverage；明确标记 `non_official`。
- 内部外部数据可能无法复现官方 anchors；此时保存 raw metric，内部 scaling 使用预注册 train-context baseline/replicate 方案并命名为 `internal_scaled`，不得冒充官方 scaled score。
- 统计单位：context 与 perturbation；bootstrap/重复 fold 必须 cluster-aware，不能把单细胞当独立模型比较样本。
- 预期产物：`metric_contract.yaml`、小型已知答案 fixtures、`metric_report_schema.json`。
- 完成闸门：锁版 scorer 对固定 fixture 可复现；metric 方向、缺失条件、target/panel exclusion 和 DE 参数与官方配置一致。

### S01.T06 模型晋级比较协议

- 目标：在看到候选模型结果前定义比较方向、主要指标族、不确定性和 practical effect 处理。
- 默认原则：模型必须在多数 LOCO contexts 上稳定不劣于当前简单基线，并在预先指定的核心指标族上显示一致增益；不得只挑 overall 或单 context 最好值。
- 数值阈值：只有在 S03 得到基线方差、fold 数和 scorer 可用性后才能通过计划修订冻结；此前不得沿用 v2 的任意百分比或 `p<0.05` 作为永久 gate。
- 预期产物：`model_comparison_protocol.md`、`promotion_decision_schema.yaml`。
- 完成闸门：记录主要/次要指标、比较单位、重复策略、平局/退化规则、复杂度惩罚和禁止的 post-hoc 选择。

### S01.T07 泄漏与不变量测试

- 覆盖：fold-scoped fit、validation response access、context label invariance、gene order、panel、NTC-only input、calibration/blend fit scope、官方/辅助指标命名。
- 预期产物：测试代码和 `reports/leakage_audit.md`。
- 完成闸门：人为注入的泄漏能使测试失败；guard 不依赖文件名约定而依赖显式 data role。

### S01.B.R 阶段真实产物复核

- 复核对象：官方合同、bundle schema、CLI dry-run、fold manifests、scorer fixture、比较协议与泄漏测试。
- 完成闸门：进度记录直接证据和 mismatch；用户批准 evaluation contract 后根进度才进入 S02。
- 禁止结论：本阶段通过不代表任何模型具备泛化能力。

## 计划变更规则

官方规则变化、bundle schema 变化、fold 定义或指标/晋级协议变化必须先修订本计划并在进度记录影响；实际版本号、hash、dry-run 结果和冲突回复只写进度/证据。
