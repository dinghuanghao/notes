# K-Mean



利用“距离”为基准的一种聚类算法



+ 随机初始化K个中心点
+ 遍历所有点，根据最短“距离”原则，划分给K个中心点
+ 对K个集合求平均，得到新的K个中心
+ 重复 1 ~ 3，直到算法收敛



损失函数，$c$ 代表划分集合，$\mu$ 代表中心：

$J(c^1, c2, ……, \mu^{k-1}, \mu^k) = \cfrac{1}{m} \sum^m_{i = 1} || x^i - \mu_{c^{(i)}} ||^2$



初始化：K-MEAN的初始化结果对算法有很大的影响，可能会陷入一些小概率的局部最优。可用通过随机初始化N次，然后进行一轮训练得到 $J$ ，最后选取$J$ 最小的那一种初始化方式。



聚类数量K的选择：当K越大，K-Mean的全局最优会越小（局部最优不一定），**拐点** 附近是较好选择。

![K-MEAN Elbow.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/K-MEAN%20Elbow.PNG?raw=true) 

