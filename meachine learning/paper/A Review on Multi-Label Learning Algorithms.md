# A Review on Multi-Label Learning Algorithms

## 数学术语

**cardinality**：the cardinality of a set is a measure of the "number of elements of the set".For example, the set A = {2, 4, 6} contains 3 elements, and therefore A has a cardinality of 3.

**normalization**：In the simplest cases, **normalization of ratings** means adjusting values measured on different scales to a notionally common scale, often prior to averaging 

**∃** ：表示存在，∃ *x*: *P*(*x*) 意味着有至少一个 *x* 使 *P*(*x*) 为真 

**{ : } 、{ | } **：满足…的集合 

**$|\cdot|$** : 求集合的cardinality





## 多标签分类度量

label cardinality ：数据集D上，平均每个对象的标签数量

$$LCard(\mathcal{D}) = \cfrac{1}{m}\sum^m_{i = 1}(|Y_i|)$$



label density ：数据集D上，平均每个对象的标签密度（label cardinality的标准化）, $\mathcal{Y}$ 表示标签的维度

$$LDen(\mathcal{D}) = \cfrac{1}{|\mathcal{Y}|}LCard(\mathcal{D})$$



label diversity ：数据集D中，不同的 Y 的数量（最大为$2^{\mathcal{Y}}$）

$$LDiv(\mathcal{D}) = |\{Y | \exists x: (x, Y)\in\mathcal{D}\}|$$



label diversity normalization：对标签多样化进行标准化

$$PLDiv(\mathcal{D} = \cfrac{1}{|\mathcal{D}|}\cdot LDiv(\mathcal{D}))$$



## 框架



训练的时候使得，相关标签的输出大于非相关标签

$f(x, y^{'}) > f(x, y^{''})$

+ 通过一个阈值函数 $t(x)$，来输出二进制标签：$$h(x) = \{ y | f(x, y) > t(x), y \in \mathcal{Y}\}$$ ：
+ 对$f(x, y)$ 进行排序，然后选出值最大的前N个：$h(x) =\{y|rank_{f(x, y)} \leq t^{'}(x)\}$
+ 

阈值设置

+ 使用constant，可以通过$LCard(\mathcal{D})$ 等量，结合验证数据进行指定。
+ 自学习

