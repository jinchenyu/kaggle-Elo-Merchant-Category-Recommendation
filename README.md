# Elo Merchant Category Recommendation 27 Solution


## model

由于target(信用卡忠诚度)分布满足符合正太分布的同时，有少量严重偏离期望的离群值，如下图

![target分布.png](https://github.com/jinchenyu/kaggle-Elo-Merchant-Category-Recommendation/blob/master/images-flods/target分布.png)

而本题使用RMSE评测指标，RMSE对异常值敏感，所以合理预测异常值，就是本题关键，也就是为什么会有选手，在public board进行修改异常值，以大幅提分

如果直接使用回归模型预测，模型无法较好的学习到正太分布部分，直接预测分布如下

![直接预测分布.png](https://github.com/jinchenyu/kaggle-Elo-Merchant-Category-Recommendation/blob/master/images-flods/直接预测分布.png)


可以看到，模型非常倾向于负值

所以我们在此，使用去掉异常值的回归模型，来保证预测结果的正太分布的同时，使用二分类模型，对异常值进行预测。
最后使用二分类预测结果，对回归进行偏移。
既 sub = regression + binary_pred * outlier

但是在此，我们的做的并不好，没有考虑加入异常值概率带来的误差

参考[第一名方案](https://www.kaggle.com/c/elo-merchant-category-recommendation/discussion/82036)，
可以修改为
sub = (1-binary_pred) * regression + binary_pred * outlier

最终结果为去掉异常值的模型和直接预测模型8:2加权得到

最终成绩 Private Score 3.60205   Public Score 3.67791

## feature

基本统计特征

滑窗统计

历史访问记录的merchant id,merchant category id,category 1/2/3等类别特征CountVectorizer

时间差特征，包括用户上下次购买时间差，用户最长连续购买天数

