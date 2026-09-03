# S04 候选 GRN 与边证据计划

## 目标与边界

- 目标：为每个外层 fold 冻结可追溯、容量受控且可做随机图对照的候选图 `M`，并生成与预测耦合分离的 interventional-effect evidence。
- 输入：S00 data release、S01 folds、S02 evidence release、经许可且已本地提供的外部图资源。
- 输出：`stages/04_candidate_grn/derived/<graph_release_id>/`、`reports/`、`src/arc_vcc/grn/`。
- 范围外：直接决定机制图 `G`；联网获取未经批准资源；把先验或算法排名写成因果边。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [`跨细胞系 GRN 构建`](../../../参考资料库/idea库/02_跨细胞系_GRN构建.md)：候选图、机制图、解释图三层边界。
- [`知识先验与现成方法复用`](../../../参考资料库/idea库/05_知识先验与现成方法复用.md)：prior 与随机图对照。
- [`因果重叠校正方法卡`](../../../参考资料库/文献/因果重叠校正/方法卡.md)：BackShift/ICP/JCI 的强假设和限制。

## Phase S04.0：实际开工调研与复用闸门

### S04.T00 GRN 方法、图工具与先验资源调研

- 作用域前置：先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)，再按 S02 的实际 nodes/evidence interface 调研。
- 这是实际开工任务：重新核验项目存量、已安装 `arboreto/networkx` 等依赖，以及 GENIE3、GRNBoost2、SCENIC/pySCENIC、Inferelator、CellOracle、BackShift/ICP/JCI 和 TF-target/pathway resources 的当前论文、官方实现、version/license 与维护状态；R00 和静态方法清单不能替代本次调研。
- 最低 capabilities：gene/program node mapping；tree/regression/correlation candidate estimators；perturbation edge evidence；stability selection；graph statistics/randomization；prior registry；可选多环境 causal score。
- 选择要求：优先成熟候选图 estimators 和 graph modules；项目只实现统一 edge schema、fold-safe adapters、证据合并和公平随机图生成。强假设 causal 方法不满足条件时 defer。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`，含各候选的方向语义、规模、license、adapter seam、验证与新增方法卡。
- 完成闸门：每个启用的 estimator/prior/graph capability 有 decision ID；官方实现/许可可用；`reuse_gate: pass`；自建 GRN estimator 或随机化核心已人工批准。
- 禁止：把静态方法清单视为已选模型；gate 未通过时进入 S04.T01+。
- 检查点：更新本阶段 progress；通过后继续 S04.A。

## 候选边 schema

每条边至少记录：`fold_id, source, target, level(gene/program), direction_semantics, data_methods, stability, evidence_type, total_effect_support, state_conditional_effect, heterogeneity_ref, prediction_interval_ref, equivalence_status, sign_consistency, context_interaction, protocol_stability, temporal_support, direct_regulatory_edge_status, prior_sources, prior_version/license, optional_invariance_score, optional_backshift_score, inclusion_rule, provenance`。`direct_regulatory_edge_status` 默认 `unresolved`；空字段保持空并说明原因。

## Phase S04.A：数据驱动候选空间

### S04.T01 节点宇宙与图层级

- 目标：冻结 gene-level 与 program-level 节点、允许的映射和容量预算。
- gene universe 来自 S02 fold-specific features；program graph 与 gene graph 分开，不把 `G_predictive=AB` 的 program coupling 伪装成逐基因直接边。
- 先定义 TF/调控因子候选 source 与所有可建模 target；target panel 不能成为唯一节点筛选依据。
- 预期产物：`nodes/<fold_id>.tsv`、gene-program mapping、graph capacity config。
- 完成闸门：训练/validation fold 边界清晰；节点覆盖、缺失和映射可对账。

### S04.T02 数据驱动候选生成

- 最小路径：在 training fold NTC/condition-level data 上运行一个可复现的树/回归型 GRN 候选器和一个简单 correlation/partial-correlation 对照；优先复用已安装、许可清晰的 GENIE3/GRNBoost2 等实现。
- 每种方法明确输入 layer、采样单位、seed、top-k/threshold 和方向语义；不得在 validation response 上生成或裁边。
- 大数据采用 context/lineage 配额与分块；同一 context 过多 cells 不得无上限主导。
- 预期产物：per-method edge tables、runtime/memory、method-specific reports。
- 完成闸门：合成已知图 smoke test与 seed 重跑通过；相同输入/seed 可重现边排序。

### S04.T03 Interventional total-effect 与跨环境证据

- 目标：把 S02 E1/E3、层级 effect synthesis 和 support 对候选 source→target/program 的终点总效应、状态依赖、异质性和跨 context 可比性转成独立证据字段。
- 统计单位为 environment/replicate；只在 support class 允许的范围汇总；状态交互与 shared effect 分开。
- 稳定性规则：不能把“不拒绝异质性”当作不变；预注册 practical-equivalence margin、最低环境数、prediction interval 与 influence 门槛，探索性多边检验控制 FDR。
- 时间信息：仅在设计/剂量/协议可比时生成 `temporal_support`；它只能辅助区分快慢响应，不能把单终点或跨 study 顺序升级为直接边。
- 输出 `effect_support` 不直接删除图外边；至少保留低配额 graph-out discovery channel。
- 完成闸门：每个 evidence score 可追到 S02 rows；无 overlap 的边不能获得“shared”标签。

### S04.T04 稳定性选择与候选合并

- 在 training fold 内做 seed/bootstrap/context subsample；报告 selection frequency、rank stability、sign consistency 和 context interaction。
- 合并规则在看 validation 结果前冻结；限制每节点/全图边预算并报告 degree distribution。
- 预期产物：`candidate_graph_data_only/<fold_id>.*`、稳定性报告、graph-out 配额。
- 完成闸门：每条入选边有明确规则；边预算不会因方法数量增加而隐式扩大模型容量。

### S04.A.R 阶段审阅

- 复核 nodes、per-method edges、effect evidence、稳定性和 fold isolation。

## Phase S04.B：先验、可选识别分数与消融资产

### S04.T05 外部先验 registry

- 目标：登记本地可用 TF-target/pathway/interaction 资源的来源、版本、物种、证据类型、许可和 gene mapping。
- prior 只提供候选 inclusion/soft penalty/解释字段；保留无 prior 训练路径。
- 若资源需新下载、账户、API 或许可证判断，停止并请求用户批准，不将获取偷偷并入 S04。
- 预期产物：`priors/registry.yaml`、标准化 prior edge table、冲突/许可报告。
- 完成闸门：每个 prior edge 有 source/version/license；无法许可的资源不进入可执行图。

### S04.T06 可选 ICP/JCI/BackShift 多环境辅助分数

- ICP/JCI 触发：环境/干预语义、条件集合、机制不变假设和统计单位可定义；输出只作候选不变性分数，不能由不显著结果证明边稳定。
- BackShift 触发：至少三个充分不同且 metadata 可辨的 environments、相同节点集合、近似平衡/线性化可接受、shift-intervention、equilibrium 与 hidden-confounding-stability 假设有诊断依据。
- 不满足时输出 `not_run` 或 `not_identifiable`，不得生成占位数值。
- 即使运行，score 只进入候选证据字段，不直接决定边，也不升级为非线性或直接因果发现。
- L3 测试：少于三环境 fail closed、已知循环线性图、违反稳定混杂假设的反向场景。
- 完成闸门：假设检查、数据矩阵和 score 都可追踪。

### S04.T07 真实先验、随机先验与无图资产

- 生成三组容量匹配资产：data-only/no-prior、data+real-prior、data+degree-matched randomized-prior；随机图保持节点数、边数和尽可能相近的 in/out-degree 分布。
- 随机化 seed 固定且 validation 结果不可见；多 seed 随机图用于不确定性。
- 预期产物：每 fold 图集、graph statistics、随机化测试。
- 完成闸门：真实/随机图容量与模型接口一致；任何差异均可归因并记录。

### S04.T08 Graph release 冻结

- 预期产物：manifest、nodes/edges、source tables、configs、tests、limitations、license registry、graph card。
- 完成闸门：从任一边追到生成方法/数据/先验；fold graph 不见 validation response；graph-out channel 存在。

### S04.B.R 阶段真实产物复核与人工闸门

- 工程 verdict：candidate graph release 是否完整可用。
- 科学 verdict：固定为对候选空间可用性的判断；任何具体边仍为 `not_tested`/`unresolved`。
- 用户批准候选图版本后进入 S05。

## 资源与计划变更

- 先在小节点集/单 fold dry run，再扩展；每个方法/seed独立 checkpoint，及时释放大矩阵与模型。
- 节点宇宙、合并规则、边预算、prior 用法、BackShift 触发或 graph schema 改变时先修订计划；实际边数/分数写进度/报告。
