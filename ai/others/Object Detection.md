# RCNN系列算法笔记

## 准备知识

### Efficient Graph-Based Image Segmentation 算法

- 把图像中的每一个像素点看成一个顶点vi ∈ V（node或vertex），像素点之间的关系对（可以自己定义其具体关系，如相邻关系）构成图的一条边ei ∈ E，使用梯度差、RGB差等指标作为边的权重，这样就构建好了一个图 G = (V,E) 
- 基于图G = (V, E)，构造最小生成树
- 通过合并策略，合并出一颗一颗的子树，最后得到一个既不”太精细“也不”太粗糙“的分割 

### Selective Search 算法

#### 区域提取

- 使用Efficient Graph-Based Image Segmentation算法获取初始区域
- 根据相似度排序，使用贪心算法每次合并相似度最高的两个区域，直到只剩下一个区域。并记录合并过程中的所有区域（保留层级性）
- 将区域变为bbox

#### 相似度计算

- 使用纹理、颜色、大小（为了使算法优先合并小区域，以实现层级性）、吻合度四种相似度求和，作为最终的相似度

## RCNN

### 简介

使用CNNs代替传统的HOG、SIFT作为特征提取器，并使用SS生成region proposal，并训练单独的回归器和分类器。

### 背景

Object detection的目标是找出图像中的object的位置并判断object的类型。在上一个十年HOG、SIFT等特征提取算法造成了object detection的飞速进步，但是在近十年达到瓶颈。

从算法的原理来看，HOG、SIFT等特征提取手段类似于灵长类V1皮层，仅仅进行了初级的特征提取，缺乏层级性的特征提取能力。

那么，有没有层级性特征提取算法呢？当然有，那就是CNNs（卷积神经网络）。

CNNs在上世纪90年代非常火热，但由于SVM算法的出现以及深层CNNs难以训练等原因，CNNs逐渐退出了主流的舞台。但CNNs从未停止前进的脚步，诸多的训练技巧、基础设施的进步使得训练深层CNNs成为可能，在2012年，AlexNet横空出世，在ILSVRC上取得惊人的进步，让CNNs重回聚光灯下。

AlexNet的成功证明了CNNs在image classification领域的能力，而如何将CNNs应用到Object detection领域，成为了下一个研究的重点，RCNN则应运而生。

###算法

RCNN秉持着，进行最少的改动来将CNNs应用到Object detection领域这一初衷。在这种思想的引导下，最终得出了如下的结构：

+ 使用现有的selective search算法，搜索出region proposal
+ 使用ImageNet上训练的CNNs并在PASCAL VOC上精调（这种方法如今已成为了CNNs的使用范式）
+ 使用精调的CNNs对每一个region proposal进行特征提取
+ 使用SVM对生成的特征进行训练，得到分类器
+ 训练回归器，对bounding-box进行精修

从上可以看出，CNNs仅仅充当一个特征提取器，其他的均是成熟的技术。但即便如此，也取得了极大的成功（基于HOG的DPM模型mAP为33%，而RCNN为63%）



## SPP-Net

### 简介

将SPP技术与CNNs相结合

###背景

RCNN存在一个极其重大的缺陷，即需要为一张图片的所有region proposal单独计算feature map，这个过程中存在大量的冗余计算。也是RCNN性能的最大瓶颈。

究其原因，是因为CNNs之后的FC层需要固定维度的输入，而由于region proposal的大小不一，因此RCNN中将每一个region proposal都resize到相同的shape，然后再通过CNNs计算得到相同维度的feature map。

而从另外一个方面来看，能否有一种技术，将不同尺寸的feature map整形为固定的尺寸？如果这种技术存在，那么则只需将原始的完整图像输入一次CNNs得到整体的feature map，然后每一个region proposal只需使用该feature map的对应部分，然后整形为固定尺寸即可，这样就将特征计算次数减少到了一次。

CV领域有一种成熟的技术叫做SPP（spatial pyramid pooling）它主要有以下功能：

- 将任意尺寸的feature map转化为固定尺寸的feature map
- 能够提取不同空间尺度的特征（使用不同的pooling stride）

可见，将SPP与CNNs结合，即可解决RCNN存在的缺陷，而SPP-Net也因此诞生（SPP-Net并非指某一特定的网络结构，而是使用了SPP技术的CNNs）

###算法

#### SPP

+ 假设stride为 k ，那么将input feature map划分为 k x k 的固定区域，然后使用max pooling得到 $k^2$个特征点
+ 使用n种不同的stride，然后将feature vector进行拼接，feature的数量为 $\sum_{i = 1}^n  stride_i^2  $

值得注意的是，SPP本身仅仅是对CNNs的输出进行max pooling，虽然有多种stride，但并不能提取多尺度特征，它需要依赖图像金字塔（图片尺寸的缩放）才能获取不同尺寸的特征。相比之下，后文提到的FPN则可以在单一尺寸图片输入的情况下，获得多层次的特征。

#### SPP-Net（object detection）

SPP-Net应用于object detection的场景时，继承了RCNN的结构，但对其的特征提取流程进行了改进

+ 将原始图片输入CNNs得到整体的feature map
+ 利用image到feature map的映射关系，直接获取region proposal对应的sub feature map
+ 利用SPP，将sub feature map整形为固定的尺寸



SPP-NET大大加快了RCNN的训练速度，且其多尺度特征提取的特性，也一定程度上提升了算法的精度。



## Fast RCNN

### 简介

将RCNN中单独的分类、回归两个步骤融合到神经网络中。并使用SPP技术减少冗余的特征计算。

### 背景

SPP-Net虽对RCNN进行了一定程度改进，但是二者仍存在诸多问题：

+ selective search算法速度慢
+ 需要多阶段训练，使得整体流程较为复杂
+ 特征提取与分类、回归的分离导致需要使用大量的磁盘缓存中间数据（region proposal的feature）
+ SPP-Netfine tuning效率很低
  + SPP-Net 进行fine tuning的时候先求出了所有图片的roi，然后打乱顺序进行fine tuning。这是一种Roi-centric-sampling，这导致每个batch的输入可能来自于不同的image，因此需要反复计算feature map或者对feature map进行缓存，均带来了很大的开销

其中二、三、四这两个问题尤为突出，基于此，RCNN的作者提出了Fast RCNN对其进行了优化。

### 算法

#### fine tuning

Fast RCNN采用image-centric-sampling，对于每个batch，先从训练集中采样N张图片，然后每张图片采样R个Roi。相当于每一个batch训练 N x R 个Roi。这样的好处是，同一张图片的Roi可以共享计算和内存。

根据作者的实验表明，这种fine tuning算法并未带来过拟合（每个batch的Roi都有大量的相似性）。



#### object detection

+ 使用SS算法提取region proposal（此处和RCNN一致，没有进行改进）
+ 将整张图片输入CNNs，获得完整的feature map
+ 通过region proposal获得Roi（region of interest，即region proposal对应的feature map）
+ 通过Roi pooling将不同尺寸的Roi变为固定尺寸的feature map（相当于简化版的SPP，Roi pooling采用固定的stride，例如将Roi划分为7x7，然后做Max Pooling）
+ 在FC层的最后新增两个并行的layer，一个用于做分类，一个用于做回归





由上可知，Fast RCNN的网络结构对比与SPP-Net而言，主要有两点区别：

+ 使用single stride的SPP（SPP-Net中使用了multi-stride）
+ 新增两个FC层来代替分离的SVM和BBox回归



其中，最主要的是使用神经网络本身完成分类和回归，新增的两个FC层构成了multi-task学习，能在进行分类和回归的同时，能够对整体网络进行fine tuning，提升了算法的精度，且减少了中间数据的存储和加载带来的巨大开销。



作者的一些实验：

+ 使用FC层代替SVM后，效果有小幅度的提升（更重要的是让算法变得更简洁和快速）。
+ multi-task对整体的准确率有一定的提升。
+ mAP随着候选框的数量增加而略微上升，然后下降。因此过多的候选框反而有害。




## Faster RCNN

### 简介

将region proposal的生成过程融合到神经网络中（RPN，Region Proposal Network）

### 背景

Fast RCNN解决RCNN的object detection network中存在的大部分问题，至此，算法速度的最大瓶颈变为了region proposal流程。

此处有两种思路：

+ 在GPU上实现更快速的SS算法
+ 重新设计region proposal算法

鉴于Fast RCNN算法中将分类和回归融合到神经网络上取得的成功，作者选择了第二种思路，将region proposal也融合到了神经网络当中。

### 算法

#### RPN

RPN（region proposal network）网络是一种region proposal生成算法，其处于到CNNs和FC层之间，利用CNNs生成的feature map来生成region proposal。

RPN网络对CNNs的输出进行了一次3x3卷积，然后接两个FCN网络，分别计算分类、回归。其中回归的时候引入了anchor的概念，anchor是对feature的不同比例映射（以不同的比例和尺寸映射回原图）。回归的目的则是对不同的anchor进行精调，得到region proposal。分类则是确定每一个anchor是否是region proposal。

此处值得注意的是，每一个特征点都会训练K个anchor。每一个特征点的感受野是固定的，但anchor却是不固定的，anchor有可能大于了感受野，也可能小于感受野。因此anchor的作用是一种推理能力。例如感受野是一个人的下半身，可以大概率推断出一个竖立的长方形anchor，而不会推断出一个横躺的长方形。并且，根据下半身在感受野中的位置，偏左偏右，还能对该竖立的长方形anchor进行一点微调，得到最终的一个region proposal。

【我的思考】用多种不同的anchor的目的，可能是为了获得更准确地获取不同形状不同大小的region proposal。因为如果只有一个anchor，那么需要回归到不同的尺寸和比例，那么回归的难度会更大。而使用不同的anchor结合分类网络，那么每个anchor有自己对应的回归网络，因此只用进行较小的微调，然后通过分类网络筛选掉错误的region proposal。



RPN的一些巧妙设计

+ 利用FCN，并行得到了整张图片中所有的region proposal

+ 利用了multi-task学习，与object detection网络共享一个特征提取CNNs

  ​


#### 完整流程

- 将图片输入CNNs得到完整的feature map
- 将feature map输出RPN网络得到region proposal
- 结合feature map和region proposal得到roi，并输入后续网络（与Fast RCNN一致）




RPN网络后最后的回归网络的区别：

+ RPN网络的目标是Recall，偏向于寻找更多的框
+ 最后的回归网络的目标是Precision，偏向于寻找更准确的框



## FPN

### 简介

一种有效使用CNNs内在的多尺度特征的方法

### 背景

在传统的object detection领域中，特征金字塔是一种基本组件，用于实现不同尺度目标的检测。但是在最近的object detector中（Fast RCNN、Faster RCNN），均未使用特征金字塔，因为它的计算、内存密集特性（消耗大量的计算资源）。

本文提出了一种具有横向连接的自顶向下的结构，利用了CNNs内在的多尺度、金字塔分级，来实现了低成本特征金字塔，称之为FPN（feature pyramid network）

在一个基本的Faster RCNN系统中仅添加了FCN，便达到了state-of-the-art效果。

### 多种特征金字塔结构对比

![img](https://upload-images.jianshu.io/upload_images/3232548-79414c80444765b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



a） 特征化图像金字塔：通过图片的缩放来获得不同尺度的特征

- 由于成倍的计算开销，因此可行性不高，且大尺寸的图片会占用很大的内存，而如果仅仅在测试时使用又会造成训练、测试不一致，由于这些原因，在Fast、Faster RCNN中并未使用特征化图像金字塔。

b）单一feature map：只使用CNNs的最高层级的特征进行预测

- Fast、Faster RCNN使用了这种方式

c）金字塔特征层级：利用CNNs内在的层级性，分离的使用不同层级的特征

- 不同层级的feature的语义具有较大的差异，尤其是高分辨（低层）的特征普遍是低级特征，会损害物体检测的效果
- SSD虽然采用了这种方式，但是为了防止使用低层次特征，因此在CNNs后新增了几个卷积层，用于特征提取。

d）特征金字塔：不仅使用CNNs内在的层级性，并对不同层级进行自顶向下的级联



在手工特征设计阶段，特征金字塔的被大量应用，在最近的比赛中，所有排名靠前的队伍都使用特征金字塔（ResNet等）。



![Figure 2](https://upload-images.jianshu.io/upload_images/3232548-e1ee021eac1b88be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在最近的一些论文中，提到了上方的结构，采用自顶向下与横向连接，但是只预测一次。而本文提到的方法是分别进行预测。



### 算法

现有的CNNs算法通常有多个阶段，每个阶段内部的feature map尺寸是相同（same pooling），而阶段间的尺寸通常是两倍关系（pooling stride = 2）。

FPN网络选取每一个阶段的最后一层的输出，考虑到内存的问题，本文没有选取第一阶段。

特征自顶向下流动时，由于尺寸在变大，因此对上层特征进行上采样并与横向特征进行求和。

![Figure 3](https://upload-images.jianshu.io/upload_images/3232548-a51d06a2a94bfec4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在得到特征集合 $\{C_2, C_3, C_4, C_5, C_6\}$ 后，对其进行 3x3 卷积，去除上采样导致的混叠效应得到最终的特征集合

$\{P_2, P_3, P_4, P_5, P_6\}$ 



得到特征集合之后，将其分别输入后续的分类器和回归器（分类器、回归器，为所有层次特征值所共享）。



## Mask RCNN

### 简介

ResNext101 + FPN + Faster RCNN + Mask layer，使用先进的底层技术，并在Faster RCNN的基础上新增一个掩码旁支网络。

### 背景

Faster RCNN基本解决了RCNN系列算法存在的大部分问题，但是存在“不匹配”问题：

+ RPN网络计算出region proposal之后，是四个浮点数，但是将其映射到roi后，进行了取整
+ ROi pooling的过程中，将Roi几等分，这个时候可能出现无法整除的情况，例如feature map为30x30，而Roi为7x7，这个时候就有取整

“不匹配”问题会导致学习到的region proposal存在一定的偏移，对于越小的样本，误差越大。基于此作者提出了Roi align。



multi-task学习在object detection中取得了很好的效果：

+ Fast RCNN将分类和回归融合到同一个网络中。
+ Faster RCNN将region proposal与object detection融合到同一个网络中。

基于这种思路，作者将image segment作为一个旁支网络，添加到了Faster RCNN中，构成了分类、回归、分割三个并列输出。



在object detection算法发展的过程中，backbone网络也在飞速发展，涌现出了ResNet等优秀的网络结构，MaskRCNN使用了ResNeXt-101网络作为其Backbone，并使用到了FPN技术，最终达到了state-of-the-art。



### 算法

#### Roi align

+ 从region proposal映射到roi时，保留浮点数。
+ 将roi划分为kxk个区域，保留区域坐标为浮点数。
+ 每个区域中选择n个点（固定位置），使用双线性插值得到每个点的特征值（使用每个点最近的4个特征点进行双线性插值）
+ 从n个点中选择最大值作为该区域的特征值（max pooling）



值得注意的是，在roi pooling中，对每个区域中的所有点进行max pooling。而roi align中，仅仅对n个点进行max pooling。根据作者的实验，n取4时效果最佳，即便在n取1时也和roi pooling效果相差无几。

#### 分割网络

在Faster RCNN最终的分类、回归两个layer的并行位置，添加一个分割layer。假设一共有K个类别，而feature map的大小为mxm，那么mask layer的输出为mxmxK。此处的激励函数使用了sigmoid，然后通过二值化的方式确定掩码的有效性。对比与softmax而言，避免了类别竞争。在计算loss的时候，结合class layer的结果，选择对应的类别mask，使用binary cross entropy计算损失。

此处的一点理解：

+ 使用mxmxk而非mxmx1，确保了不同的类别独立训练。
+ 使用sigmoid代替softmax，去除了类别输出的彼此干扰。
+ 使用旁支网络的方式，是算法变得一体化，且用到了multi-task学习



根据作者的消融实验：

![img](https://alvinzhu.xyz/assets/2017-10-07-mask-r-cnn/table2.png)

根据上述消融实验可知一下技术均起到了提升作用

+ 使用优秀的backbone
+ sigmoid代替softmax
+ RoiAlign
+ FCN
+ FPN