# S08 竞赛推理、格式验证与提交计划

## 目标与边界

- 目标：用内部冻结的最优 E4 prediction model 生成、验证、提交并归档 A/B/C 与 D/E/F 预测；不将交付结果回写为 E1-E3 因果证据。
- 输入：S03 fallback 及任何经 gate 保留的 S05/S06/S07 release；S01 official contract；用户提供的 authenticated official bundles。
- 输出：`stages/08_competition_delivery/derived/<delivery_id>/`、`runs/`、`reports/`、submission ledger 与 disclosure package。
- 范围外：下载/登录/提交而无用户批准；按 hidden response 或文献逐 target 手调；final phase 新建未验证模型路线。
- 防御等级：`L3`；执行策略：`phase_boundary`；每次外部提交均为强制人工批准 gate。

## 必读依据

- [`2026 VCC 官方信息核验`](../../../参考资料库/竞赛资料/2026_VCC_官方信息核验_2026-08-30.md)：提交 shape、六指标、CLI、赛程、许可、披露和未解决冲突；执行时必须刷新。
- [`scPerturBench 方法卡`](../../../参考资料库/文献/scPerturBench/方法卡.md)：内部 OOD 证据不得被 leaderboard 替代。
- [根项目总设计](../../../.doc/design.md) 与 S01 frozen contract。

## Phase S08.0：实际开工调研与复用闸门

### S08.T00 推理、封装、校验与归档工具调研

- 作用域前置：先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)，并对执行当日官方工具和 selected model interface 做现场调研。
- 这是实际开工任务：重新核验项目存量、已安装 H5AD/sparse/streaming/provenance 工具、官方 `vcc-cli`/`cell-eval2`/bundle contract、release/changelog/license；S01 snapshot、R00 或 validation-era 决策不能替代 final-era refresh。
- 最低 capabilities：frozen model loader、chunked/streaming inference、deterministic seed routing、sparse H5AD assembly、raw-count/schema validation、official `.vcc` packaging/submission、artifact hashing、submission ledger、method disclosure/provenance。
- 选择要求：官方 CLI/scorer 和成熟 AnnData/sparse I/O 优先；项目 wrapper 只负责参数化重放、preflight、恢复和 provenance，不复制官方封装/评分实现。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`，含 validation/final 执行时版本、许可、contract diff、adapter seam 和新增资料归档。
- 完成闸门：所有 delivery capabilities 有 decision ID；官方工具与 selected model interface 可执行；`reuse_gate: pass`；自建 submission/scorer/streaming core 例外已人工批准。
- 禁止：沿用旧 CLI/scorer 状态或在 gate 前启动 S08.T01+；外部提交仍需独立人工批准。
- 检查点：更新本阶段 progress；通过后继续 S08.A。

## 固定提交不变量

- 当时官方 manifest 决定 partition、contexts、panel、gene order 和 shape。
- 当前公开合同的预期是三个 contexts、300 targets/context、400 cells/target/context、360,000 total cells、18,533 genes、raw finite non-negative integer-valued counts、无 NTC rows。
- `.obs['context']` 与 `.obs['target_gene']` 从官方 bundle/panel 直接传播；不猜测 context 身份、不把 guide ID 当 target。
- 输出使用稀疏矩阵并清除 explicit zeros；每 cell library、总 cells 与 stored entries 满足当时 CLI。
- 所有预测由冻结模型与声明的确定性/随机 generator 产生；不得人工逐 target 修改。

## Phase S08.A：验证阶段推理冻结

### S08.T01 提交前官方信息 refresh

- 重新核验 Rules、Data、Evaluation、FAQ、CLI guide、`vcc-cli`/`cell-eval2` release 与 authenticated bundle manifest。
- 对比 S01 contract，生成 `official_contract_diff.md/yaml`；旧快照保留不覆盖。
- U-001/U-002 或 ML-only 边界若仍影响实际流程，以当时 CLI dry-run/服务端为操作证据，并对科学/合规不明确项请求书面澄清。
- 完成闸门：无未处理的 submission-critical diff；需要改变持久流程时先修订 S01/S08 plan并人工批准。

### S08.T02 模型 release 选择与冻结

- 候选仅限 S03/S05/S06/S07 已获批 releases；用 S01 预注册内部 OOD protocol 比较，优先选择最简单的不劣 E4 model。对 `limited_transport`/`not_identifiable` target 保留 coverage/fallback 标记，不因必须提交而升级运输识别状态。
- leaderboard 可记录为黑箱竞赛信号，但不得反向决定 feature、graph、hyperparameter、blend 或 target-specific修正。
- 若使用 ensemble，weights 必须已在外部数据 inner/held-out protocol 冻结；无预注册 ensemble 则不临时混合。
- 预期产物：`selected_model.yaml`、selection report、完整 dependency manifest。
- 完成闸门：选择理由、fallback 与禁止变更项获用户批准。

### S08.T03 A/B/C 只读输入与 context invariants

- 读取官方 control H5AD、gene list、target panel、manifest；复跑 checksum/schema/raw-count/context/NTC tests。
- 任何 target context adaptation 只使用该 context NTC，且方法已在内部 LOCO 模拟中冻结；不得使用 response 或猜测 identity。
- 预期产物：inference input manifest、context summaries、model-ready NTC view；raw bundle保持不变。
- 完成闸门：A/B/C 标签、gene order、panel 与 selected model接口完全对齐。

### S08.T04 参数化批量推理

- 先运行 1 context × 少量 targets smoke test，再运行完整 `3 × 300`；每 target/context seed 由 master seed 的确定性派生规则生成。
- 每单元保存 status、runtime、seed、library/distribution diagnostics、checkpoint/model id；可从单元恢复，不覆盖失败。
- 输出 effect/distribution params 与 raw-count cells 分层保存；最终每单元恰好400 cells。
- 长任务前给出时间/CPU/GPU/磁盘预算并获批准；周期性报告进度。
- 每 chunk 释放 NTC/model intermediates；阶段末显式执行 CPU/CUDA cleanup。

### S08.T05 Raw-count 与生物/统计 sanity checks

- schema：finite/non-negative/integer、gene order、labels、cardinality、no NTC、sparse/stored entries、library limits。
- distribution：library-size、zero fraction、expressed genes、mean/variance、target knockdown direction（只作为 E4 模型内部 sanity，不用隐藏 GT）、与 target NTC 的距离/异常值。不得把这些 sanity 结果写成 E1/E2/E3 支持。
- clipping/rounding/sparsification必须是冻结模型 pipeline 的一部分；若改变输出含义，先在内部 folds 验证，不以文件压缩名义临时操作。
- 异常单元按预注册 fail/fallback rule重跑或回退 frozen model；不得人工修数。
- 预期产物：QC tables、failure/fallback ledger、final prediction H5AD。

### S08.T06 官方 dry-run 与封装

- 用锁版 `vcc prep ... --dry-run` 验证 final H5AD；通过后以同版本生成 `.vcc`。
- 记录命令参数、CLI version、bundle/panel/gene stamps、artifact checksum、size、stored entries 和 dry-run output。
- 完成闸门：内部 tests 和官方 dry-run 均通过；若服务端/CLI与文档冲突，保留证据并停止决定是否修订。
- 禁止：dry-run 未执行或失败时称 submission-ready。

### S08.A.R 验证发布真实产物复核

- 直接检查 selected release、input manifest、完整预测、QC/fallback、dry-run 与 package。
- 用户批准后才可执行外部提交。

## Phase S08.B：外部 validation submission 与冻结

### S08.T07 外部提交批准与 ledger

- 提交前展示 model/delivery ID、artifact checksum、partition/panel、dry-run、许可/ML-only、剩余额度和 fallback。
- 只有用户明确批准后执行 `vcc submit`；不请求或暴露凭据。
- ledger 至少记录 entry ID、UTC/CST time、artifact/model/data/seed/CLI versions、partition/panel/anchor set、状态、六项 raw/scaled/overall（可得时）与错误。
- 遵守当时每日/并发限制；线上失败不自动反复消耗额度。

### S08.T08 Validation 信号解释与 pipeline freeze

- leaderboard 仅记录 E4 workflow/competition evidence；不能单独支持 E1/E2/E3 或任何 scientific hypothesis。
- 不使用 A/B/C hidden response 做新的 target-specific拟合；若允许一次模型级选择，必须是 S01 预先注册的有限候选 release 选择并完整记录尝试总数。
- 冻结 `validation_pipeline_release`：代码、环境、model、generator、schema、seeds、configs、dependency graph和已知限制。
- 完成闸门：D/E/F 可通过替换 manifest/contexts/panel 参数重放，不存在 A/B/C 特例代码。

### S08.B.R 验证阶段人工闸门

- 用户确认 final strategy；任何 validation 后方法变化必须说明不是利用不可见 response 人工改预测，并重新完成内部 OOD 与 contract 验证。

## Phase S08.C：D/E/F 最终测试重放

### S08.T09 Final bundle refresh 与差异核验

- 用户提供 D/E/F bundle 后，重复 S08.T01/T03 的只读合同核验；保存最终 panel/manifest/checksum。
- diff 只允许 partition、contexts、panel 和官方声明字段变化；代码/schema/模型语义变化需强制人工升级。
- 当前公开日期仅作快照；以实际 bundle 和规则为准。

### S08.T10 冻结流程重放

- 使用 `validation_pipeline_release` 和同一参数化脚本重放 D/E/F；仅替换 official inputs。
- 完整执行 smoke→batch inference→QC/fallback→dry-run→package；不得查看/猜测 context identity。
- 失败修复只处理共享实现根因，并在 validation fixture 回归；不得添加 final-only target规则。

### S08.T11 Final 外部提交与归档

- 展示完整 preflight 后请求用户批准；明确只有最后一次 final entry 计入排名的当前规则风险。
- 提交后保存 ledger、receipt/status、artifact checksum和所有 stamps；保留未提交候选但不制造混淆。
- 生成 finalist-ready method disclosure：训练数据/许可、处理、模型/公开资产、统计/机制组件、training/inference、compute 和人工环节。

### S08.T12 最终可复现与科学审计

- 检查从 S00 release 到 final artifact 的 lineage、所有阶段 gate、运行完整性、license、失败/回退和隐藏标签边界。
- 输出 `final_workflow_audit.md` 与 `scientific_claims_ledger.md`。
- `workflow_verdict` 可记录 `submission_succeeded`/`submission_validated`；`scientific_verdict` 逐主张独立，不能由排名或成功上传自动升级。

### S08.C.R 最终真实产物复核

- 复核最终文件、官方 receipt、ledger、披露包与 claims ledger。
- 只有所有必需交付齐备且无未处理缺口，根进度才可登记项目已完成。

## 失败与恢复

- 每个 context/target/chunk 独立状态；重试不覆盖首次失败证据。
- schema/dry-run失败先定位 shared generator/label/gene-order 根因，在 fixture 回归后重放全部受影响单元。
- 外部服务/额度/凭据问题停止并交给用户，不伪造成功。

## 计划变更规则

官方合同、selected release、ensemble、generator/fallback、validation-to-final replay 或提交规则变化时先修订计划；实际 entry、hash、版本、scores 和错误写进度/ledger。
