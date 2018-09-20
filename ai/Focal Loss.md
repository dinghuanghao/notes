# Focal Loss

https://arxiv.org/pdf/1708.02002.pdf



## Cross Entropy

$CE(p, y) = - (ylog^p + (1 - y)log^{1 - p})$

$CE(p, y) = \begin{cases}  -log^{p}，& if \ y \ = \  1 \\ -log^{1 -p}, & otherwise \end{cases} $

$p_t = \begin{cases} p, &if\ y\ is\ 1\\1 - p, & otherwise \end{cases}$

$CE(p_t, y) = -log^{p_t}$



## Balanced Cross Entropy

$\alpha_t = \begin{cases} \alpha, &if\ y\ is\ 1\\1 -  \alpha, & otherwise \end{cases}$

$CE(p_t) = -\alpha_tlog^{p_t}$

此处是二分类任务，因此只有一个 $\alpha$ 用于表示类别的关系。



## Focal Loss

$FL(p_t) = -(1 - p_t)^{\gamma} log(p_t)$

$FL(p_t) = -\alpha_t(1-p_t)^{\gamma}log(p_t)$

第二个公式引入了权重项，轻微提升了性能。

$1 - p_t$ 表示输出值与实际值的误差，误差约小，那么$(1 - p_t)^{\gamma}$ 的值约小，即当一个样本学习误差越小，它的权重会越低（简单样本）。而误差越大，权重越高（困难样本）。

第二个公式中的 $\alpha$ 在RetinaNet的实现中是一个固定的值，为0.25。

$\alpha_t = \begin{cases} 0.25, &if\ y\ is\ 1\\ 0.75, & otherwise \end{cases}$

由此，可知。对于正样本而言，loss会衰减更多。搜索资料来看，应该是由于在作者的任务中，虽然正样本更少，但是经过Focal Loss修正后，正样本的作用反而有所逆转，因此使用权重项对其降低。该数值纯粹是工程数值，在不同的项目中可能不同，个人感觉没有足够的说服力。

