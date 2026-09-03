# S03 强基线与保底推理计划

## 目标与边界

- 目标：建立可复现、泄漏安全、能生成官方 raw-count schema 的简单模型阶梯，并冻结至少一条保底路径。
- 输入：S00 release、S01 contract/folds、S02 representation/evidence release。
- 输出：`stages/03_baselines/runs/`、`derived/<baseline_release_id>/`、`reports/` 与 `src/arc_vcc/models/baselines.py` 等共享代码。
- 范围外：候选 GRN、nonlinear ODE、隐藏 leaderboard 调参、手工 target-specific 修正。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [`基线、验证与复杂度门控`](../../../参考资料库/idea库/04_基线_验证与复杂度门控.md)。
- [`Linear Baselines 方法卡`](../../../参考资料库/文献/Linear-baselines/方法卡.md) 及其本地论文。
- [`scPerturBench 方法卡`](../../../参考资料库/文献/scPerturBench/方法卡.md)：OOD 切分与指标协议。
- [`2026 VCC 官方信息核验`](../../../参考资料库/竞赛资料/2026_VCC_官方信息核验_2026-08-30.md)：raw-count 单细胞输出合同。

## Phase S03.0：实际开工调研与复用闸门

### S03.T00 基线与单细胞 count generator 调研

- 作用域前置：先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)，再以 S01/S02 的实际 interfaces 为约束。
- 这是实际开工任务：复查项目存量、已安装统计/ML modules、Linear Baselines/scPerturBench 官方实现及当前 count distribution/generator 方法；R00 或论文结论不能替代现场代码、版本和输入适配调查。
- 最低 capabilities：no-change/NTC resampling、mean delta、ridge/elastic-net、hierarchical/mixed-effects meta-regression、small-sample uncertainty/prediction interval、reduced-rank regression/operator、Poisson/NB/residual bootstrap、sparse raw-count sampling、model card/run provenance。
- 选择要求：成熟 linear estimators 和 distribution implementations 优先；项目代码主要提供统一 fold/effect/generator adapters，不重复实现可靠的优化器或分布采样器。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`，记录接口、版本、license、输出语义、规模和小型已知答案验证；新 shortlist 先归档。
- 完成闸门：B0–B3（含 B2H）与 generator 每项有 decision ID；fallback 路线无未解决许可/interface 问题；`reuse_gate: pass`；自建 generator/estimator 例外已人工批准。
- 禁止：为匹配预期结论而忽略更成熟强基线；gate 未通过时进入 S03.T01+。
- 检查点：更新本阶段 progress；通过后继续 S03.A。

## 共用训练与输出合同

- 每个 baseline 在完全相同的外层 folds、gene universe、NTC-only target input 和 metric harness 下评估。
- 任何超参数只在训练 fold 内层选择；validation context response 不可见。
- 模型内部的 normalized effect、mean 或 program prediction 与最终 raw-count cell generator 分离保存。
- 每个预测必须可由 `data release + fold + config + seed + code/environment id` 重放。

## Phase S03.A：最小和线性基线

### S03.T01 B0 No-change

- 定义：仅从目标 context NTC 分布生成每个 target 的 400 cells，不使用 target-specific effect。
- 至少比较两种预注册生成方式：确定性/分层重采样 NTC 与一个保持 NTC gene-wise count statistics 的 parametric sampler；二者只用于明确 no-change 下限。
- 预期产物：fold predictions、generator diagnostics、六指标/辅助指标报告。
- 完成闸门：不同 target 在同一 context 的期望预测相同；输出满足 raw/schema；seed 重放一致。
- 禁止：用 target identity、外部响应或 validation score 改变采样。

### S03.T02 B1 Matched/source mean delta

- 定义：从训练 contexts 的 matched NTC effect evidence 得到 target-specific 平均或状态邻域平均 effect，再加到目标 NTC baseline。
- 必须并列：全训练域 inverse-variance mean delta；只在 S02 支持域内的 state-neighbor/matched delta。无支持 target 回退 B0 并记录 coverage。
- aggregation 以独立 environment/replicate 为单位；不得让大细胞数数据集独占。
- 预期产物：target/context effect table、coverage/fallback ledger、预测与评测报告。
- 完成闸门：effect 只由 training fold evidence 产生；无记录的 fallback 为测试失败。

### S03.T03 B2 Ridge/elastic-net

- 目标：检验 target encoding、NTC state 和可观测 protocol covariates的线性/稀疏线性映射能否解释 response。
- 输入特征和输出 estimand 必须在配置中明确；target 特征先用 one-hot/可观测基因特征的简单版本，外部 embeddings 属于 S07。
- 正则化、alpha/l1 ratio 只在 inner folds 选择；输出 gene/program effect 与不确定性。
- 预期产物：系数、feature manifest、inner/outer fold 报告、预测。
- L3 测试：closed-form/小型已知解、shape、目标编码置换、fold leakage、稀疏输入一致性。

#### B2H Hierarchical conditional-effect baseline

- 目标：以 condition/replicate 为独立单位，显式比较共享 target effect、NTC-state effect modification 与可识别的 study/protocol/lineage 异质性。
- 模型维度：在 program 或预注册低维 gene summary 上运行；moderator/随机项数量受独立环境数约束，缺乏交叉重复的项标记不可估计。
- 输出：共享项、state effect、异质性、prediction interval、leave-one-environment-out、influence 和针对目标 NTC state 的 effect prediction。
- 完成闸门：与 B1/B2 使用相同 evidence 和 fold；合成已知异质性、小环境数、单环境支配与不可识别场景测试符合声明；不把系数解释为 GRN 边。

### S03.T04 B3 Reduced-rank program linear

- 目标：检验低维 program dynamics 是否已足够，无需 ODE 非线性。
- 使用 S02 fold-specific basis，拟合共享低秩 finite-time operator；rank 和正则只在 inner fold 选择。
- 保存 operator spectrum/stability diagnostics，但不得解释为真实连续动力学。
- 预期产物：operator、rank selection evidence、predictions、与 B2 同协议比较。
- 完成闸门：projection/decoder 无 fold 泄漏；数值有限；重放一致。

### S03.A.R 阶段审阅

- 直接检查 baseline code、配置、fold outputs、fallback coverage 和测试。
- 确认所有 baseline 共享同一 metric/output path，避免不公平比较。

## Phase S03.B：Raw-count generator、比较与冻结

### S03.T05 通用 raw-count cell generator

- 目标：把模型的 effect/mean/distribution 参数转成每 target/context 恰好 400 个稀疏 raw-count cells。
- 最小候选：目标 NTC cells 重采样 + 预测 effect 的非负 mean adjustment；可选 NB/Poisson 或 residual bootstrap 仅在训练 fold 拟合 dispersion/residual。
- 必须处理：非负、整数化、library-size、zero fraction、gene covariance/program variance、官方 gene order、stored entries；round/clip/renormalize 的顺序和偏差需在配置与报告中显式。
- 不得用文献/实验结果人工修正具体 target；不得从 normalized matrix 假装还原原始 counts 而无生成模型。
- L3 测试：已知均值/dispersion 模拟、seed、400-cell cardinality、极端 effect、all-zero/overflow、sparse zero cleanup。
- 完成闸门：每种 baseline 通过同一 generator 和 S01 contract tests。

### S03.T06 全 fold 评测与稳定性

- 运行 LOCO 为主、LOPO/双留出为辅；S01 可用时同时运行同-study LOCO、同-cell-model 跨-study、leave-one-study-out 和 leave-one-lineage-out 原因定位拆分；保存六项 raw/internal-scaled metrics、科学辅助指标、每 context/target 结果、runtime/peak memory。
- 比较单位是 context/perturbation 与重复 fold；报告置信区间、胜负一致性和 failure slices，不用单细胞 p-value。
- 每个失败报告按 cause ledger 区分 data/support、generator、representation、optimization（适用时）、metric geometry 与 hypothesis。
- 完成闸门：所有预注册 folds 均有结果或明确 blocker；缺失结果不能被 overall 平均掩盖。

### S03.T07 冻结保底模型与晋级协议数值化

- 目标：按 S01 比较协议选出最简单的不劣 baseline，冻结为 `fallback_release`。
- 预期产物：model card、完整 config/seed/environment、fold report、raw generator、submission fixture dry-run、limitations、release manifest。
- 使用 S03 实测方差修订并冻结 S01 的 practical-effect/平局阈值；这是持久合同变化，须先修改 S01 plan 并在两阶段进度记录 amendment。
- 完成闸门：第三方可从 frozen inputs 重放一 fold 与 submission fixture；无高级模型依赖。
- 禁止：因复杂 baseline 分数略高但不稳定而自动选择；因格式通过称为竞赛有效模型。

### S03.B.R 阶段真实产物复核与人工闸门

- 工程 verdict：fallback release 是否完整可重放。
- 科学 verdict：仅说明简单模型能否恢复可迁移扰动信号；不评价共享 GRN 假设。
- 用户批准 baseline 下限和 S04/S05 比较门槛后继续。

## 资源与恢复

- 先跑单 fold/少量 targets dry run，再运行全 fold；长任务写周期性 progress 与 per-fold checkpoint。
- 每个 fold/模型结束后释放 matrices/models/dataloaders 并执行内存清理；失败 fold 可独立恢复。

## 计划变更规则

baseline 定义、generator、fold、metric 或 promotion protocol 改变时修订计划；实际 scores、耗时、release IDs 和失败写入进度/报告。
