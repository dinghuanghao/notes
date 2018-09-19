# Convolutional Neural Networks

卷积分为卷和积

- 积：使用卷积核与相同大小的图像区域进行乘积，然后求和。
- 卷：将上述操作在全图范围内执行

## Padding

直接卷积存在的问题：

+ 对图像直接进行卷积，会导致图像缩小。$n*n$ 的图像 $f*f$ 的卷积核，最终图大小为 $(n-f+1)*(n-f+1)$
+ 对于边缘的像素，被卷积到的次数会小于中心像素


两种不同的卷积方式：

+ valid卷积：不填充
+ same卷积：填充 $p$ 个像素。$(n+2p)*(n+2p)$ ，对于$f*f$ 卷积核，当 $p = \cfrac{f - 1}{2}$ 时，图像大小不变。

## Strided convolution

卷积的时候通常采用保持一定的步长，这是因为过于靠近的像素点特征非常相似，存在冗余信息，引入步长后，去除了冗余且提升了性能。

+ 对于图像 $n*n$ 
+ 卷积核 $f*f$ 
+ padding为 $p$ ,
+ 卷积步长为 $s$ 

卷积后图像大小为 $(\lfloor\cfrac{n + 2p - f}{s} + 1\rfloor)*(\lfloor\cfrac{n + 2p - f}{s} + 1\rfloor)$

## Convolution over volumes

输入是3D时，卷积器也是3D。如图像的RGB三通道，分别对应卷积器的三个维度，卷积得到三个2D图像后，求和得到单个输出。

![img](http://www.ai-start.com/dl2017/images/d148c3dd7ce9e6d7e29c02c483298842.png) 

卷积后的通道数量仅与卷积器的数量相关。

## One layer of a convolution network

+ 卷积，等效于 $Wx$
+ 加上偏差值 $b$ , 等效于 $z = Wx + b$
+ 执行ReLU函数，等效于 $a = f(z)$

第二、第三步操作都是以广播的形式执行。之所以要执行这两步是因为卷积操作本身是线性的，表达能力有限，因此需要引入非线性元素。

## Pooling layers

将一定的单元进行合并，不改变通道数量，有以下种类：

+ max pooling：将最大值保留
+ average pooling：取平均
+ global max/average pooling：在全局范围内进行一个池化，通常用在FC层之前，保持维度的固定。
+ spatial pyramid pooling：输入任意尺寸的feature map，输出固定大小的特征（6x6，4x6，3x3），然后进行拼接，确保输出尺寸不变。global pooling神spatial pyramid pooling的一种特例。
+ roi pooling：fast rcnn中提出的池化，相当于spatial pyramid pooling中的一层，如固定7x7。
+ roi align：是roi pooling的改进，将池化过程中的坐标保持为浮点数，然后采用双线性插值的方式来获得池化之后的输出。

池化之后图片的大小，与卷积的计算公式相同。

## Why convolutions?

+ 参数少：无论图片多大，参数仅仅与卷积核的大小核数量相关。
+ 稀疏连接：例如卷积核为 3*3，那么一个输出单元只与9个输入单元连接。
+ ​一定的平移不变性



## Some CNNs

![LeNet5.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/LeNet5.PNG?raw=true) 

![AlexNet.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/AlexNet.PNG?raw=true) 

![VGG16.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/VGG16.PNG?raw=true) 

## Residual Networks

采用跳跃连接构建跨卷积层的连接。

+ 普通的NN： $a^{l + 2} = g(z^{l+2})$
+ ResNet：$a^{l + 2} = g(z^{l+2} + a^l)$ ，当二者的维度不一致时，需要将 $a^l$ 进行变换，可以用一个自学习的矩阵，也可简单的用0进行padding。

传统网络：训练误差在网络到达一定深度后，反而出现上升

残差网络：训练误差总是下降

注意，此处是训练误差，最终还要考虑Variance，因此并不一定越深越好。



## Why ResNets work?

$a^{l + 2} = g(z^{l+2} + a^l)$

学习恒等式：当 $W^{l+2}$ 近似为0时，$a^{l+2} = a^l$ 

+ 由于正则或者训练等原因，W接近0是很有可能的
+ 因为 $a^l$ 是ReLU函数的输出，因此一定大于等于0

因此：

+ 容易构成学习恒等式，使性能不下降
+ 可能会学到一些额外的特征，使性能上升



##1 x 1 convolutions

![1x1convolutions.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/1x1convolutions.PNG?raw=true) 

单个1x1 convolution将输入 axbxc的输入变为，axbx1。

+ 网络的压缩。使用pooling可以对长宽进行压缩，而使用1x1 convolution可以对通道数量进行压缩。
+ 提升网络性能

![1x1convolution_2.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/1x1convolution_2.PNG?raw=true) 

![1x1convolution_3.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/1x1convolution_3.PNG?raw=true) 

直接卷积的乘法运算为120M次，而使用1x1 convolution后，乘法次数变为12.4M，变为了十分之一。



## Inception network

![Inception.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/Inception.PNG?raw=true) 



+ 不用人为选择卷积器的参数，使用机器自动选择多个卷积器，然后堆叠起来


这种堆叠会导致网络急剧加大，导致性能问题，因此引入了1x1convolution来进行性能的提升。

![inception_2.PNG](https://github.com/dinghuanghao/notes/blob/master/pictures/inception_2.PNG?raw=true) 



+ 每个单元都由一系列的堆叠单元组成
+ 隐藏层也会引出分支，连接到FC层，并输出结果



## Transfer learning

卷积神经网络得到大规模应用的一个很重要的原因就是它非常容易做迁移学习。

这是因为其层级性特征导致的。例如ImageNet中包含了各种各样的图片，使用它训练出来的CNNs，的底层是一些如线条、颜色、边缘相关的视觉基础特征。这些特征往往可以被各种计算机视觉任务所通用，因此对于特定的任务，只需要对靠后的层级进行fine tuning即可。