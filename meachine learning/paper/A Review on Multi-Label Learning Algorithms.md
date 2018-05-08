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



训练的目的是使相关标签的输出大于非相关标签

$f(x, y^{'}) > f(x, y^{''})$

+ 通过一个阈值函数 $t(x)$，来输出二进制标签：$$h(x) = \{ y | f(x, y) > t(x), y \in \mathcal{Y}\}$$ ：
+ 对$f(x, y)$ 进行排序，然后选出值最大的前N个：$h(x) =\{y|rank_{f(x, y)} \leq t^{'}(x)\}$

  

阈值设置

+ 使用constant，可以通过$LCard(\mathcal{D})$ 等量，结合验证数据进行指定。
+ 自学习



## Evaluation Metrics

传统监督学习：

+ Accuracy，F-socre，ROC curve（AUC），etc

多标签学习：

+ example-based
  + 分开评估每一个对象，并返回均值
+ label-based
  + 分开评估每一个标签，并用macro average/micro average得到均值

### Example based metrics

+ Subset Accuracy

  $subsetacc(h) = \cfrac{1}{p}\sum^p_{i = 1}[[h(x_i) = Y_i]]$

  预测完全正确的对象占总对象的比例

  

+  Hamming loss

  $hloss(h) = \cfrac{1}{p}\sum^p_{i=1}|h(x_i)\Delta Y_i|$

  预测标签与实际标签的不一致程度，在维基百科和其他的一些论文中，均除以了一个 $L$ (标签种类)。



+ $Accuracy_{exam}、Precision_{exam}、Recall_{exam}、F^{\beta}_{exam}$

  $Accuracy_{exam} = \cfrac{1}{p}\sum^p_{i=1}\cfrac{|Y_i \bigcap h(x_i)|}{|Y_i \bigcup h(x_i)|}$

  这个地方有一个疑问，分母并不是 $|\mathcal{Y}|$ ，那么假设，一共有20个类别，与 x 相关的类别有5个，而预测出来了4个，有两个预测正确，两个错误。这个时候。并不是 2/20。 而是2/7？

  $Precision_{exam}(h) = \cfrac{1}{p}\sum^p_{i=1}\cfrac{|Y_i \bigcap h(x_i)|}{|h(x_i)|}$

  $Recall_{exam}(h) = \cfrac{1}{p}\sum^p_{i=1}\cfrac{|Y_i \bigcap h(x_i)|}{|Y_i|}$

  $F^{\beta}_{exam} = \cfrac{(1 + \beta^2) \cdot Precision_{exam} \cdot Recall_{exam}}{\beta^2\cdot Precision_{exam} + Recall_{exam}}$

  