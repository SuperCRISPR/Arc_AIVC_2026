# R00 文献、已有研究与可复用实现基线计划

## 目标与边界

- 目标：系统清点项目存量、参考资料、已安装依赖和当前成熟实现，建立阶段 capability map、复用 registry、缺口归档和可执行 gate 校验合同。
- 输入：根 `AGENTS.md`、根/阶段 design/plan、`参考资料库/`、当前仓库文件和本地依赖环境。
- 输出：`stages/R00_method_reuse_baseline/reports/`、`参考资料库/reuse_registry.yaml`、新增/修订方法卡与索引、`scripts/check_reuse_gate.py` 及其测试。
- 范围外：训练数据调研/下载；替 S00–S08 作永久方法选择；安装/下载第三方代码、权重或依赖而无批准；运行模型实验。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [根项目 AGENTS](../../../AGENTS.md)：递归作用域与 Method Research And Reuse Gate。
- [参考资料与方法复用治理规则](../../../参考资料库/rules.md)：调研、选择、归档、decision 与 gate 的权威合同。
- [参考资料库索引](../../../参考资料库/README.md) 与 [文献索引](../../../参考资料库/文献/README.md)。
- [`2026-09-03 因果矫正分析审阅`](../../../document/项目审计/已落实/2026-09-03_因果矫正分析审阅.md)：本轮需落实的 estimand、协变量、层级汇总、验证和图解释问题；审阅结论仍需一手来源与任务适配复核。
- 根/阶段 `design.md` 和 `AI_plan.md`：提取实际 capabilities，不能只按现有方法卡反推需求。

## 固定产物合同

- `reports/project_asset_inventory.md/yaml`：项目代码、配置、测试、报告和已有验证状态。
- `reports/installed_capability_inventory.md/yaml`：标准/native 能力和已安装依赖的版本/interface。
- `reports/stage_capability_map.md/yaml`：R00、S00–S08 的 capability、候选和缺口。
- `reports/current_method_survey.md`：实时一手论文、官方实现、版本、许可和候选比较。
- `参考资料库/reuse_registry.yaml`：稳定 method/capability → 方法卡、官方实现、license、stage role 映射。
- 新增/修订 `参考资料库/文献/<method_id>/方法卡.md` 及 `文献/README.md` 索引。
- `scripts/check_reuse_gate.py` 和 L3 tests：治理证据校验，不作科学判断。

## Phase R00.A：本地资产与能力基线

### R00.T01 项目与参考资料 inventory

- 目标：逐项清点当前项目代码/脚本/配置/测试/报告、idea 规则、方法卡、论文和官方资料入口。
- 方法：使用文件和文本搜索建立双向索引；区分 `mentioned_only`、`available_unverified`、`implemented`、`validated`，不得由文件名推断验证状态。
- 预期产物：`project_asset_inventory.md/yaml`、失效链接/缺失来源/重复方法记录。
- 完成闸门：每个阶段 plan 中点名的方法或工具都能指向本地资产/方法卡，或显式进入 gap list。
- 禁止：把历史报告或方法卡描述当作当前安装/版本证据。
- 检查点：更新 R00 progress，继续本 phase。

### R00.T02 本地依赖与 stage capability map

- 目标：从阶段实际任务提取 capabilities，并核验标准库、原生工具、Python/R/CLI 已安装依赖及版本。
- 最低覆盖：数据格式/QC/可视化与协变量角色；fold/metric/submission 及 biology-vs-protocol 拆分；representation/causal evidence、E1-E4、层级 effect synthesis、balance/DR、guide-IV 可行性和敏感性；baselines/count generator；predictive graph 与 interventional evidence；linear/nonlinear ODE；extensions；delivery/provenance。
- 预期产物：`installed_capability_inventory.md/yaml`、`stage_capability_map.md/yaml`。
- 完成闸门：每个 S00–S08 阶段都有 capability 清单、当前候选、已知缺口和对应 T00 最低刷新范围。
- 验证：版本来自实际环境命令或包 metadata；未安装项标为未安装，不触发安装。

### R00.A.R 本地基线审阅

- 直接复核真实文件、环境版本和 capability map；发现遗漏先修正，不进入外部候选冻结。

## Phase R00.B：实时一手调研与归档

### R00.T03 当前文献与官方实现调研

- 目标：对 capability gaps 和核心模型/评测能力执行实时一手调研，包括分层/混合效应 meta-regression、小样本不确定度、transportability、跨环境异质性/等效性、guide-level intervention calibration、anchor/bridge robustness 和未观测偏差敏感性。
- 来源优先：论文官方页面/DOI、作者或机构官方仓库、官方文档、release/changelog、license；二手材料只用于发现来源。
- 比较维度：科学问题和假设、interface、zero-shot/fold 泄漏适配、单细胞规模/稀疏性、维护状态、license、复现与 adapter 成本。
- 预期产物：`current_method_survey.md` 和规范化候选表，含检索日期、范围、来源和未决冲突。
- 完成闸门：每个高优先级 capability 至少有项目/本地方案或经过一手核验的成熟候选；无合格候选则明确 gap，不预写自建结论。

### R00.T04 新方法归档与既有方法卡补全

- 目标：将进入 shortlist 或影响设计的新方法按治理规则归档，并补足既有方法卡中缺失的 version/license/interface/stage role；本轮审阅点名的方法只有在完成官方来源、实现、许可、estimand 和识别条件核验后才建立方法卡或进入 registry。
- 目标范围：`参考资料库/文献/<method_id>/方法卡.md`、合法且必要的 `论文.pdf`、`参考资料库/文献/README.md`。
- 预期产物：方法卡、索引更新、archive change report。
- 完成闸门：每个 shortlist candidate 有方法卡和索引；官方链接有效；无第三方代码/权重/缓存进入参考资料库。
- 禁止：仅复制 abstract；把没有许可声明解释为可自由复用；为归档而下载无权保存的 PDF。

### R00.B.R 方法证据审阅

- 复核 shortlist、方法卡、官方入口、版本/许可和 gap；任何许可或来源未决项不得标为可执行候选。

## Phase R00.C：Registry、决策 interface 与校验器

### R00.T05 复用 registry 与阶段 T00 最低合同

- 目标：生成机器可读 registry，并为每个 stage T00 固定 capability 最低覆盖、必读方法卡和需要刷新的一手来源类别。
- Registry 只引用方法卡和稳定元数据；长调研事实留在 reports，防止 registry 膨胀。
- 预期产物：`参考资料库/reuse_registry.yaml`、registry schema/说明和 stage capability mapping。
- 完成闸门：method ID 唯一；每个路径可解析；每个候选记录官方实现、version/commit、license、interface、stage role、last verified；未知值显式为 unresolved。
- 禁止：把 registry 候选状态写成 stage 的最终选择；T00 不得因 registry 存在而跳过。

### R00.T06 复用 gate 校验器

- 目标：用标准库优先的最小脚本验证治理结构，而不判断科学正确性。
- 检查：每个直接 stage 的 root-AGENTS symlink、禁止的 override/深层 AGENTS、symlink drift 和 instruction size budget；stage T00/task-ID 对齐；T01+ 与 `reuse_gate` 状态；decision schema；方法卡/本地路径/索引；shortlist 归档；`minimal_new` 字段和批准。
- 实现 seam：输入项目根和可选 stage ID，返回结构化 violations 与非零退出码；扫描/解析细节封装在 module 内。
- 预期产物：`scripts/check_reuse_gate.py`、fixtures 与 tests、使用说明。
- L3 验证：合法 fixture；缺 T00；缺失/漂移 symlink；嵌套 override/深层 AGENTS；instruction size 超限；断链方法卡；gate fail 却推进 T01；不完整 minimal_new 等反向测试。
- 完成闸门：所有反向 fixture 被拦截，合法 fixture 通过；脚本对当前未执行项目只报告符合其真实状态的预期结果。
- 自身复用决策：在实现前于 R00 report 记录标准库/YAML parser 等候选及选择，避免治理脚本成为无记录例外。

### R00.T07 R00 发布冻结与人工闸门

- 目标：冻结 versioned reuse baseline，供所有 stage T00 读取。
- 预期产物：R00 release manifest、inventories、capability map、survey、registry、方法卡/index、validator/test report、limitations。
- 完成闸门：所有产物可追溯且相互链接；校验器通过；每个阶段 T00 有明确最低覆盖；真实版本/许可未决项被标为 gap。
- 科学边界：R00 不评价模型效果，不授权任何 stage 跳过真实调研或直接使用某方法。
- 检查点：停止并请求用户批准 R00 baseline；批准后才能把根进度路由到 S00。

### R00.C.R 阶段真实产物复核

- `review_performed` 必须基于实际 registry、方法卡、环境 inventory、validator 和 tests，而非计划文本。
- 复核通过只代表复用治理可执行，不代表候选方法科学有效。

## 计划变更规则

Capability 分类、归档范围、registry/decision interface、选择顺序或 gate 判据变化时先修改根计划、本计划和治理规则，并在 progress 记录 amendment。实际候选、版本、许可、搜索结果和 release ID 写入 reports/progress，不写入本计划。
