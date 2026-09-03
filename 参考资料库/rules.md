# 参考资料与方法复用治理规则

本文件是项目中“先调研、后复用、最后才最小自建”的权威执行合同。根 `AGENTS.md` 只保存触发指针；本文件定义具体流程、产物和判据。它适用于数据处理、可视化、统计估计、评测、GRN、模型、solver、decoder、calibration、提交工具及其他会影响科学或工程结果的方法选择。

## 1. 两层调研制度

### R00：项目级复用基线

R00 对现有代码、参考资料库、已安装依赖、论文官方实现和许可证做一次系统 inventory，生成复用 registry 与阶段能力映射。它减少后续重复搜索，但只是一份有日期的基线。

### Stage T00：实际开工调研

S00–S08 每个阶段在真实工作开始时都必须执行自己的 `T00`：结合当时的输入、任务 interface、现有代码、依赖版本和最新一手资料重新判断。以下内容都不能替代 T00：

- R00 的历史 registry；
- 阶段 plan 中预先列出的“必读依据”；
- 记忆、聊天摘要或旧方法卡；
- 另一个阶段的复用决策；
- “这个方法很常见”或 agent 自身知识。

阶段内若增加新能力、改变核心方法、替换依赖、扩大 estimand 或发现原选项不兼容，必须为受影响能力创建新的 decision revision，并在继续实现前重新通过 gate。

## 2. T00 必须完成的调查

对该阶段每个将被实现或选型的 capability，依次检查：

1. **项目存量**：搜索当前仓库的代码、脚本、配置、测试、历史报告和上游冻结资产。
2. **本地环境**：核验标准库、原生平台能力和已经安装的依赖；记录实际版本及可用 interface。
3. **参考资料库**：完整阅读与 capability 相关的方法卡、idea 规则和其直接链接的必要论文/官方说明。
4. **实时一手调研**：检索当前论文、官方文档、官方仓库、release/changelog 和 license；不得只依赖二手综述、搜索摘要或旧快照。
5. **任务适配**：逐候选比较输入输出、统计假设、fold/leakage 边界、单细胞规模、稀疏/内存特性、许可、维护状态和复现成本。
6. **最小试配**：对拟复用候选定义最小 adapter seam 与小型验证；尚未实际试配时标记 `selected_pending_validation`，不能称为已经可复用。

调研对象是“完成本任务所需的 capability”，不是漫无边际的同领域论文罗列。搜索过程必须记录检索日期、范围和未解决冲突。

## 3. 选择顺序

在满足科学假设、interface、许可证、泄漏边界和资源约束的前提下，按以下顺序选择：

1. `reuse_project`：直接复用项目现有、已验证的实现；
2. `reuse_standard_or_native`：标准库或原生平台能力；
3. `reuse_installed`：已经安装且维护成熟的依赖；
4. `adapt_registered`：参考资料库登记的论文官方实现，通过窄 adapter 接入；
5. `adapt_newly_verified`：T00 新发现并完成归档、许可与版本核验的成熟实现；
6. `minimal_new`：成熟候选均有具体不兼容证据时，编写满足当前 interface 的最小新实现；
7. `defer`：当前无法合法、可靠或经济地实现时，明确推迟。

“优先复用”不等于盲目复用。出现以下情况时可以拒绝成熟实现：许可证不允许；接口无法满足 zero-shot/fold 隔离；实现引入隐藏标签泄漏；数值/数据规模不适配；依赖已停止维护且存在实际风险；改造成本高于一个有清晰 ceiling 的最小实现。拒绝理由必须有证据，不能只写“太复杂”“不方便”或“自己写更快”。

## 4. 统一复用决策 interface

每个阶段必须生成：

- `reports/method_reuse_review.md`：给人阅读的调研与比较报告；
- `reports/reuse_decision.yaml`：给 agent/校验器读取的规范化决策；
- 必要时的 `reports/reuse_decision.<revision>.yaml`：方法范围变化后的修订，旧版本不覆盖。

每个 decision 使用稳定 ID：`<stage>-RD-<capability>`，例如 `S06-RD-ode_solver`。至少包含：

```yaml
schema_version: 1
decision_id: SXX-RD-capability
stage_id: SXX
task_id: SXX.T00
capability: short description
reviewed_at: YYYY-MM-DD
reuse_gate: pass_or_fail
project_assets_checked: []
installed_options_checked: []
reference_cards_read: []
live_primary_sources: []
candidates: []
selected_route: reuse_project_or_reuse_standard_or_reuse_installed_or_adapt_registered_or_adapt_newly_verified_or_minimal_new_or_defer
selected_asset: null
adapter_seam: null
license_verdict: compatible_or_incompatible_or_unresolved
validation_status: planned_or_passed_or_failed_or_deferred
archive_updates: []
rejected_candidates: []
minimal_new_exception: null
approvals_required: []
unresolved_items: []
```

完整模板见 [`templates/reuse_decision.template.yaml`](templates/reuse_decision.template.yaml)。人类报告模板见 [`templates/阶段方法调研模板.md`](templates/阶段方法调研模板.md)。

## 5. `reuse_gate: pass` 的必要条件

只有同时满足以下条件才可进入该阶段 `T01+`：

- 每个计划实现的 capability 都有 decision ID；
- 项目存量、本地依赖、阶段必读方法卡和实时一手来源均已实际检查；
- 每个入选候选记录官方来源、版本或 commit、license、可复用 interface 和必要 adapter；
- 新进入 shortlist 或实际使用的论文/方法已归档并加入索引；
- 被拒绝的成熟候选有任务相关证据；
- `minimal_new` 记录自建范围、ceiling、升级/替换条件及必要人工批准；
- 所有许可、版本、输入输出或科学假设冲突已经解决，或明确列为阻塞项；
- stage progress 引用实际 review/decision 文件，而非只声称“已阅读文献”。

存在未解决的许可、隐藏标签、核心 interface 或统计假设冲突时，gate 必须为 `fail`。

## 6. 新文献和方法的归档规则

### 何时必须归档

任何新论文、方法、模型或软件只要满足以下任一条件，就必须在影响 plan、代码、配置或实验前归档：

- 进入候选 shortlist；
- 被用于支持或拒绝一个方法选择；
- 提供将被复用的实现、参数化、loss、metric、solver、数据处理或验证协议；
- 影响科学解释、适用边界或失败原因；
- 将在报告、模型卡或 finalist disclosure 中引用。

搜索结果中在摘要层面即可明确排除、且未影响决策的条目不必生成完整方法卡，但必须在阶段调研报告中保留链接和排除理由。

### 放置与索引

- 一般论文/方法：`参考资料库/文献/<method_id>/方法卡.md`；
- 合法且确有必要的本地论文：同目录 `论文.pdf`；PDF 不是必需，方法卡与官方入口是必需；
- 官方比赛规则：`参考资料库/竞赛资料/`；
- 跨方法设计原则：仅在确有复用价值时进入 `参考资料库/idea库/`；
- 每个新方法卡必须加入 [`文献/README.md`](文献/README.md)，并由使用它的 stage plan 或 reuse decision 链接。

第三方代码、模型权重、数据快照、缓存和嵌套 Git 仓库不放进参考资料库。实际复用资产进入项目的 dependency/cache/vendor 路径前，仍需单独审批和 provenance 记录。

### 方法卡最小内容

使用 [`templates/方法卡模板.md`](templates/方法卡模板.md)，至少记录：稳定 method ID；科学问题；关键假设；论文 DOI/官方页面；官方仓库；当前版本/commit；license；维护状态；可复用 module/interface；必要 adapter；本项目适用/禁用边界；验证要求；关联阶段与 decision ID；最后核验日期。

## 7. `minimal_new` 自建例外

自建前必须完成 deletion test：若删掉拟建 module，复杂性是否会重新散落到多个调用方？只有能形成小 interface、集中实现复杂性并被多个调用方/测试复用的 module 才值得自建；一次性的浅封装优先用 stage script 或直接调用成熟依赖。

`minimal_new_exception` 至少说明：

- 被检查的成熟候选及各自不兼容证据；
- 当前任务所需的最小 interface；
- 新实现不会覆盖的范围；
- 已知性能/规模 ceiling；
- 触发改用成熟实现或重构的具体条件；
- 正确性测试与对成熟实现/解析结果的对照；
- 人工批准记录（核心模型、算法、scorer、solver、格式转换器必须有）。

自建实现不得使用与成熟项目相同的名称来暗示兼容，也不得复制许可证不兼容的代码片段。

## 8. 阶段与实现的关系

- Stage plan 中的“必读依据”和 capability 清单是最低检查范围，不是预先指定唯一实现。
- T00 产生实际决策；T01+ 只实现已通过 gate 的选择。
- 实现发现 candidate 不适配时，先更新 review/decision 与必要方法卡，再修改 plan 或代码。
- release review 必须核对代码/依赖是否与 reuse decision 一致；未登记的科学 module 不得进入 release。

## 9. 作用域和自动校验

根 `AGENTS.md` 是 `stages/**` 的权威 project instruction。每个直接 stage 目录用 `AGENTS.md -> ../../AGENTS.md` 直接暴露同一根文件，解决非 Git 工作区从子目录启动时的发现问题，不复制或分叉规则。R00 将建立校验器，至少检查：

- 每个直接 stage 的 `AGENTS.md` 都是解析到项目根 `AGENTS.md` 的相对 symlink；不存在 `AGENTS.override.md`、更深层 `AGENTS.md` 或 stage-specific policy；
- 根 AGENTS 在可能的 root+stage 双重发现情形下仍低于 Codex project instruction 字节上限；
- 每个 stage plan 含 `T00` 且 progress 使用同一 task ID；
- `T01+` 的执行状态不能领先于 `reuse_gate: pass`；
- reuse decision 引用的方法卡、官方链接和本地路径存在；
- shortlist 新方法已进入文献索引；
- `minimal_new` 的理由、ceiling、替换条件和批准字段完整。

校验器通过是 workflow evidence，不代表方法科学上正确或实现已经验证。

## 10. 变更控制

本文件只在复用流程、选择顺序、归档范围、decision interface 或 gate 判据发生持久变化时修订。修订必须同步更新根/阶段 plan 和 progress amendment 记录。实际搜索结果、候选版本、许可判断和选择结果属于 R00 或 stage-owned reports，不写回本规则。
