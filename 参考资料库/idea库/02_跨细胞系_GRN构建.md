# 跨细胞系 GRN 构建

## 候选、干预证据、预测耦合与解释的职责

```text
成熟 GRN 工具、TF-target/pathway 资源 -> 候选图 M
多细胞系 perturbation 响应             -> interventional_effect_evidence 表
OOD 预测训练                            -> 共享预测耦合 G_predictive
文献与知识证据                         -> 训练后解释图
```

候选图缩小搜索空间，不能直接当作预测图。`interventional_effect_evidence` 记录 target assignment 对下游终点的总效应、状态依赖、异质性和支持域；它不假定直接调控。最终 `G_predictive` 的方向、符号和强度由扰动后分布拟合、跨细胞系稳定性、稀疏性和 OOD 预测共同定权，表示模型中的有效共享耦合，不自动等于直接因果 GRN。

## 可复用与自建

GENIE3、GRNBoost2、SCENIC/pySCENIC、Inferelator、CellOracle 等可用于生成 `M`。项目自身的核心是把不同细胞系的 perturbation 效应整合为共享机制，而不是重复发明一个无监督 GRN 推断器。

建议为每条边保留：

```text
evidence_type, total_effect_support, state_conditional_effect,
heterogeneity, prediction_interval, equivalence_status,
sign_consistency, context_interaction, protocol_stability,
temporal_support, prior_support, direct_regulatory_edge_status
```

跨细胞系一致响应优先称为共享 predictive/program coupling。`direct_regulatory_edge_status` 默认是 `unresolved`；不显著异质不能当作不变性证明，需结合预注册等效界值、预测区间和环境覆盖。多时间点只在设计、剂量和协议可比时作为辅助 temporal support，不能由跨 study 的最早响应单独判定直接关系。

## 知识图边界

知识图只做候选边、软正则和解释标签。训练必须保留图外发现通道，并比较无图、真实图与 degree-matched 随机图。若真实图不提升外部细胞系或未见扰动预测，就不保留它。

<!-- File structure: candidate space, interventional evidence, predictive coupling, interpretation and prior boundaries. -->
