# A Review on Multi-Label Learning Algorithms

## 数学术语

**cardinality**：the cardinality of a set is a measure of the "number of elements of the set".For example, the set A = {2, 4, 6} contains 3 elements, and therefore A has a cardinality of 3.

**normalization**：In the simplest cases, **normalization of ratings** means adjusting values measured on different scales to a notionally common scale, often prior to averaging 

**∃** ：表示存在，∃ *x*: *P*(*x*) 意味着有至少一个 *x* 使 *P*(*x*) 为真 

**{ : } 、{ | } **：满足…的集合 

**$|\cdot|$** : 求集合的cardinality

$[[ \cdot ]]$ ：成立返回1， 不成立返回0





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

#### Classification metircs

+ $Subset Accuracy$

  $subsetacc(h) = \cfrac{1}{p}\sum^p_{i = 1}[[h(x_i) = Y_i]]$

  预测完全正确的对象占总对象的比例

  

+ $Hamming loss$

  $hloss(h) = \cfrac{1}{p}\sum^p_{i=1}|h(x_i)\Delta Y_i|$

  预测标签与实际标签的不一致程度，在维基百科和其他的一些论文中，均除以了一个 $L$ (标签种类)。



+ $Accuracy_{exam}、Precision_{exam}、Recall_{exam}、F^{\beta}_{exam}$

  $Accuracy_{exam} = \cfrac{1}{p}\sum^p_{i=1}\cfrac{|Y_i \bigcap h(x_i)|}{|Y_i \bigcup h(x_i)|}$

  这个地方有一个疑问，分母并不是 $|\mathcal{Y}|$ ，那么假设，一共有20个类别，与 x 相关的类别有5个，而预测出来了4个，有两个预测正确，两个错误。这个时候。并不是 2/20。 而是2/7？

  $Precision_{exam}(h) = \cfrac{1}{p}\sum^p_{i=1}\cfrac{|Y_i \bigcap h(x_i)|}{|h(x_i)|}$

  $Recall_{exam}(h) = \cfrac{1}{p}\sum^p_{i=1}\cfrac{|Y_i \bigcap h(x_i)|}{|Y_i|}$

  $F^{\beta}_{exam} = \cfrac{(1 + \beta^2) \cdot Precision_{exam} \cdot Recall_{exam}}{\beta^2\cdot Precision_{exam} + Recall_{exam}}$

  

#### Ranking metrics

需要注意的是，Classification metrics是针对$h(x)$ ， 而Ranking metric是针对 $f(x, y)$ 

+ $One\ error$

  $one\ error(f) = \cfrac{1}{p}\sum^p_{i=1} [[ [arg max_{\mathcal{y} \in \mathcal{Y}}f(x_i, y)] \notin Y_i ]]$

  隶属度最高的一个标签，分类错误的概率。在单标签分类中，就等于传统的分类错误率。

+ $Coverage$

  $coverage(f) = \cfrac{1}{p}\sum^p_{i=1}max_{y \in Y_i} rank_f(x_i, y) - 1$

  将结果排序，从隶属度最高的开始，要跨过多长的区间，才能覆盖所有的相关样本。

  输出[0.8  0.7  0.6  0.5  0.4]，真实[1 0 1 1 0]，则coverage为 ((0.8 - 0.5) - 1)

+ $Ranking\ Loss$

  $rloss(f) =  \cfrac{1}{p}\sum^p_{i=1}\cfrac{1}{|Y_i||Y_i^{\prime\prime}|}|{\left(y^\prime, y^{\prime\prime}|f\left(x_i, y^\prime\right)<f(x_i, y^{\prime\prime}), (y^\prime, y^{\prime\prime})\in Y_i \times Y_i^{\prime\prime}\right)}|$

  样本所属标签的隶属度低于非样本所书标签隶属度的平均比例。

  如[0.8  0.7  0.6  0.5  0.4]，真实标签[ 1 1 0 1 1]，那么就是 2/5（0.5 0.4小于了0.6）

+ $Average \ Precision$ 

  rank之后，以个数为阈值，从1取到K(K为最大标签数量)。并分别计算每一个阈值对应的Precision，Recall。绘制出PR曲线（Recall为横坐标，Precision为纵坐标），这条曲线总体是下降的，因为阈值为K时，所有标签都预测为相关标签，Recall为1，Precision较低。而Average Precision则是，PR曲线的线下面积。

  在PASCAL VOC比赛2010年以后，会对PR曲线进行调整，将Precision处理为单调曲线

  + $Precision[i] = max(Precision[Recall \ge i])$

  不过此处的AP和VOC的AP不同，VOC是同时输出多个类，每个类有多个标签。VOC的AP是只单个类别，MAP神指所有类别的均值。而此处仅仅是输出多个标签（相当于一个类，多标签）。

### Label based metrics

#### Classification metrics

+ $B_{macro}\ B_{micro} B\in \{Accuracy, Precision, Recall, F^\beta \}$

  $B_{macro}(h) = \cfrac{1}{q}\sum^q_{j = 1} B(TP_j, FP_j, TN_j, FN_j)$ 

  $B_{micro}(h) = \cfrac{1}{q}B(\sum^q_{j=1}TP_j, \sum^q_{j=1}FP_j, \sum^q_{j=1}TN_j, \sum^q_{j=1}FN_j)$ 

  macro averaging：对每个类别求对应的指标，然后平均。

  micro averaging：将所有的样本加起来，再求指标。 

   $Accuracy_{micro}(h) + hloss(h) = 1$ 

#### Ranking metrics



## Learning Algorithms

### Problem Transformation Methods

#### Binary Relevance

将K标签分类转换为K个二元分类，针对每一个类别训练一个二元分类器，在预测的时候预测K次。

如果输出均小于阈值，那么会没有任何相关标签，这时候可使用T-Criterion 

$T-Criterion $ ：将输出最大的一个标签作为相关标签。

Remarks：

+ Binary Relevance是一种一阶的算法，分离处理每一类标签

+ 优点：

  + 算法直接，简单
  + 可用进行并行化计算（标签独立）

+ 缺点：

  + 忽略了标签潜在的关联性

  + 本质上是一种One-Vs-All学习，容易收到 Class-imbalance影响（标签类别多，但是标签密度小）

    

#### Classifier Chain

可用认为是Binary Relevance算法的一个变种，将K个二元分类器串联起来，将输入与本分类器的输出拼接起来，作为下一个分类器的输出。![Classifier Chain](.\pictures\Classifier Chain.PNG)

由于分类器的顺序会对算法效果有很大影响，一种改进方法是Ensemble of Classifier Chain，通过随机产生多个不同顺序的Chain，并且每次的输出都是多个Chain的综合（还有一些方法是，不改变特征空间，而是在最后的输出进行聚合），一定程度上缓解了Classifier Chain对分类器顺序的敏感性，但是没有从根本解决问题，且随着标签类别变多，计算量会大大增加。



Remarks：

+ Classifier Chain是一种假定标签之间是随机相关性的高阶算法
+ 优点：
  + 利用标签的潜在联系
+ 缺点：
  + 无法并行化计算（标签预测串行化）



#### Calibrated Lable Ranking

