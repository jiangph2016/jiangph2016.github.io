---
layout: post
title: 【机器学习】使用sklearn库进行机器学习二分类任务
category: 学习
tags: 机器学习
keywords: GBDT
---
* content
{:toc}

详细可分别见：
- [【Python】sklearn的GBDT库](https://jiangph2016.github.io/2020/04/02/GBDT/)
- [【Python】sklearn笔记](https://jiangph2016.github.io/2020/04/04/sklearn/)

----

- [sklearn各分类模型的比较](https://blog.csdn.net/Yellow_python/article/details/84885979)



## 引用
```
import numpy as np
from sklearn.model_selection import train_test_split
from imblearn.over_sampling import RandomOverSampler 
from collections import Counter
from sklearn.metrics import recall_score,f1_score,precision_score
import matplotlib.pyplot as plt
from sklearn.metrics import confusion_matrix
```


### 数据集处理

```
# 把数据集划分为测试集和训练集
def split_dataset(X,y):
    x_train, x_test, y_train, y_test = train_test_split(X, y,test_size=0.33,stratify = y)
    return x_train, x_test, y_train, y_test

# 过采样
def over_sample(X,y):
    #X,y = split_dataset()
    ros = RandomOverSampler(random_state=0)
    X_resampled, y_resampled = ros.fit_sample(X, y)
    print(Counter(y_resampled))
    return  X_resampled, y_resampled
```


### 训练模型

#### GBDT
```

def GBDT():
    from sklearn.ensemble import GradientBoostingClassifier
    gbr = GradientBoostingClassifier(n_estimators=200, max_depth=7,
                                     min_samples_split=2, learning_rate=0.1)
    gbr.fit(x_train, y_train)
    evaluate(gbr,x_train, x_test, y_train, y_test)
    return gbr
```
#### LightGBM
官方API：
- <https://lightgbm.readthedocs.io/en/latest/Python-API.html#training-api>
- <https://lightgbm.readthedocs.io/en/latest/pythonapi/lightgbm.LGBMClassifier.html>

参考:
- <https://www.cnblogs.com/chenxiangzhen/p/10894306.html>

```
def LightGBM(x_train,y_train,x_test,y_test):
    from lightgbm import LGBMClassifier
    gbm = LGBMClassifier(boosting_type='gbdt',max_depth=4,n_estimators=100)
    gbm.fit(x_train, y_train)
    evaluate(gbm,x_train, x_test, y_train, y_test,"LightGBM")
    
    return gbm
```

#### SVM
```
def LinearSVM(x_train,y_train,x_test,y_test):
    from sklearn.svm import LinearSVC
    from sklearn.preprocessing import StandardScaler
    from sklearn.pipeline import make_pipeline
    svm_clf = make_pipeline(StandardScaler(), LinearSVC())
    svm_clf.fit(x_train,y_train)
    evaluate(svm_clf,x_train, x_test, y_train, y_test,"Linear SVM")
    return svm_clf
```


```
def RBFSVM(x_train,y_train,x_test,y_test):
    from sklearn.svm import SVC
    from sklearn.preprocessing import StandardScaler
    from sklearn.pipeline import make_pipeline
    svm_clf = make_pipeline(StandardScaler(), SVC(gamma=2, C=1))
    svm_clf.fit(x_train,y_train)
    evaluate(svm_clf,x_train, x_test, y_train, y_test,"RBFSVM")
    return svm_clf
```



#### LogisticRegression

```
def LR(x_train,y_train,x_test,y_test):
    from sklearn.linear_model import LogisticRegression
    lr_model = LogisticRegression()
    lr_model.fit(x_train,y_train)
    evaluate(lr_model,x_train, x_test, y_train, y_test,"LogisticRegression")
    return lr_model
```


#### 决策树和随机森林

```
def DecisionTree(x_train,y_train,x_test,y_test):
    from sklearn.tree import DecisionTreeClassifier
    DT = DecisionTreeClassifier(max_depth=5)
    DT.fit(x_train,y_train)
    evaluate(DT,x_train, x_test, y_train, y_test,"DecisionTree")
    return DT


def RandomForest(x_train,y_train,x_test,y_test):
    from sklearn.ensemble import RandomForestClassifier
    RF = RandomForestClassifier(max_depth=5, n_estimators=10, max_features=2)
    RF.fit(x_train,y_train)
    evaluate(RF,x_train, x_test, y_train, y_test,"RandomForest")
    return RF
```


## 评价模型
```
def plot_confusion_matrix(cm, labels, title):
    cm = cm.astype('float') / cm.sum(axis=1)[:, np.newaxis]    # 归一化
    plt.imshow(cm, interpolation='nearest')    # 在特定的窗口上显示图像
    plt.title(title)    # 图像标题
    plt.colorbar()
    num_local = np.array(range(len(labels)))    
    plt.xticks(num_local, labels, rotation=90)    # 将标签印在x轴坐标上
    plt.yticks(num_local, labels)    # 将标签印在y轴坐标上
    plt.ylabel('True label')    
    plt.xlabel('Predicted label')



def evaluate(model,x_train, x_test, y_train, y_test,model_name = None):
    acc_train = model.score(x_train, y_train)
    acc_test = model.score(x_test, y_test) 
    print("acc_train:",acc_train)
    print("acc_test :",acc_test)
    y_pre = model.predict(x_test)
    TP = np.sum(np.logical_and(np.equal(y_test,1),np.equal(y_pre,1)))
    FP = np.sum(np.logical_and(np.equal(y_test,0),np.equal(y_pre,1)))
    FN = np.sum(np.logical_and(np.equal(y_test,1),np.equal(y_pre,0)))
    TN = np.sum(np.logical_and(np.equal(y_test,0),np.equal(y_pre,0)))
    print(TP,FP,FN,TN)
    P = TP/(TP+FP)
    R = TP/(TP+FN)
    #print("accuracy:",(TP+TN)/(TP+FP+TN+FN))
    print("recall  :",R)
    print("f1-score:",2*P*R/(P+R))
    cm = confusion_matrix(y_test, y_pre,)
    print(cm)
    plot_confusion_matrix(cm, [1,0], "Confusion Matrix")
    plt.show()
    
    
    if model_name is None:
        print("--------------------")
    else:
        print("----------{}----------".format(model_name))
    print("pre:",precision_score(y_test, y_pre,average=None),precision_score(y_test, y_pre,average='macro'))
    print("recall: ",recall_score(y_test, y_pre, average=None),recall_score(y_test, y_pre, average='macro'))
    print("f1-score: ",f1_score(y_test, y_pre,average=None),f1_score(y_test, y_pre,average='macro'))

```