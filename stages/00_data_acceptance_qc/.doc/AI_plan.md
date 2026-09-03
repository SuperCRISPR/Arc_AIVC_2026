# S00 数据接收、标准化、QC 与人工评估计划

## 目标与范围

- 目标：把用户手动拷贝的上游数据转化为不可变、可追溯、统一语义且经人工诊断批准的数据发布版。
- 输入根：`Stage_0-raw_training_data/`，沿用 `datasets/<dataset_id>/raw/`、`DATASET_CARD.md` 与 `MANIFEST.tsv` 上游约定。
- 输出根：`stages/00_data_acceptance_qc/derived/<release_id>/` 与 `reports/`。
- 范围外：数据调研、下载、远程补齐；模型训练；跨环境科学效应解释；覆盖写回 raw。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [`技术路线与项目设计-v2.md` 第 5 节](../../../技术路线与项目设计-v2.md)：上游数据 schema 与候选用途，须按本计划移除获取任务。
- [`Stage 0 既有审计`](../../../document/项目审计/已落实/2026-08-15_Stage0技术路线与数据审计.md)：历史风险与缺口，仅作设计依据，不代表当前 copy 状态。
- [`2026 VCC 官方信息核验`](../../../参考资料库/竞赛资料/2026_VCC_官方信息核验_2026-08-30.md)：raw counts、gene/context 不变量与人工评估边界。

## Phase S00.0：实际开工调研与复用闸门

### S00.T00 数据工程方法调研与复用决策

- 作用域前置：即使从本 stage 目录启动，也必须先完整读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)。
- 这是实际开工任务：结合用户刚拷贝的真实文件类型、字段、大小和上游卡片，重新检查项目存量、本地依赖、阶段必读资料及当前一手文档/官方实现；R00 registry 和本计划清单不能替代本次调研。
- 最低 capabilities：H5AD/AnnData、10x H5/MTX、H5MU/MuData、RDS/Seurat/Monocle adapter；稀疏/分块 I/O；QC/doublet/guide assignment；gene ID 映射；PCA/neighbors/UMAP；报告与 provenance。
- 选择要求：优先现有 adapter、AnnData/MuData/Scanpy、Seurat/官方转换工具和成熟 QC modules；每种实际输入格式必须记录选择、adapter seam、版本、license、内存特性和小型试配计划。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`；新 shortlist 方法先归档到参考资料库并更新索引。
- 完成闸门：每个 capability 有 decision ID，所有真实输入格式均有合法可执行路线，`reuse_gate: pass`，progress 引用实际文件；涉及格式转换器或核心 QC 的 `minimal_new` 已人工批准。
- 禁止：数据未拷贝时用历史文件格式代替现场调研；gate 未通过时进入 S00.T01+。
- 检查点：更新本阶段 progress；通过后继续 S00.A。

## 固定数据契约

每个 canonical AnnData 必须满足：

- `.X` 或 `.layers['counts']` 保存稀疏、有限、非负、整数意义 raw counts；若 `.X` 用于分析 view，raw counts 必须在独立 layer 且 provenance 明确。
- `.obs` 至少保留可得的 `dataset_id, study_id, source_sample_id, cell_model, batch, replicate, timepoint, perturbation_modality, target_gene, guide_id, is_ntc, condition, qc_status, eligibility_branch`。
- `.var` 至少含 `gene_symbol_original, gene_symbol_canonical, ensembl_id`（存在时）、`mapping_status` 与原始顺序。
- `registry/field_dictionary.yaml` 为每个可能进入分析的字段登记 `measurement_timing: pre_assignment|post_assignment|unknown` 与 `covariate_role: pre_treatment|design_variable|effect_modifier|mediator_candidate|selection_variable|diagnostic_only`；同一字段在不同数据源语义不同时分开登记。
- registry 尽可能记录 assignment unit/分配方式、CRISPRi system、MOI、guide capture、culture/pooling、采样时间、敲低效率测量和存活/捕获机制；缺失保持缺失，不按常识补全。
- `.uns['provenance']` 记录源文件、转换器版本、配置标识和字段映射；缺失值必须显式为空并带 `missing_reason`，不得猜测。
- raw、canonical、QC-filtered 和 visualization view 使用不同路径；任何派生文件不得覆盖上游文件。

## Phase S00.A：只读接收与输入登记

### S00.T01 接收核验

- 目标：证明用户拷贝的 bundle 在本机存在、可枚举且与供应 manifest 一致。
- 目标范围：`Stage_0-raw_training_data/` 只读；`derived/<release_id>/intake/` 写入。
- 预期产物：`input_inventory.tsv`、`copy_verification.json`、`reports/intake_report.md`。
- 具体检查：相对路径、字节数、供应状态、checksum（若 manifest 提供则比较，否则生成 copy-side checksum 并标注无上游对照）、压缩完整性、HDF5/AnnData 可打开性、磁盘容量与重复文件。
- 完成闸门：每个批准 dataset 均归类为 `received_verified`、`received_unverified_manifest`、`missing_from_copy` 或 `unreadable`，不存在未解释文件。
- 验证：对小文件完整读取；对大对象用格式级打开、shape/dtype/schema 读取；不得为验证把全矩阵载入内存。
- 禁止：自行联网下载、修复或删除缺失/损坏文件；把历史获取快照当作当前 copy 证据。
- 检查点：更新本阶段进度，继续 S00.A。

### S00.T02 数据卡与字段字典对齐

- 目标：把上游卡片和实际文件字段映射到一个显式 registry。
- 预期产物：`registry/datasets.yaml`、`registry/field_dictionary.yaml`、`reports/metadata_gaps.tsv`。
- 具体要求：逐数据集记录许可、模态、细胞模型、实验系统、timepoint、batch/replicate、NTC、guide assignment、assignment unit、CRISPRi/MOI/capture/culture 设计、敲低效率来源、源 counts 形态、主/辅助候选角色；每个字段登记 measurement timing/covariate role，每个推断或人工补充字段必须带来源与 reviewer。
- 完成闸门：每个输入文件有唯一 dataset/source sample 归属；关键字段缺失均被列为缺口，未被静默填充。
- 验证：schema 校验、dataset/file 双向覆盖检查、许可与用途枚举检查。
- 禁止：以“公开可下载”推断任意训练许可；以文件名猜测未核实的实验元数据。

### S00.A.R 阶段审阅

- 直接审阅 intake 产物与真实文件样本。
- 只批准进入转换的实际收到资产；缺失项保持 external prerequisite，不触发获取任务。

## Phase S00.B：逐源转换与质量标记

### S00.T03 Canonical 转换器与小样本 dry run

- 目标：为每类源格式实现一个显式 adapter，并先在小样本/切片上验证语义。
- 目标代码：`src/arc_vcc/data/ingestion/`；薄入口：`stages/00_data_acceptance_qc/scripts/`；测试：`tests/data/`。
- 支持分支：AnnData/H5AD、10x H5/MTX、MuData/H5MU、RDS/Seurat/Monocle。缺少 R 环境或必要读取器时记录 blocker，不转换为 CSV 规避语义。
- 转换要求：保持稀疏；记录原始与 canonical shape；检测转置；保留 raw gene order；明确 cells×genes 方向；规范 control/target/guide 标签；不执行 QC 删除。
- 预期产物：每种 adapter 的 schema fixture、转换报告和 `derived/.../canonical/<dataset_id>.h5ad` 或等价分片对象。
- 完成闸门：每个可转换 dataset 的 counts 总和抽样一致、行列和关键标签一致，重复 cell/gene IDs 有确定处理记录。
- L3 测试：转置检测、非负整数检查、稀疏零清理、标签映射、round-trip 元数据、损坏/缺字段的明确失败。

### S00.T04 数据清洗、QC flags 与 guide/condition 诊断

- 目标：先计算可审计 flags，再决定排除；保留被排除细胞的索引与原因。
- 指标：`total_counts, n_genes_by_counts, pct_counts_mt, pct_counts_ribo`，可得时加 doublet score、guide UMI、guide multiplicity、target assignment confidence 与 ambient/background 指标。
- 角色边界：QC 指标默认是 `diagnostic_only`；能否作为过滤依据由本阶段人工闸门决定，能否进入 S02 adjustment set 由因果 estimand 合同另行决定。KD 后或时间顺序未知的指标不得在本阶段被授权为普通混杂变量。
- 阈值流程：按 `dataset × source sample × batch` 生成分布；以中位数/MAD 等鲁棒规则提出初值并输出图表；硬阈值必须在人工评审前冻结到配置，不得跨协议套用一个未经检查的数值。
- perturbation 完整性：区分 single-guide、multi-guide、unassigned、NTC、safe-target；主域默认只接受与数据卡一致的高置信 CRISPRi target/NTC，其他记录先分支而非删除。
- 预期产物：`qc_metrics.parquet`、`qc_flags.parquet`、逐数据集 QC 报告、阈值配置。
- 完成闸门：每个 flag 有规则、统计单位和原因；过滤前后 cell/condition/NTC 数量可对账。
- 验证：合成边界样本性质测试、实际小样本重算一致性、counts 不变性检查。

### S00.T05 训练资格筛选、分流与基因规范化

- 目标：形成主训练、辅助评估和隔离三条不混淆的数据支路。
- 主域默认条件：human、单基因 CRISPRi、可追踪 target/guide、matched NTC、raw counts 可用、许可允许；药物/刺激数据只取预先定义的 control 条件；KO/CRISPRa/multi-guide 进入辅助或隔离分支。
- 基因处理：使用可追踪 HGNC/Ensembl 映射；一对多/历史符号/重复 gene 先报告再按冻结规则聚合；完整 raw gene set 始终保留，训练 feature universe 留给 S01/S02 在 fold 内决定。
- 预期产物：`eligibility/records.parquet`、`eligibility/dataset_decisions.yaml`、`gene_mapping.tsv`、`reports/exclusion_ledger.md`。
- 完成闸门：每个 cell/condition 有且仅有一个 branch；每个排除有机器规则或人工决策 ID；分流前后总数守恒。
- 禁止：依据预期 perturbation 效果、UMAP 形状或比赛 target panel 后验挑选记录。

### S00.B.R 阶段审阅

- 审阅转换代码、实际 canonical 对象、QC 对账和资格分流。
- 工程通过不等于生物质量批准；继续进入人工诊断图阶段。

## Phase S00.C：UMAP、人工评估与发布冻结

### S00.T06 冻结诊断视图

- 目标：生成可复现、不会改变主数据的 PCA/UMAP 诊断包。
- 视图至少包括：NTC-only；按 dataset/lineage 配额采样的全条件视图；逐 dataset 视图。颜色面板包括 dataset、cell model、batch、replicate、time、NTC/target、QC 指标、guide confidence；不得混入隐藏响应。
- 预处理与随机种子写入 `visualization_config.yaml`；normalized/log1p/HVG/PCA/neighbors/UMAP 只存派生 view。
- 预期产物：静态图、低维坐标、样本清单、参数、`reports/visual_review_packet.md`。
- 完成闸门：同一配置可重现坐标/邻接摘要；每张图能追踪到 exact cells；图中标签不改变原始 context。
- 禁止：把 UMAP mixing 当作 batch correction 或科学成功；看图后反复调参数直到得到期望形状而不记录。

### S00.T07 人工诊断闸门

- 目标：由人类按预先列出的检查问题，将 dataset/condition 判为 `accept`、`reprocess` 或 `quarantine`。
- 检查问题：是否存在样本标签错配、明显低质量群、批次完全支配、NTC 异常、guide assignment 断裂、协议子集混合、意外双细胞/污染迹象；每一判断需配套非视觉统计或保留 `unresolved`。
- 预期产物：`reports/human_review.md` 与签署的 `human_review_decisions.yaml`。
- 完成闸门：所有待评项目有决定、理由、证据和允许用途；任何 route-changing 决定由用户批准。
- 科学结论：固定为 `not_tested`；视觉结果不能升级。
- 检查点：这是强制人工闸门，停止等待批准。

### S00.T08 发布冻结

- 目标：在人工批准后冻结可供后续阶段只读使用的 data release。
- 预期产物：`release_manifest.yaml`、逐 dataset canonical/QC 对象、cell/gene/condition summaries、field/covariate-role dictionary、assignment/selection metadata、配置快照、环境标识、checksums、排除 ledger 和发布报告。
- 完成闸门：manifest 能从 source path 追踪到每个输出；所有输出可打开；shape/counts/branch 对账通过；发布 ID 在后续阶段作为固定输入。
- 资源要求：大对象使用 backed/chunked 路径；每个 dataset 完成后显式释放 AnnData/R 对象并执行内存清理。

### S00.C.R 阶段真实产物复核

- 复核对象：release manifest、实际数据对象、QC/分流/人工决策、读取与对账测试。
- 完成闸门：阶段进度记录 `review_performed`、证据、缺口和下一纠正；用户批准后才将根进度切换到 S01。
- 禁止结论：S00 通过不支持共享机制、因果边或模型泛化。

## 计划变更规则

仅在输入路径、canonical schema、任务分解、完成闸门或验证方法发生持久变化时修改本计划；数据实际数量、checksum、错误和评审决定写入进度/报告，不写回计划。
