# S06 共享非线性 GRN-ODE 计划

## 目标与进入条件

- 目标：检验受候选图约束的共享 nonlinear predictive ODE（`G_predictive`）是否在严格 OOD 评测中稳定优于 S03/S05 与同容量 one-step。
- 进入条件：S05 核心闸门明确授权并记录理由；未授权时不得启动本阶段。
- 输入：S00–S05 frozen releases 与 S01 comparison protocol。
- 输出：`stages/06_shared_nonlinear_model/runs/`、`derived/<nonlinear_release_id>/`、`reports/`、`src/arc_vcc/models/grn_ode.py`。
- 范围外：context adapter/MoE、知识先验增益结论、真实轨迹/吸引子声明、leaderboard 驱动调参。
- 防御等级：`L3`；执行策略：`phase_boundary`。

## 必读依据

- [`GRN-ODE 与有限时间边界`](../../../参考资料库/idea库/03_GRN_ODE与有限时间边界.md) 与 [`复杂度门控`](../../../参考资料库/idea库/04_基线_验证与复杂度门控.md)。
- [`PerturbODE 方法卡`](../../../参考资料库/文献/PerturbODE/方法卡.md)：主线结构参考。
- [`MEGA-ODE 方法卡`](../../../参考资料库/文献/MEGA-ODE/方法卡.md)：图向量场参考；MoE 仍留在 S07。

## Phase S06.0：实际开工调研与复用闸门

### S06.T00 非线性 GRN-ODE、solver 与分布模块调研

- 作用域前置：先读取 [根 AGENTS](../../../AGENTS.md)、根 plan/progress 与 [`参考资料库/rules.md`](../../../参考资料库/rules.md)，并以 S05 gate 指出的真实残差/升级理由限制调研范围。
- 这是实际开工任务：刷新项目存量、已安装 autodiff/ODE/distribution modules，以及 PerturbODE、MEGA-ODE 和当前相关研究的官方实现、solver backend、version/license、维护和规模特性；R00 与 S05 结论都不能自动指定 S06 implementation。
- 最低 capabilities：nonlinear vector field、ODE integration/adjoint、stiffness/failure diagnostics、low-rank/mask parameterization、MMD/Sinkhorn/count likelihood、distribution decoder、capacity-matched one-step、training/checkpoint。
- 选择要求：成熟 ODE solver、autodiff 和 distribution modules 优先；项目实现聚焦共享 vector field、任务 adapter、比较/诊断，而非重写数值积分库。
- 预期产物：`reports/method_reuse_review.md`、`reports/reuse_decision.yaml`，含 solver/模型候选矩阵、数值验证计划、版本/license、adapter seam 和新增方法卡。
- 完成闸门：所有启用 capabilities 有 decision ID；ODE/one-step 公平接口明确；`reuse_gate: pass`；自建 vector field 核心/solver/loss 例外已人工批准。
- 禁止：把已有方法卡或 S05 授权当成跳过现场调研的许可；gate 未通过时进入 S06.T01+。
- 检查点：更新本阶段 progress；通过后继续 S06.A。

## Phase S06.A：模型与数值合同

### S06.T01 Nonlinear shared vector field

- 实现 `A σ(Bz+b+u)-Λz` 或经 S05 修订批准的等价低秩稀疏参数化；state/program 维度、rank、activation、decay 与 candidate mask 显式配置。结果对象命名为 `G_predictive`，不得把它自动导出为 `direct_regulatory_edge`。
- context identity 不进入 embedding；目标 context 只通过 NTC-derived initial-state samples进入。
- soft intervention 映射/rho 与 S05 共用，避免通过换 intervention 定义制造不公平增益。
- 预期产物：模型接口、参数容量表、共享不变量测试、配置 schema。
- 完成闸门：所有跨 context 共享参数可枚举；不存在未声明的 per-context trainable path。

### S06.T02 ODE solver 与数值边界

- 优先复用成熟 solver；在小系统比较固定步长与自适应方法，选择依据 accuracy/runtime/stability，而非默认最复杂 solver。
- 固定并记录 `T`、tolerance、max steps、adjoint/direct gradient、dtype、failure policy；单终点默认 `T=1` 只是尺度约定，不解释成真实实验时间。
- 失败必须显式标记：NaN/Inf、max-step、gradient explosion、stiffness/underflow；禁止静默返回 clipped prediction。
- L3 测试：已知解析 ODE、forward convergence、有限差分 gradient、batch/seed/device 一致性、near-stiff 反向案例。
- 完成闸门：误差随 tolerance/step 有合理收敛；失败路径可诊断。

### S06.T03 Loss、regularization 与 count decoder

- 沿用 S05/S02 的 E1/E3 evidence、weight/bridge interaction audit；新增 nonlinear stability、sparsity/low-rank 和必要的 solver penalty，各项可单独关闭和报告。E4 预测误差不得反向改写 S02 evidence 或 S04 interventional-effect labels。
- decoder 与 S03 raw-count generator接口保持一致；若训练 learned distribution decoder，必须与固定 generator 做等容量/额外容量说明，并仅在 training fold 拟合。
- 分布损失候选（count likelihood、MMD/Sinkhorn）一次只启用一个主版本；选择需通过小型合成/真实训练 fold 比较，不能把多个 loss 网格无上限扩张。
- 完成闸门：损失尺度/梯度可审计；输出 raw-count contract 通过；未使用 hidden response。

### S06.T04 同容量 nonlinear one-step 对照

- 构造使用相同 state、target input、candidate mask、parameter count range、decoder 和 training budget 的 finite-time one-step model。
- 唯一目标是隔离 ODE 积分/共享 vector-field parameterization 的附加价值。
- 保存容量、FLOPs/时间、optimization steps 与 early stopping 对账。
- 完成闸门：对照没有被故意削弱；差异清单明确。

### S06.T05 单 fold dry run、恢复与资源批准

- 小 K/targets/fold 贯通 train→solver→predict→count generator→metrics；测试 checkpoint 恢复、seed 和数据顺序。
- 记录 solver 调用数、失败率、runtime、peak CPU/GPU memory 和预测文件规模。
- 每策略/seed 后释放模型、optimizer、loader、large tensors/AnnData/metrics并清理 CPU/CUDA。
- 长时全 fold、GPU 或高资源运行是人工批准点。

### S06.A.R 阶段审阅

- 复核模型共享边界、solver/gradient tests、one-step 公平性、dry run 与资源预算。

## Phase S06.B：OOD 评测与科学闸门

### S06.T06 预注册全 fold 训练

- 按 S01 folds 与 S05 授权配置运行；inner fold 选择 rank/regularization/solver/loss，outer response 严格不可见。
- 每 fold 多 seed；所有成功/失败 run 保留。早停、retry 与数值修复规则在运行前冻结。
- 预期产物：checkpoints、predictions、run manifests、solver diagnostics、metrics 与资源记录。
- 完成闸门：所有预注册 run accounted for；retry 不覆盖原 run。

### S06.T07 结构消融

- 必须比较：S03 fallback、S05 shared linear、nonlinear one-step、nonlinear ODE。
- 图层面先比较 data-only mask 与无图/相同容量版本；真实 prior 与 randomized prior 的增益归 S07，不在 S06 偷跑。
- 组件消融：state input、soft intervention/rho、bridge loss、sparsity/low-rank；每项只在预注册 fold 子集或预算内执行。
- 完成闸门：每个增益主张有容量匹配对照和跨 fold 结果。

### S06.T08 诊断与失败原因

- 分析 state swap、target/context slices、support/transport class、protocol/batch、solver failures、decoder/calibration、`G_predictive`/program 稳定性；把 E1/E3 证据不足与 E4 generator/solver 失败分别记入 cause ledger。
- 视觉诊断若启用，先冻结问题与样本，盲化模型标签；不能用于事后挑 target 或 seed。
- Cause ledger 使用 `supported/ruled_out/unresolved`；workflow failure 与 scientific hypothesis 分开。

### S06.T09 模型保留与科学 verdict

- 评估两个独立子主张：`nonlinearity_adds_ood_value` 与 `continuous_structure_adds_ood_value`。
- 只有 nonlinear ODE 对 one-step/linear/fallback 的增益在多数 contexts/folds 稳定、实际效应达门槛且成本可接受，才冻结为主模型。
- nonlinear 有增益但 ODE 不增益：回退 nonlinear one-step；共享 linear 不劣：回退 S05；复杂模型全面不劣优势不足：回退 S03。
- `falsified` 仍需排除 implementation/numerical/data/geometry 等替代原因；否则用 `weakened`/`unresolved`。
- 预期产物：`reports/nonlinear_ode_gate.md`、frozen model card 或明确的 fallback decision。
- 完成闸门：用户批准保留/回退以及是否允许进入 S07。

### S06.B.R 阶段真实产物复核

- 直接检查 checkpoints、predictions、run completeness、对照、metrics、诊断和 gate report。
- 根进度只在用户决策后切换到 S07 或 S08。

## 计划变更规则

进入理由、vector field、solver、loss、decoder、one-step 公平协议或 scientific gate 改变时先修订计划；实际 run/metric/资源/verdict 写进度和报告。
