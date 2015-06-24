title: Logistic回归
date: 2013-08-27
categories: 机器学习
tags: ['Machine Learning',分类器,概率论]

---

## 基于Logistic回归和Sigmoid的分类

Logistic回归分类器：

$$z=w^Tx$$
$$x=(x_0, x_1, ..., x_n) \qquad （待分类数据）$$
$$w=(w_0, w_1, ..., w_n) \qquad （最佳回归系数）$$
$$\sigma(z)=\frac{1}{1+e^{-z}} \qquad（Sigmoid 函数）$$

*   当$\sigma(z)>0.5$时，数据被归为1类
*   当$\sigma(z)<0.5$时，数据被归为0类

## 基于最优化方法的最佳回归系数的确定

函数$f(x,y)$的梯度表示为：
$$\nabla{f(x,y)}=\binom{\frac{\partial{f(x,y)}}{\partial{x}}}{\frac{\partial{f(x,y)}}{\partial{y}}}$$

梯度算法的迭代公式：
$$w:=w+\alpha\nabla_wf(w)$$

*   $\alpha$ 移动步长
*   $\nabla_wf(w)$ 移动方向

<!-- more -->

### 梯度上升算法

```Python
def gradAscent(dataMatIn, classLabels):
    dataMatrix = mat(dataMatIn)             #convert to NumPy matrix
    labelMat = mat(classLabels).transpose() #convert to NumPy matrix
    m,n = shape(dataMatrix)
    alpha = 0.001
    maxCycles = 500
    weights = ones((n,1))
    for k in range(maxCycles):              #heavy on matrix operations
        h = sigmoid(dataMatrix*weights)     #matrix mult
        error = (labelMat - h)              #vector subtraction
        weights = weights + alpha * dataMatrix.transpose()* error #matrix mult
    return weights
```
*  特点：每次更新回归系数时都要遍历整个数据集，计算复杂度太高。

### 随机梯度上升算法

```Python
def stocGradAscent0(dataMatrix, classLabels):
    m,n = shape(dataMatrix)
    alpha = 0.01
    weights = ones(n)   #initialize to all ones
    for i in range(m):
        h = sigmoid(sum(dataMatrix[i]*weights))
        error = classLabels[i] - h
        weights = weights + alpha * error * dataMatrix[i]
    return weights
```
* 特点：每次仅用一个样本点更新回归系数，是一个在线学习算法。

### 改进的随机梯度上升算法

```Python
def stocGradAscent1(dataMatrix, classLabels, numIter=150):
    m,n = shape(dataMatrix)
    weights = ones(n)   #initialize to all ones
    for j in range(numIter):
        dataIndex = range(m)
        for i in range(m):
            alpha = 4/(1.0+j+i)+0.0001    #apha decreases with iteration, does not 
            randIndex = int(random.uniform(0,len(dataIndex)))#go to 0 because of the constant
            h = sigmoid(sum(dataMatrix[randIndex]*weights))
            error = classLabels[randIndex] - h
            weights = weights + alpha * error * dataMatrix[randIndex]
            del(dataIndex[randIndex])
    return weights
```
* 特点：
    1.  $\alpha$在每次迭代的时候都会调整，这会缓解回归系数的高频波动；
    2.  $\alpha$从一个较大的值逐步减小并趋于常数（非0），与固定的$\alpha$相比，加快了算法的收敛速度；
    3.  通过随机选取样本来更新回归系数，将减少回归系数的周期性波动。

