# GRN-ODE 与有限时间边界

## 主模型

在可解释 gene-program 空间中使用共享、稀疏、允许反馈的动力学：

\[
\frac{dz}{dt}=A\sigma(Bz+b+u_a)-\Lambda z,\qquad G_predictive=AB.
\]

`u_a=-rho q_a` 是 knockdown 的 soft intervention，不把 CRISPRi 视为完全敲除。每个 NTC 细胞是一个初态；ODE 终点经 decoder 回到基因表达空间。

## 单终点数据允许什么

若只有一个实验终点，模型学习的是从 NTC 到固定时间窗 `T` 的结构化生成过程。它不能据此声称中间轨迹、吸引子、长期稳定性或完整 Waddington 景观已经被识别。

one-step operator 是同容量对照或有限时间近似，不是第二条主路线。只有多时间点数据才使 time-held-out、滚动积分和更强的连续时间解释成立。

## 可选扩展的门槛

MEGA-ODE 的图向量场和 MoE、或低秩 context adapter，只能在共享 predictive GRN-ODE 的 leave-one-cell-line-out 残差稳定存在并可复现时启用。不能以自由的每细胞系网络吸收差异。
