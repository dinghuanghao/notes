# One-Vs-One One-Vs-All

二者都是Multiclass-Classification策略。即输出有多种类别，但一个样本只属于其中一个类别。

##One-Vs-All

为每一种输出类别，训练一个分类器。![One-Vs-All](E:\github\notes\meachine learning\others\pictures\One-Vs-All.PNG)

优点：

+ 训练快速，只需要K个分类器
+ 平均预测速度更快，预测时需要进行 [1，K-1] 次分类

缺点：在训练样本不均衡的情况下，会偏向于样本量较大的一方

![One-Vs-All样本不均匀](E:\github\notes\meachine learning\others\pictures\One-Vs-All样本不均匀.PNG)

如上图，黑色是训练样本，这个时候由于三角形较少，因此分类结果为“红色实线”。而绿色是测试集中的数据，可见，如果把测试集也考虑进来，分类结果应该是“红色虚线”。这就是对大量样本的偏向性。



## One-Vs-One

类别两两组合，然后训练C(k, 2) 个分类器。

预测的时候，需进行 K - 1 次分类， 如：

+ A > B
+ A > C
+ A < D
+ D > E
+ D < F
+ ……



优点：

+ 更为准确，因为每一个分类器只使用两个类的数据，减小了数据的偏斜程度。

缺点：

+ 预测时间更长，需要进行 k - 1 次分类