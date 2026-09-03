# S02 Fold 内表征、matched NTC 与因果证据计划

## 目标与边界

- 目标：生成 fold-scoped program 表征和带支持域、权重、方差、失败标签的 condition-level evidence release。
- 输入：S00 `release_id`、S01 fold/evaluation contract。
- 输出：`stages/02_representation_evidence/derived/<evidence_release_id>/`、双 DAG/E1-E4 合同与共享 representation/causal 代码。
- 范围外：宣称直接因果 GRN；无约束全局 batch integration；从不重叠数据算法性补出识别性；模型选择。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [`机制与状态解耦`](../../../参考资料库/idea库/01_机制与状态解耦.md) 与 [`GRN-ODE 有限时间边界`](../../../参考资料库/idea库/03_GRN_ODE与有限时间边界.md)：状态表示和可检验边界。
- [`跨环境重叠与因果校正`](../../../参考资料库/idea库/06_跨环境重叠与因果校正.md)：condition-level evidence、共同支持与不可识别情形。
- [`因果重叠校正方法卡`](../../../参考资料库/文献/因果重叠校正/方法卡.md)：平衡、稳健摘要和强假设方法的启用限制。

## Phase S02.0：实际开工调研与复用闸门

### S02.T00 表征、匹配与因果证据方法调研

- 作用域前置：先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)，再使用 S00/S01 的真实 release/interface 约束调研。
- 这是实际开工任务：重新调查项目存量、已安装依赖、必读资料以及当前 PCA/NMF/cNMF、bootstrap、matching、hierarchical/meta-regression、balance weighting、DML/CATE、guide-IV、sensitivity 和 overlap diagnostics 的一手实现；R00 不替代现场数据规模与 estimand 适配检查。
- 最低 capabilities：双 DAG/E1-E4 registry；fold-scoped normalization/feature selection；PCA、NMF/cNMF；NTC distribution summary；matched E1 effects；cluster/bootstrap uncertainty；covariate-role/selection/interference audit；overlap graph/MMD/邻域支持；hierarchical effect synthesis；稳定平衡权重；可选 cross-fitting/DR、E2 guide-IV 与匹配尺度的 sensitivity summary。
- 选择要求：优先成熟矩阵分解、统计和因果估计 modules，通过窄 adapter 统一 condition-level interface；强假设方法只有识别条件满足时进入 shortlist。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`，逐 capability 记录版本、license、稀疏/规模能力、统计假设和验证 fixture；新方法先归档。
- 完成闸门：所有默认和可选支路有 decision ID；不满足识别条件的候选明确 defer；`reuse_gate: pass`；核心估计器 `minimal_new` 已人工批准。
- 禁止：以旧方法卡代替当前版本/实现核验；gate 未通过时进入 S02.T01+。
- 检查点：更新本阶段 progress；通过后继续 S02.A。

## 固定统计单位与对象

- 环境 `e = study × cell_model/context × protocol × batch × replicate × timepoint`。
- `S_e`：仅由该环境 matched NTC 得到的预干预状态摘要。
- `d_{e,a}`：perturbation `a` 与同环境 NTC 在 fold-scoped program/gene 空间中的条件分布差异。
- 独立证据单位：环境中的 condition/replicate，不是其中单个 cell。
- 原始 NTC/KD 细胞和 counts 始终保留；evidence store 是索引/摘要，不是“纠正后表达矩阵”。

## 注册因果合同

- `causal/estimand_contract.yaml` 注册 `E1_assignment_itt`、`E2_knockdown_dose`、`E3_transport`、`E4_prediction_distribution` 的处理、结局、统计单位、目标总体、效应尺度、识别假设和禁止解释。
- `causal/dag_contract.md` 分别描述实验内 `Z/K/S/Y/Q_post/V` 与跨环境 `S/H_pre/coverage/U/Y`；每次 estimand 修订必须产生新 revision，不能覆盖旧合同。
- `causal/covariate_role_manifest.yaml` 读取 S00 field dictionary，冻结每个字段的 measurement timing、role 和 allowed use。`post_assignment`、`mediator_candidate`、`selection_variable` 与时间未知字段默认不进入 matching/weighting/outcome adjustment。
- `causal/selection_interference_assumptions.yaml` 记录 assignment unit、pooled-culture/partial-interference、存活/捕获选择、缺失响应和可用诊断；未满足时限制结论而非静默忽略。
- E2/E3 不可识别不阻止 E4 fallback，但 evidence 和下游报告必须保留 `not_identifiable`。

## Phase S02.A：Fold 内表征

### S02.T01 Feature universe 与 normalization 合同

- 目标：在每个外层 fold 的训练部分确定可建模基因和分析 view，同时保留官方完整 gene axis 的输出能力。
- 要求：gene mapping 来自 S00；过滤只基于训练 fold 的表达/QC；validation context 只按冻结规则投影；raw counts 独立保存。
- normalization 至少保留 library-size/log1p 基线；任何 Pearson residual、SCTransform 或 learned normalization 都是比较分支，需证明不破坏 count provenance。
- 预期产物：每 fold 的 `feature_manifest.yaml`、normalization config、train/validation gene coverage 报告。
- 完成闸门：validation 信息不参与 feature selection；缺失/额外 genes 有冻结的 projection/zero-fill 规则；官方输出 gene order 不被训练 feature 顺序替代。
- 测试：fold leakage、gene order、raw layer 不变、sparse/dense 一致性小样本测试。

### S02.T02 Program basis 与纠缠诊断基线

- 目标：实现 PCA、NMF/cNMF 中至少一个非负方案与一个线性 PCA 基线，选择由 S01 协议控制；用于 `S_e` 的主 basis 默认只拟合训练 fold NTC cells。
- 可选响应 basis：若全部训练 cells 能改善 response 表示，必须与 NTC-only state basis 分开保存、独立命名和消融，不得改变 `S_e` 语义。
- 谱系/数据量不平衡：拟合采样默认按 context/lineage 设置上限或均衡 quota；同时保留 unweighted 对照，避免 X-Atlas 等大数据集支配。
- 每 fold 独立拟合 encoder/basis/decoder；保存 seed、K、训练 cells、重构误差和 loading；不得跨 fold 复用 fitted W。
- K 的候选范围与选择规则在训练 fold 内预注册；不按 validation hidden response 调整。
- 纠缠诊断：dataset/context predictability、target-context association、`S_e` 与 `d_ea` 的轴重叠、NTC-only/all-cell basis 差异；诊断结果只触发表示复核，不自动声称状态/响应已分离或未分离。
- 预期产物：`programs/<fold_id>/`、program annotations、重构/稳定性/纠缠报告。
- 完成闸门：basis 可重放；留出 context 仅 transform；不同 seed/program matching 的稳定性可量化；state/response basis provenance 清楚；内存使用符合 chunked 计划。

### S02.T03 NTC state 摘要

- 目标：对每个 environment 生成既保留分布又可用于 overlap 的 `S_e`。
- 至少保存：program mean/covariance、cell-level program samples 的可追踪索引、NTC cell 数、bootstrap uncertainty、library/QC diagnostic summary 及其 covariate role；诊断字段不自动进入后续调整集。
- NTC guide 可作为重复结构保留；不得先把不同 guide/batch 无条件合并。
- 预期产物：`state/ntc_state.parquet`、分布对象/索引和诊断报告。
- 完成闸门：每个进入主域的 KD condition 都能指向唯一 matched NTC set，或明确标记 `no_matched_ntc`。

### S02.A.R 阶段审阅

- 复核 fold-scoped fit、representation artifacts、NTC 匹配和内存路径。
- 表征质量通过不等于存在共享机制。

## Phase S02.B：Matched evidence 与支持域

### S02.T04 Matched NTC E1 assignment effects

- 目标：按环境/target/replicate 计算 `E1_assignment_itt` 的 gene/program 分布差异与不确定度；每个数据集记录 assignment 依据，不能一律宣称随机化。
- 输出至少含均值差、方差/协方差摘要、bootstrap CI、cell counts、NTC matching key 和 guide aggregation rule；必要时保留 pseudobulk replicate。
- guide 聚合先在同 target、同环境内评估方向一致性；不一致 guide 降权/分开或隔离，规则冻结后应用。
- selection/interference：报告 KD/NTC 观测率、存活/捕获/QC 差异、pooled-culture 范围和可用 placebo；没有证据时保持假设 `unresolved`。
- 完成闸门：加性 batch 偏移的合成测试能被 matched diff 消除；注入 `KD × batch` 交互、post-treatment adjustment 或 selection bias 时反向测试必须检测残余/偏差而非错误通过。
- 禁止：将 KD 与 NTC cells 做一一伪配对；把细胞数当跨环境 sample size。

### S02.T05 Overlap 连接图与可运输性审计

- 目标：建立 `environment/state ↔ perturbation` 二部图与 protocol/batch 交叉图，先判断是否可识别再拟合权重。
- 对每 target 报告：环境/独立重复数、context/lineage 数、状态距离/局部支持、protocol/batch 交叉、connected component、support flag、effective independent units。
- 状态支持使用低维、训练 fold 预定义的 program scores；并列报告标准化差异、MMD/近邻覆盖等，不以单一凸包指标下结论。
- 结果分类：`shared_evidence_candidate`、`limited_transport`、`single_domain_only`、`not_identifiable_metadata`。
- 预期产物：`overlap/graph.*`、`overlap_by_target.parquet`、`reports/overlap_audit.md`。
- 完成闸门：所有进入共享证据的 target 有 matched NTC 和真实跨环境连接；完全共线的 cell model/batch 不被标记为可分离。

### S02.T06 层级 effect synthesis 与条件平衡权重

- 默认路径：对每个可汇总 target/program/edge 拟合低维、部分池化的 hierarchical meta-regression；共享项、NTC-state effect modification、可识别的 study/protocol/batch 项分开，报告 heterogeneity、prediction interval、sign consistency、leave-one-environment-out 和 influence。
- 小样本约束：moderator/interaction 数受独立环境数限制；使用适合少环境的不确定度/收缩候选并在 T00 决策；cell 数只影响 condition variance，不增加环境自由度。
- 平衡触发：仅对 S02.T05 中支持充分、独立环境数允许、存在真实交叉重复且 positivity 可诊断的 target/stratum 执行。
- 比较：未加权/逆方差层级摘要与至多一种主稳定平衡方法；overlap weighting、entropy balancing 或 kernel balancing 的启用依据真实倾向分布、维度和 ESS 选择并记录。
- 必须诊断：加权前后 balance、weight range/percentiles、ESS、单环境支配度、截断敏感性；极端或低 ESS 时回退为 `limited_transport`，不是强行归一化。
- 两层权重：数据集/谱系采样权重与因果 balance 权重分别存储、组合前后诊断。
- 预期产物：`weights.parquet`、balance reports、方法配置。
- 完成闸门：层级模型在合成异质性/单环境支配场景中行为符合声明；平衡性质测试通过；真实数据没有未报告的极端权重；加权未改善 balance/稳定性时保留层级基线并回退。

### S02.T07 可选 DR、E2 guide-IV 与敏感性支路

- 触发：每个 estimand 有足够独立 environments 做交叉拟合，并能明确 outcome 与 coverage nuisance model。
- 输出只用于 program/edge effect summary 和 uncertainty；不生成 corrected single-cell matrix。
- 若样本量不足、positivity 失败或模型不可诊断，记录 `not_run`/`not_identifiable`。
- E2 guide-IV：仅在 target 有多条独立 guide、实际 knockdown `K` 可可靠测量、first stage 足够、排除限制/单调性/脱靶和存活选择可审计时，估计 dose effect 或生成 `rho` calibration evidence；主 E1 不被替换。
- Proximal causal learning 不在默认路径；只有找到满足定义的双代理并经计划修订/人工批准后才可增加。NTC treatment 本身不自动是合格代理。
- 候选图、管家基因或响应数据本身不能循环定义负对照；代理需有独立设计/外部证据。
- E-value、regression robustness value 或其他敏感性量均不作为默认通用输出；只有 estimand、处理、效应尺度和偏差模型匹配时另行注册。
- 完成闸门：交叉拟合分割独立、合成 known-effect 测试通过、失败条件能 fail closed。

### S02.T08 Evidence store 与发布冻结

- 每条记录至少含：`fold_id, environment_id, target_gene, modality, estimand_id, target_population, assignment_unit, effect_scale, S_ref, effect_ref, variance_ref, matched_ntc_ref, covariate_role_manifest_ref, allowed_adjustment_set, support_class, hierarchical_summary_ref, heterogeneity_ref, prediction_interval_ref, weight components, independent_unit_count, protocol/batch diagnostics, guide_consistency, selection/interference flags, rho_source/calibration_status, method flags, provenance`。
- 预期产物：versioned evidence store、schema、release manifest、coverage/limitations report。
- 完成闸门：从任一 evidence row 可追到 S00 cells、S01 fold、方法配置和实际输出；raw distributions 未被覆盖。
- 资源要求：condition-level summaries 可内存处理；cell-level bootstrap 分 dataset/chunk 执行，每个重任务后释放对象并清理内存。

### S02.B.R 阶段真实产物复核与人工闸门

- 复核对象：双 DAG/E1-E4、representation/纠缠诊断、matched effects、selection/interference、overlap 图、层级汇总、weights/ESS、可选支路、evidence store 与 limitations。
- 工程 verdict：evidence release 是否可用。
- 科学 verdict：仅针对“现有数据是否足以检验共享机制”的证据状态；`supported` 共享机制仍禁止。
- 用户批准共享证据域、单域辅助项与不可运输项后，才进入 S03/S04。

## 计划变更规则

representation、统计单位、support 定义、weighting 方法、evidence schema 或触发条件改变时先修订计划；实际 K、counts、ESS、权重和审计结果写入进度/报告。
