# S05 共享线性机制模型计划

## 目标与边界

- 目标：以最小共享模型检验 NTC 初态 + shared mechanism 是否带来超过 S03 基线的稳定 LOCO 增益。
- 输入：S00–S04 获批 releases，尤其是 S03 fallback 与 S04 fold-specific candidate graphs/evidence release。
- 输出：`stages/05_shared_linear_model/runs/`、`derived/<linear_release_id>/`、`reports/`、`src/arc_vcc/models/linear_shared.py`。
- 范围外：nonlinear adapter/MoE；把单终点参数解释为唯一 GRN；按 leaderboard 调参。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [`机制与状态解耦`](../../../参考资料库/idea库/01_机制与状态解耦.md)：共享参数、NTC 初态和 adapter 门槛。
- [`GRN-ODE 与有限时间边界`](../../../参考资料库/idea库/03_GRN_ODE与有限时间边界.md)：单终点解释范围和 one-step 对照。
- [`基线、验证与复杂度门控`](../../../参考资料库/idea库/04_基线_验证与复杂度门控.md)。
- [`PerturbODE 方法卡`](../../../参考资料库/文献/PerturbODE/方法卡.md)：结构化 ODE 接口，仅作实现参考。

## Phase S05.0：实际开工调研与复用闸门

### S05.T00 共享线性机制与数值模块调研

- 作用域前置：先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)，再以 S02/S03/S04 frozen interfaces 为真实需求。
- 这是实际开工任务：调查项目存量、当前线性系统/矩阵指数/低秩优化 modules、PerturbODE 及相近共享机制研究的最新论文和官方实现；R00 和既有模型公式不能替代实现可复用性核验。
- 最低 capabilities：shared finite-time operator、stable linear ODE/matrix exponential、low-rank/sparse parameterization、soft intervention mapping/可选 E2 calibration、hierarchical evidence interface、weighted losses、anchor/bridge robustness、analytic tests、checkpoint/trainer integration。
- 选择要求：优先成熟 linear algebra、optimization、solver/training modules；项目新增部分集中于“跨 context 共享 + NTC state + candidate mask + OOD protocol”的 adapter seam。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`，含 module interface、版本、license、数值特性、可复用代码边界和新增方法卡。
- 完成闸门：每个模型/数值/训练 capability 有 decision ID；PerturbODE 等候选已作实际 task-fit 比较；`reuse_gate: pass`；核心模型或 solver 的 `minimal_new` 已人工批准。
- 禁止：仅因公式不同就从零实现 solver/optimizer；gate 未通过时进入 S05.T01+。
- 检查点：更新本阶段 progress；通过后继续 S05.A。

## 注册 estimand 与主比较

- 主 estimand：在未见 cell context、仅暴露其 NTC 时，target perturbation response 相对 S03 最强简单 baseline 的 context/perturbation-level OOD 预测差异。
- 主验证：LOCO；LOPO 和双留出为结构泛化辅助证据。
- 主要指标族与 practical effect 使用 S01/S03 冻结合同；不得在结果后改选。
- 共享假设：模型动力学/finite-time operator 参数跨训练 contexts 共享；context 信息只由 NTC-derived state 与明确技术 covariates 进入；`G` 的稳定机器语义为 `G_predictive`，S04 干预总效应证据不自动成为直接边。

## Phase S05.A：实现与数值验证

### S05.T01 线性模型接口与参数化

- 实现两种容量匹配形式：直接 finite-time reduced-rank operator（S03 B3）与稳定线性 ODE operator；二者共享 state、intervention、decoder 和 parameter budget 对账。
- `u_a=-rho q_a`：`q_a` 来自 gene-program mapping；`rho_source` 只能是 registry 实测、S02 已批准 E2 calibration 或 bounded/regularized fold-learned，且逐 target 记录 uncertainty；E2 不可识别时不得伪造校准值。
- `G_predictive/Q` 受 S04 candidate mask/soft penalty 约束，同时保留 data-only/no-prior 对照；不允许 context-specific `G_c`，也不把 S04 total-effect evidence 直接复制为参数。
- 稳定性参数化或约束必须有明确目的：避免积分/矩阵指数数值发散，而非宣称长期稳态真实。
- 预期产物：接口文档、参数容量表、默认/无图 configs、实现代码。
- 完成闸门：输入输出 shape、mask、rho bounds、共享参数不变量可自动检查。

### S05.T02 损失与 condition weighting

- 最小训练目标：S02 condition effects 的 inverse-variance/支持域加权误差 + target direction consistency + sparsity/low-rank；需要 cell-distribution loss 时使用训练 fold raw distributions并单独报告。
- S02 balance weights、dataset sampling weights、uncertainty weights 分开存储，组合后诊断 range/ESS；不能用一个乘积隐藏单域支配。
- `L_bridge` 只有在 shared_evidence_candidate 上启用；gamma 只在 inner LOCO-like folds 搜索。
- 语义分离：evidence weights 定义目标总体/可信度，bridge 定义对预注册 shift 的鲁棒约束；必须运行 `weight_off/bridge_off/both_on` 与必要的单组件对照，报告 loss scale、gradient、ESS 和 environment influence。
- Anchor contract：默认技术 anchors 为 study/protocol/batch；cell line/lineage 不作为默认 nuisance anchor。biological-context anchor 仅作为单独压力测试，需检查是否抹除 NTC state effect。
- 预期产物：loss/anchor contract、weight-bridge interaction audit、合成 known-effect/biological-effect-modifier tests。
- 完成闸门：关闭某项时其梯度/贡献为零；极端权重 fail closed；每项损失尺度可追踪。

### S05.T03 解析/合成数值测试

- 线性系统的矩阵指数/积分结果与解析解一致；direct operator 与 ODE 在构造等价参数下终点一致。
- 测试 zero intervention、rho=0/边界、单 program、带反馈稳定矩阵、stiff/near-unstable、mask、batch dimension、CPU/GPU（可用时）一致性。
- 注入 state-dependent response、context-specific mechanism 和 batch interaction 的反例，确认 state swap/残差诊断能区分至少部分失败模式。
- 完成闸门：核心数值性质与反向失效测试通过；数值容差和 dtype 明确。

### S05.T04 单 fold dry run 与资源边界

- 先用小 target/program/fold 跑完整 train→predict→count generator→metrics；记录 runtime/peak memory/solver diagnostics。
- checkpoint 包含 model/optimizer/config/fold/seed，并验证中断恢复不会重置数据顺序或重复 fold fit。
- 每个 run 结束显式释放 model/optimizer/dataloaders/AnnData 和 metric payload，执行 CPU/CUDA cleanup。
- 完成闸门：dry run 可重放且通过 S01 schema；资源估计获批准后才启动长时全 fold。

### S05.A.R 阶段审阅

- 复核代码、解析测试、loss/weight、共享不变量、dry run 与资源预算。

## Phase S05.B：核心假设评测

### S05.T05 全 LOCO/LOPO/双留出运行

- 所有 preprocessing/graph/model/hyperparameter fit 严格在 fold 内；validation context 只提供 NTC。
- 每 fold 至少多 seed 或与优化随机性匹配的重复；保存预测、raw generator output、六指标、辅助指标、资源和失败状态。
- 长任务按 fold/seed checkpoint；单个失败不删除其他结果，统一报告缺失。
- 完成闸门：所有预注册 fold 有有效结果或有原因 ledger；无 hidden response access。

### S05.T06 State/共享性反事实诊断

- state swap/context permutation：错配 NTC 后应改变预测并通常降低与正确 response 的一致性；结果需与 no-state 模型比较，避免“必然变化”假阳性。
- shared vs free per-context：free 模型仅作为训练 contexts 内容量/拟合上限，不能在真正未见 context 直接使用；比较参数量与训练内/LOCO 行为。
- residual analysis：区分可由 NTC state 预测的残差、target-specific残差、protocol/batch残差、generator/metric geometry。
- gamma/anchor 只按预注册 inner-fold 规则冻结；S07 adapter 或 outer validation 的结果不得回流重调本阶段后再在同一 outer fold 重评分。
- 预期产物：诊断报告与 residual dataset，禁止在同一 outer validation fold 上训练后再把结论用于重拟合并重新评分。

### S05.T07 与 S03 基线的预注册比较

- 使用 S01/S03 protocol 汇总 context/target-level paired differences、bootstrap CI、fold/seed一致性和 failure slices；辅助报告同-study LOCO、同-cell-model 跨-study、leave-one-study-out/lineage-out，用于判断 bridge 改善的是协议鲁棒性还是生物运输。
- 同时比较 direct one-step/reduced-rank 与线性 ODE，分离“共享低维映射”与“连续参数化”的贡献。
- 报告参数量、训练成本、推理成本和稳定性；复杂度是保留模型的成本项。
- 完成闸门：不因单 context、单 metric 或单 seed 的最好结果晋级。

### S05.T08 原因分析与科学闸门

- 每个失败/不增益分别评估：implementation、numerical/optimization、data/support、protocol/batch、representation/decoder、count generator、metric geometry、共享假设。
- 每项 cause 标为 `supported`、`ruled_out` 或 `unresolved` 并引用证据。
- `scientific_verdict` 必须命名子主张，例如“受限共享有限时间 operator 能否改善未见 context 预测”，状态用 `supported/weakened/unresolved/falsified`。
- `falsified` 仅在注册 estimand 被测且上述替代原因有证据排除时使用。
- 路由：稳定增益且诊断一致可推荐 S06；线性表达能力可能成为唯一主要未决原因时可申请 S06 诊断性升级；否则保持 S03/S05 简单路径并停止复杂化。
- 预期产物：`reports/core_hypothesis_gate.md` 与 machine-readable decision。
- 完成闸门：用户审阅并决定 S06 是否获授权。

### S05.B.R 阶段真实产物复核

- 复核真实 checkpoints、predictions、fold manifests、metrics、diagnostics、cause ledger 和 gate report。
- 根进度只在用户决定后切换到 S06 或 S08。

## 计划变更规则

estimand、模型容量、共享边界、loss、主比较或科学 gate 改变时先修订计划；实际 scores、seeds、run IDs、资源和 verdict 写入进度/报告。
