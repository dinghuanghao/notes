# Decision Tree

定义：在给定特征条件下，类的条件概率分布

通俗的理解：寻找最优的特征划分顺序，以及每个特征最优的划分值。

特点：决策树的划分边界（在特征空间中），总是平行于坐标轴的（多变量决策树可以划分斜线）



## 公式

+ 熵：$ H(D) = - \sum p_i * log_2p_i $ 

+ 条件熵：$H(D|A) =\sum_{i=1}^n p_i * H(D | A = a_i), p_i = P(A = a_i) = Num(a_i) / Num(A)$

  + n表示有A有n种不同的取值，在决策树中表示使用一种特征的n种取值。例如“肤色”，那么n为3（黑、白、黄）

+ 信息增益：$g(D, A) = H(D) - H(D|A)$

  + 在决策树中，表示用特征A进行划分后，系统的熵减程度，信息增益越大说明系统熵减少的越多。

+ 信息增益比：$g_R(D, A) = \cfrac{g(D, A)}{H_A(D)}$ , $H_A(D) = - \sum_{i=1}^n \cfrac{|D_i|}{|D|} log_2{\cfrac{|D_i|}{|D|}}$  

  $H(D)$ 通常指的是数据的标签（y）的熵，而$H_A(D)$ 则是指定数据的特征A的熵。

  当一个特征存在很多value（熵较大），那么信息增益比会比信息增益小，用来抑制信息增益总是倾向于选择value较多的特征。

+ 基尼指数：$Gini(p) = \sum_{k=1}^K p_k(1-p_k) = 1 - \sum_{k=1}^K p_k^2$

  $Gini(D) = 1 - \sum_{k=1}^K  (\cfrac{|D_k|}{|D|})^2$

  $Gini(D|A) =  \sum_{i=1}^n p_iGini(D_i|A = A_i) , p_i = P(A=a_i)$



基尼指数与熵的区别：

熵：$ H(D) =  \sum p_i *  - log_2p_i $

基尼指数：$Gini(p) = \sum p_i(1-p_i) $

+ 二者都可以表示分布的无序程度，越大，越无序。
+ 其中，一个是使用log函数，一个使用线性函数，二者的单调性相同。但是线性函数计算更为迅速。
+ 二者都会偏向于选择取值较多的特征，但是由于基尼指数用于CART决策树（二分类决策树），因此不存在取值较多的问题。

![img](http://s10.sinaimg.cn/large/0062QVXZzy7dLjdDBSF49&690) 





## 经典决策树

### ID3

以**信息增益**最大作为决策树划分标准

剪枝的时候从底向上，对比取消子树前后系统的准确率变化（cv）

### C4.5

以**信息增益比**最大作为决策树划分标准

### CART

+ 回归：以最小二乘误差作为决策树划分标准

  + 遍历所有的特征A，以及特征的取值，由于回归问题特征是连续值，因此遍历所有的可能值，然后以该值为中点，将输入值划分为两个区间，并寻找最小化平方误差的可能值

  ​        $min[min\sum_{x\in R1}(yi - c1)^2 + min\sum_{x\in R2} (yi - c2)^2]$

  ​	R1、R2表示两个划分区间，c1、c2表示两个区间的平均值

  + 重复上述步骤，构建一颗二叉树，直到满足条件结束
  + 剪枝

+ 分类：以基尼指数作为决策树划分标准
  + 遍历所有的特征A，以及特征的取值a，寻找最小化基尼指数的a
  + 重复上述步骤，构造一颗二叉树，直到满足条件结束
  + 剪枝



## 连续值处理

+ 将特征的所有取值按大小排序$A = \{a_1, a_2, a_3 …… a_n\}$
+ 获得中位数集合$T = \{t_{1.5}, t_{2.5}, …… t_{n-0.5}\}$
+ 利用T，进行划分。并利用信息增益或者基尼指数选择最佳的 t



## 缺失值处理

+ 当寻找特征A的划分点时，仅在特征A未缺失的子集$D^{'}$ 上进行搜索

  

