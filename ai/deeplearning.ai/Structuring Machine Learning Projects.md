# Structuring Machine Learning Projects

## Introduction to ML Strategy

### Why ML Strategy

some idea to improve ML

+ Collect more data
+ Collect more diverse training set
+ Training algorithm longer with gradient descent
+ Try Adam instead of gradient descent
+ Try bigger network
+ Try smaller network
+ Try dropout
+ Add L2 regularization
+ Network architecture
  + Activation function
  + hidden units
  + .......

### Orthogonalization

相互垂直，互不干扰。

如调节图形的水平位置+垂直位置就可以便捷的移动图形倒指定位置。而如果操作是水平方向30度，水平方向150度，虽然组合起来也可以调节到指定位置，但是会非常复杂。

在机器学习中则是指调节参数的时候，希望多种手段之间是正交的，否则彼此干扰很难确定最优的参数。

例如训练过程：

+ Fit training set well on cost funciton
  + bigger network
  + Adam
  + Longer time training
+ Fit dev set well on cost function
  + Regularization
  + Bigger training set
  + Dropout
+ Fit test set well on cost function
  + Bigger dev set
+ Performs well in real world 
  + Change dev set or cost function

上面是一些简单的例子，用来说明不同的阶段进行的一些调节手段。为什么不推荐使用Early stopping？因为它不正交，在第一步training set还未拟合到足够好，就提前考虑dev set、test set，同时影响了多个阶段。

early stoping并不是无用，只是让调参过程变得复杂，在适当的情况下还是可以考虑使用。

## Setting up your goal

### Single number evaluation metric

意义：评估模型、选择

例如一个classifier有Precision、Recall两种评判方式，如果想要有效的评判，可以使用F1 Score

又比如一个classifier对于不同地区的猫有不同点准确率，想要有效的评判，可以使用平均值

### Satisficing and optimizing metrics

意义：评估模型、选择

当你有N个参数可选时，如果没有一个合理的公式来统一成一个参数，那么可以从中选一个作为optimizing（优化项），其余作为satisficing（满足项）。

例如准确率、运行时间、差异性……，那么可以限定运行时间小于10S，差异性小于5%，然后在这个前提下，以准确率最高作为选择标准。

### Train/dev/test distributions

意义：maximize the learning efficiency

原则：Choose a dev set and test set to reflect data you expect to get in the future and consider important to do well on.(dev set and test set should come form same distributions)

方法：将所有数据随机打乱，然后分配给train/dev/test，最起码要取保dev、test一致。

dev+evaluation metric = 靶心，所有工作都朝着靶心进行优化

test = 真实的靶心

### Size of dev and test sets

in machine learning

+ 60/20/20

in big data deeplearning

+ 98/1/1
+ 99.9/0.05/0.05
+ ……

Guideline

+ Set your **test set** to be big enough to give high confidence in the overall performance of your system(some times, not having a test set might be ok，不推荐)

###When to  change dev/test sets and metrics 

metric和算法的优化应该是正交的。当metric不满足需求，则需要修改metric，然后根据metric重新评估、优化算法。

+ how to define a metric to evaluate algorithm
+ how to do well on the metric


## Comparing to human-level performance

### Why human level performance

+ human error
+ bayes error(best possible error, 最小误差)



Why compare to human-level performance？

+ 人类在很多时候是很优秀的，误差接近于贝叶斯误差。
+ 当性能不如人类的时候，有许多有效的措施：
  + 更多的人工标准数据
  + 人工误差分析，分析人为什么能对？
  + bias/variance 分析



当一个算法的性能已经高于人类，那么许多方法则无法使用，如人工标注。因此使用人类误差作为一个标尺，能有助于决策。

### Avoidable bias

和human error进行对比的另外一个好处是，可以用于决策当前应该减少bias还是variance。

例如：

+ human error 1%，training error 10 %，dev error 20%，这种情况下，应该优先降低bias
+ human error 9.9%，training error 10%，dev error 20%，这种情况下，可以考虑开始减低variance

所谓高bias、高variance都是需要有参考标准的，在许多任务（human error 和 bayes error接近）中，可以用human error作为参考，进行决策。

### Understanding human-level performance

Human-level error ( proxy for Bayes error)

​		        **avoidable bias**

​			Training error

​            		     **variance**

​			     dev error

Human-level error也有不同的等级，如普通医生、专家团队，需要根据具体场景进行选择。

### Surpassing human-level performance

Problems where ML significantly surpasses  human-level performance

+ Online advertising
+ Product recommendations
+ Logistics (predicting transit time)
+ Loan approvals

ML在这些方面超过了人类的可能原因：

+ 这些是非自然感知问题
+ ML算法可以比人类学习更多的数据



在计算机视觉、NLP等领域，一定情况下，计算机已经超过了一般人的水平。但是由于人类水平本身有较大差异，因此还有许多工作需要做。

### Improve your model performance

+ 减小avoidable bias
+ 减小variance

正交地做这两个步骤，并以human error作为byes error的proxy



## Error analysis

### Carrying out error analysis

误差分析，就算人工分析错误的原因。

+ 从错误的用例中找出可能的原因

+ 选一定量的错误用例，针对每种原因进行统计、分析，得出每种原因的 优化上限。

  ![Structuring Machine Learning Projects Fig 1.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/Structuring%20Machine%20Learning%20Projects%20Fig%201.PNG?raw=true) 

### Clean up Incorrectly labeled data

DL algorithms are quite robust to random errors in the training set，but they are less robust to systematic errors。（对于少量随机误差，如label标注错误，DL算法有很强的适应性，但是对于系统性的误差，如白色的狗都标记为了猫，算法就会出现明显的错误）

![Structuring Machine Learning Projects Fig 2.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/Structuring%20Machine%20Learning%20Projects%20Fig%202.PNG?raw=true) 

在error analysis的时候，新增一个Incorrectly labeled 列，用于分析，错误标注带来的影响。



correcting incorrect dev/test set examples：

+ 确保dev、test数据集具有相同的分布。
+ 检查你的算法是否正确以及错误的例子。
+ train set 可以和dev、test set 有轻微的不一致。



### Build your first system quickly， then iterate

算法常常有非常多的优化方向，但是一开始就进行优化并不高效

+ set up dev/test set and metric
+ build initial system quickly
+ use bias/variance analysis & Error analysis to prioritize next steps



## Mismatched training and dev/test set

### Training and testing on different distribution

我的思考：

+ training set数量非常庞大，足以含盖所有的特征。
+ dev/test set与train set 的分布不同，但是其特征被training set 所涵盖。
+ 根据dev/test set 来优化和评估算法，实际上是在让算法收敛到dev/test set所期望的map。因为traing set的特征足够丰富，可以让算法收敛到许许多多不同的map。

例如一个算法的目的是识别用户拍摄的猫

+ 用户拍摄：
  + 模糊、构图不稳定
  + 数量较少
+ 网上搜索：
  + 拍摄较为专业，高清
  + 种类丰富
  + 数量庞大

网上搜索到的图片，包含的特征直接或间接（高清图可以变换为模糊图）地包含了用户拍摄图片的特征。

### Bias and Variance with mismatched data distributions

在training set 和dev/test set 不同分布的时候，train set 和dev set训练结果的差异可能有两个来源：

+ 算法的过拟合导致的variance
+ data set的差异

因此，无法简单判断是否是高variance



引入一种新的data set，Training-dev set：Same distribution as training set， but not used for training

Human-level error ( proxy for Bayes error)

​		        **avoidable bias**

​			Training error

​            		     **variance**

​		      Training-dev error

​	                **data mismatch**

​			     dev error

​	      **digree of overfit to dev set** 

​			     test error



### Addressing data mismatch

通过error analysis 来发现training set 和dev/test set的区别，并减小其区别。

+ 寻找分布存在的差异
+ 收集、生成更多的数据来填补差异

例如语音识别，真实的场景是公路上的语音识别。而training数据则是标准语音库中的数据，人工分析得出，公路上的数据具有车辆行驶噪声，因此可通过合成的方式为标准语音库添加车辆噪声。

生成数据的时候需要注意的就是，用一个很小的集合生成一个很大的集合后，可能人眼觉得数据量已经足够，但是从算法的角度，特征并未增加多少。例如对车子改变颜色之后，可能人第一眼看过去会以为是另外一辆车，但是对于计算机而言仅仅变化来很小的特征。因此相当于出现了大量重复的特征，会导致算法overfit到这个subset。因此生成的时候，尽量采取更丰富的基础集，以及更多样化的生成方式。

## Learning form multiple tasks

###Transfer learning

将网路的最后一层随机初始化，并使用新的数据重新训练。

+ 如果新的数据量少，那么只训练最后一两层
+ 如果新的数据量打，可以在原有基础上整网继续训练

![Structuring Machine Learning Projects Fig 3.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/Structuring%20Machine%20Learning%20Projects%20Fig%203.PNG?raw=true) 

对于在原有基础上继续训练：

+ 原有的网络：pre-training
+ 继续训练：fine tuning

神经网络的不同层有不同的分工，例如一些低层，可能用于进行边缘提取，颜色分辨等，这些是可以复用的因此可以迁移。

![Structuring Machine Learning Projects Fig 5.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/Structuring%20Machine%20Learning%20Projects%20 Fig 5.PNG?raw=true) 

### Multi-task learning

迁移学习是串行的，而多任务学习是并行的。

例如检查一个图片中是否有人、猫、狗

输出层同时输出三个值，并分别计算loss，然后相加。

这有一个好处，如果某些值缺失，例如标注人员也不确定是否有一只猫，这个时候算法仍然可以计算（计算loss的时候，只计算有标签的项目）

![Structuring Machine Learning Projects Fig 4.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/Structuring%20Machine%20Learning%20Projects%20Fig%204.PNG?raw=true) 

在小型的NN中，分离的task更好，在大型的NN中，multi-task更好。这个结论并非一定。可能是因为在multi-task中，有boost效果，数据量城倍增加，低层的共用网络得到充分的学习。

在计算机视觉中，物体识别等方向，大量用到multi-task learning，但通常transfer learning使用的更多。可能是因为多个task共存在一个NN中的场景相对较少，但是从大量数据中学习并迁移到少量数据的场景更为场景。

## End-to-end learning

### What is end-to-end deeplearning？

特征：

+ 输入与输出之间只有一个NN，而传统的pipeline有许多步骤


+ 需要大量的数据，远大于传统的pipline

### Where to use end-to-end deeplearning

Pros and cons end-to-end deep learning 

+ Pros

  + Let the data speak：去掉传统pipine中人工指定规则的影响，直接通过数据驱动（也许这样才能更好的超过human error，接近bayes error）
  + Less hand-designing of components needed：简化工作流程，可以把所有的精力都投入到特征工程，网络优化等工作上，不用再考虑纷繁复杂的pipeline设计、验证……

+ Cons

  + May need large amount of data：一方面是所有的知识都由数据中获取需要大量的数据，另外一方面是需要的数据的获取难度可能比pipeline方式更高，导致更难获取。

    例如人脸识别，如果用pipeline，可以分为多步。

    + 找出人脸的位置，并输出只有人脸的图形
    + 两张人脸的相似性检测

    这两步可以使用不同的数据集，因此可以找到大量的数据分别训练。而如果用end-to-end的方式，需要建立图片-标准头像的关联标注数据集，这样的数据集数量就会较少。

    我个人还有一个观点就是pipeline方式，可以更好的做迁移学习。例如上述的步骤1，可以找到很多类似的模型进行迁移。也就是因为pipeline的每一步都可以独立且相对简单，因此可以更好的做迁移学习，而end-to-end方式，一个模型完成复杂的任务，不容易找到相似的模型，来进行迁移。

  + Excludes potentially useful hand-designed components：人类的许多知识在很多情况下是很有帮助的，尤其是在数据量还不足以自表达的时候。



因此在决定是否要使用end-to-end deeplearning的时候，可以先看拥有的数据，是否足够，是否满足end-to-end的特征。在大多数情况下数据是不足以使用pure end-to-end deeplearning的，因此可以使用混合的方式。例如自动驾驶：

+ 判断图片中是否行人、停车标志等，拥有足够多的图片进行end-to-end学习
+ 由行人规划路线则交给其他的算法去完成

这是因为，道路图片-正确行驶路线，这种苛刻的数据集是很少的，但分开成两个系统后，数据的复杂程度则会下降。