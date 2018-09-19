# iMaterialist Challenge (Fashion) at FGVC5

## 1st place solution

https://www.kaggle.com/c/imaterialist-challenge-fashion-2018/discussion/57944



###basic

pretrained CNNs -> xgboost -> F1 optimization



framework：Pytorch + fastai library

optimizer：1cycle policy with SGD (https://arxiv.org/abs/1803.09820)

threshold：动态搜索阈值(A Study on Threshold Selection for Multi-label Classification by Rong-En Fan and Chih-Jen Lin)

xgboost：将预测结果通过xgboost进一步训练（I take raw model predictions, xg predictions based on model outputs）

### 

![1](.\pictures\1.PNG)

![2](.\pictures\2.PNG)

### didn't work

+ darknet with attention
+ per label training
  + 浪费时间，且收效甚微
  + 模型具有足够的容量，利用了多任务学习的优点




## 2st place solution

https://www.kaggle.com/c/imaterialist-challenge-fashion-2018/discussion/57934



###basic

+ 模型的统一架构

  base model -> GlobalMaxPooling2D -> Dense(2048, activation='relu') -> Dropout(0.5) -> Dense(2048, activation='relu') -> Dropout(0.5) -> Dense(NUMBER_OF_CLASSES, activation='sigmoid').

+ train time augmentation

  Flip, rotate, AddToHueAndSaturation, GaussianBlur, ContrastNormalization, Sharpen, Emboss, Crop 

+ test time augmentation

  only flip

+ threshold optimization

  just using a simple threshold 0.3 for all classes.

### stacking

借鉴了 [Planet: Understanding the Amazon from Space competition](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space) and the winner [bestfitting](https://www.kaggle.com/bestfitting) 的思路

http://blog.kaggle.com/2017/10/17/planet-understanding-the-amazon-from-space-1st-place-winners-interview/

最好的单模型f1-score：0.64

#### CNN stacking

We had 9 single models, each model predicted on 9k samples in the validation set. Then we concatenate and reshape each output sample to [number of models, number of classes]. We built a stacking CNN with the structure: Input(shape=(number of models,number of classes,1)) -> Conv2D(filters=8, kernel_size=(3, 1)) -> Conv2D(16, (3, 1)) -> Conv2D(32, (3, 1)) -> Conv2D(64, (3, 1)) -> Flatten() -> Dense(1024) -> Dense(NUMBER_OF_CLASSES, activation='sigmoid').

With a window size (3,1) we hope the CNN can learn the correlation between the prediction of single models. And the last Dense layer can learn the correlation between 228 labels. Training this model with Kfold (k=5) on the validation set, we can get *0.714x* on public LB.

集成后f1-score：0.714

#### xg-boost stacking

集成后f1-score：0.703

### didn’t work

Too much augmentation on testing time reduces our score.

[Problem Transformation, Adapted Algorithm](https://www.analyticsvidhya.com/blog/2017/08/introduction-to-multi-label-classification/) supported by skmultilearn give poor result in this competition.



## 3st place solution

https://www.kaggle.com/c/imaterialist-challenge-fashion-2018/discussion/57984

### basic

+ 模型

   I have used the following models: 2 inception_resnet_v2, 3 Xception, 2 DenseNet201, NASNetMobile, restNet50, DenseNet121, DenseNet169, VGG16, restNet50, inception 

+ data Augmentation：仅仅做了训练时增强，没做测试时增强。增强方式可看下方源码

+ 基于keras编写的lr scheduler + Xception源码

  https://www.kaggle.com/dingkun/xception-model-training-pipeline-lb-0-9798

+ stacking：LightGBM 