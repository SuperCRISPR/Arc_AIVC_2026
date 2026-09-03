# S07 知识先验与 Context 扩展门控计划

## 目标与边界

- 目标：仅对 S05/S06 冻结的 E4 预测残差明确触发的扩展分支做受控实验，并独立决定保留或关闭；不重新定义 S02 E1-E3 或 S04 interventional evidence。
- 输入：S03 fallback、S05/S06 frozen model/diagnostics、S04 graph assets、S01 comparison protocol。
- 输出：`stages/07_gated_extensions/runs/<branch>/`、`derived/<extension_release_id>/`、`reports/`。
- 范围外：无触发的模型搜索；自由 per-context GRN；用知识/论文手工修订 target outputs；未获许可的代码/权重。
- 防御等级：`L3`；执行策略：`phase_boundary`。每个 branch 本身是强制人工批准 gate。

## 必读依据

- [`知识先验与现成方法复用`](../../../参考资料库/idea库/05_知识先验与现成方法复用.md)。
- [`AROMA`](../../../参考资料库/文献/AROMA/方法卡.md)、[`TxPert`](../../../参考资料库/文献/TxPert/方法卡.md)、[`CellNavi`](../../../参考资料库/文献/CellNavi/方法卡.md) 方法卡：只按各自复用边界启用。
- [`MEGA-ODE 方法卡`](../../../参考资料库/文献/MEGA-ODE/方法卡.md)：MoE/图向量场的后备参考。
- [`2026 VCC 官方信息核验`](../../../参考资料库/竞赛资料/2026_VCC_官方信息核验_2026-08-30.md)：ML-only、许可与披露边界。

## Phase S07.0：实际开工调研与复用闸门

### S07.T00 已触发扩展的专项调研与复用决策

- 作用域前置：先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)，并只围绕 S05/S06 frozen trigger 调研；无 trigger 时记录 `not_triggered`，不得扩张搜索。
- 这是实际开工任务：对获准评估的分支刷新项目存量、本地依赖、AROMA/TxPert/CellNavi/Geneformer/MEGA-ODE 等方法卡及当前论文、官方实现、weights/license、维护状态和竞赛适用性；R00 和旧方法卡不替代现场核验。
- 最低 capabilities 按 trigger 选择：real/random prior；frozen embedding/external baseline；NTC-driven low-rank adapter；small MoE/gating；CRISPRa/heterogeneous-modality encoder；组合与 license disclosure。
- 选择要求：成熟官方实现或模型先作为 baseline/feature/adapter 候选；项目只实现受限任务 seam。每个分支单独 decision，不能用一个总 gate 覆盖多个扩展。
- 预期产物：`reports/method_reuse_review.md`、每个触发 capability 的 `reports/reuse_decision*.yaml`、新增方法卡/索引和许可/ML-only 澄清项。
- 完成闸门：所有触发分支各有 decision ID；未触发项明确；许可与 weights 使用边界已解决；可执行分支 `reuse_gate: pass`；自建 adapter/MoE 核心已人工批准。
- 禁止：为“看看是否有效”跳过 trigger 或调研；任一分支 gate 未通过时进入该分支的 S07.T03–T08 实现。
- 检查点：更新本阶段 progress；通过后继续 S07.A。

## Phase S07.A：触发与合规

### S07.T01 Extension trigger dossier

- 汇总 S05/S06 frozen residuals、support slices、state dependence、graph ablations、optimization/numerical failures和资源。
- 为每个候选分支记录：exact sub-hypothesis、trigger evidence、替代解释、baseline、主要指标、参数/训练预算、停止/回退规则、许可需求。
- 分支状态只能是 `not_triggered`、`proposed_for_approval`、`approved`、`rejected`；无自动开启。
- 预期产物：`extension_trigger_dossier.md/yaml`。
- 完成闸门：用户逐分支批准；未批准分支不创建实现。

### S07.T02 规则/许可与 ML-only 边界

- 对外部代码、模型、权重、知识图和数据逐项登记来源、版本、许可、商业/竞赛用途与披露要求。
- 若最终预测含纯未学习机制、人工融合权重或 target-specific literature rule，需先取得主办方对 ML-only 边界的书面澄清；没有澄清不得进入 submission release。
- 预期产物：`license_and_rules_ledger.yaml`、必要的官方澄清记录。
- 完成闸门：所有 approved branch 可合法使用且能在 finalist method description 中披露。

### S07.A.R 阶段审阅

- 复核 trigger、预算、许可和官方边界；仅批准的分支进入 S07.B。

## Phase S07.B：独立分支实验

### S07.T03 知识图软先验分支

- 触发：S04/S06 data-only 图稳定但长尾/稀疏区域有系统误差，且真实 prior 有明确覆盖。
- 比较：no prior、real prior、多个 degree-matched randomized priors；模型容量、fold、seed、budget相同。
- prior 只改变 mask/penalty/init 中一个预注册位置，避免多处同时注入后无法归因。
- 保留条件：真实 prior 对随机图分布有稳定 practical gain，且不损害多数 contexts/主要指标。
- 输出：graph ablation report；不把 prior-supported edge 称为因果边。

### S07.T04 冻结 embedding / 外部预测 baseline 分支

- 触发：未见 perturbation/长尾 target 是主要 residual，且相关 embedding/模型有合法本地资产。
- 首先把 Geneformer/TxPert/AROMA/其他方法作为外部 baseline 或冻结 target feature；不得直接替换主模型 training labels。
- 比较简单 one-hot/可观测 gene features；embedding 只在 training fold 接入，任何 fine-tuning 都需独立预算和计划修订。
- 保留条件：严格 LOPO/双留出有稳定增益且 gene-level方向/覆盖不退化。

### S07.T05 低秩 NTC-driven adapter 分支

- 触发：共享 `G_predictive` 的 E4 residual 在 outer-fold-free 诊断/inner folds 中可由 NTC state 稳定预测，且不能由 E1/E3 support、protocol、selection/interference、generator 或 numerical 问题解释。
- 形式：`f_c=f_shared+R_phi(h_c;z,u)`，`h_c` 只由 NTC pool 得到；rank、norm、amplitude 强约束，默认初始化为零。
- 对照：adapter off、受限 adapter、容量匹配但 context-permuted adapter；自由 per-context model 只作训练内上限。
- 保留条件：多数 LOCO contexts 稳定增益，state permutation 显著破坏，且机制共享部分仍占主导。
- 禁止：用真实 context 名称/ID embedding；outer validation residual 训练 adapter 后在同 fold 重新评分。

### S07.T06 小型 MoE 分支

- 触发：受限 adapter 已严格运行仍有多模态、NTC 可预测 residual，且 S07.T05 证据支持 context conditioning；否则 `not_triggered`。
- gating 只看 NTC-derived state；experts 数、容量和 entropy/load balance 受限，与单模型总参数/训练预算对账。
- 必须检测 expert collapse、context memorization、seed instability；未见 context 的 gating 不能依赖标签。
- 保留条件：超过受限 adapter 而非只超过 shared baseline，并在双留出不退化。

### S07.T07 CRISPRa/异模态辅助分支

- 默认角色：`eval_only` 或方向分离的辅助 representation/pretraining；不与 CRISPRi condition effects 直接平均。
- 触发：主模型对相关 TF/program 的结构性预测需要独立方向证据，且数据许可/协议明确。
- 任何联合训练必须显式编码 modality/direction，并与 CRISPRi-only 做消融；KO/药物/组合扰动同理需另立计划修订。
- 保留条件：CRISPRi OOD 主指标不退化且辅助 estimand 单独定义。

### S07.T08 分支运行、资源与失败记录

- 每个 branch 独立 run namespace/config/seed/checkpoint，先 dry run 再请求长时资源批准。
- 分支间不得共享 outer validation 选择结果形成隐式多重搜索；报告尝试总数与未运行分支。
- 每个 heavy run 后释放对象并执行 CPU/CUDA cleanup。
- 失败按 cause ledger 记录，不能删除失败 branch 或只展示赢家。

### S07.B.R 分支级真实产物复核

- 每个 branch 单独记录 engineering verdict、exact scientific sub-claim、证据状态与保留/关闭建议。
- 用户可批准一个、多个或零个分支；关闭是有效结果。

## Phase S07.C：扩展发布

### S07.T09 冻结可保留扩展

- 只合并已独立通过 gate 的分支；组合后再与各单分支/基础模型做一次预注册确认，防止相互抵消或隐式增容。最终组合只改变 E4 prediction pipeline，不回写 E1/E2/E3 或直接边状态。
- 输出 model card、license/method disclosure draft、configs、predictions、metrics、resource 与 release manifest。
- 完成闸门：所有组件合法、可重放、可单独关闭；没有人工 target-specific patch。

### S07.C.R 最终阶段审阅

- 复核组合发布与基础模型公平比较；用户批准后可供 S08 选择。
- 禁止结论：未触发/未运行分支保持 `not_tested`，不能写成失败；保留分支不产生因果证明。

## 计划变更规则

任何新分支、触发、预算、合规边界、比较对象或保留条件必须先修订本计划并由用户批准；实际 branch 状态、run 和结果写进度/报告。
