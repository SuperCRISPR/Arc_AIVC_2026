# 文献索引

本目录只保留支持当前路线的论文、方法卡和官方入口。第三方代码、数据、模型权重、缓存和 `.git` 不在资料库中保存；实际复用前须在阶段 T00 再次核对版本、维护状态、task fit 与许可证。新增方法按 [`../rules.md`](../rules.md) 归档，并使用 [`../templates/方法卡模板.md`](../templates/方法卡模板.md)。

| 资料 | 项目位置 | 复用边界 |
| --- | --- | --- |
| [PerturbODE](PerturbODE/方法卡.md) | 结构化 GRN-ODE 的主要骨架参考 | 需改造为跨细胞系共享机制和 NTC 初态任务 |
| [MEGA-ODE](MEGA-ODE/方法卡.md) | 图约束 ODE 与后续复杂扩展 | 方法参考；GPL-3.0 代码不直接复制 |
| [scPerturBench](scPerturBench/方法卡.md) | OOD 拆分、评估和基线 | 评测框架，不替代项目模型 |
| [Linear-baselines](Linear-baselines/方法卡.md) | 复杂度门控 | 强基线和反过拟合约束 |
| [AROMA](AROMA/方法卡.md) | 知识证据和外部 baseline | 不是 ODE 或核心 GRN 来源 |
| [TxPert](TxPert/方法卡.md) | 知识图预测对照 | 不是主模型 |
| [CellNavi](CellNavi/方法卡.md) | 图增强状态流形的次级参考 | 不进入当前主线 |
| [因果重叠校正](因果重叠校正/方法卡.md) | E1 matched NTC、condition uncertainty、hierarchical effect synthesis、共同支持域、条件加权与跨环境候选筛选 | 训练前证据校正；不把未识别的 KD x batch 偏差伪装成已消除 |

Geneformer 仅作为可选表示参考，当前不保留本地快照；其引入必须在 LOCO 验证中证明价值。

## 索引完整性要求

- 任何进入 shortlist、影响设计或实际使用的新方法都必须先在本表登记；
- 每个条目必须链接到方法卡，方法卡再链接一手论文、官方实现和对应 stage reuse decision；
- 搜索后立即排除且未影响决策的条目只进入 stage `method_reuse_review.md`，不污染本索引；
- R00 将补全既有方法卡的稳定 ID、版本/commit、license、可复用 interface 和最后核验日期；补全前旧卡只能作为调研入口。
