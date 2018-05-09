# Precesion、Recall、F-Score、ROC、AUC ROC

## 公式及定义

​				Relevant		NonRelevant

Retrieved			TP			FP

NotRetrieved		FN			TN

- TP: true positives
- FP: false positives
- FN: false negatives
- TN: true negatives

Accuracy：$$\cfrac{TP + TN}{TP + NP + TN + FN}$$

Precision：$$\cfrac{TP}{TP + FP}$$

Recall：$$\cfrac{TP}{TP + FN}$$

## 维基百科

+ Precesion：搜索结果有多大用处
+ Recall：结果如何完整

## 知乎

实际上非常简单，**精确率**是针对我们**预测结果**而言的，它表示的是预测为正的样本中有多少是真正的正样本。那么预测为正就有两种可能了，一种就是把正类预测为正类(TP)，另一种就是把负类预测为正类(FP)

而**召回率**是针对我们原来的**样本**而言的，它表示的是样本中的正例有多少被预测正确了。那也有两种可能，一种是把原来的正类预测成正类(TP)，另一种就是把原来的正类预测为负类(FN)。


## 其他资料

+ Accuracy：算法整体的准确率
+ Precesion：阳性预测值，只计算True，而不计算False，因此选择那种指标作为True是和结果强相关的，例如预测猫狗，狗作为True和猫作为True，得到的结果是不同的。
+ Recall：阳性预测值，计算所有的True有多少被认为是True。


## 使用Precesion、Recall的好处

+ 防止数据偏斜
+ 少量判断的时候，Accuracy很低，使用Precesion不受样本数量影响。


## 举例

假如有99个False，一个True

全部预测False

Accuracy=99%

针对False样本：Precesion=99%		Recall=100%

针对True样本：Precesion=0%		Recall=0%

只预测了一次，成功预测到True样本

Accuracy=1%

针对False样本：Precesion=0% 		Recall=0%

针对True样本：Precesion=100%		Recall=100%

## F-Score

目的：兼顾 Recall 和 Precesion 的特性

$F-Score = \cfrac{(1 + \beta^n)Precesion\cdot Recall}{\beta^n Precesion + Recall}$

+ F0.5-Score：n = 0.5，Precesion 比 Recall更重要
+ F1-Score：n=1，Precesion 和 Recall重要性相同
+ F2-Score：n=2，Recall 被 Precesion更重要



##ROC、AUC ROC

TPR：在所有实际为阳性的样本中，被正确地判断为阳性之比率 

$TPR = \cfrac{TP}{TP + FN}$

FPR：在所有实际为阴性的样本中，被错误地判断为阳性之比率。  

$FPR = \cfrac{FP}{FP + TN}$

ROC：以伪阴性率（FPR）为X轴，以真阳性率（TPR）为Y轴绘制的曲线。左上角是最佳情况，右下角是最差情况，对角线是为随机预测曲线（对错各一半）。对角线以上的点代表好的分类结果，以下代表差的分类结果。

AUC：曲线下面积（Area under the Curve）

AUC ROC：ROC曲线下面积，越大表示模型越好，$AUC \in (0, 1)$