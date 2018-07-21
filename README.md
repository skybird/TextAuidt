文本审核模块
========

### 基于敏感词的文本审核及过滤
基于DFA通过tire树实现了一个高效的敏感词过滤器      
入口及使用方法见 `text_filter.py`

#### 实现的功能
- 基于数据库或者txt文件初始化分类初始化敏感词列表
- 添加敏感词及类别,并且保存到文件中
- 判别语句中是否含有敏感词(默认贪婪,匹配所有敏感词)
- 过滤语句,可以自定义过滤字符

#### 性能测试
与python自建的in,replace性能比对

**测试集1**         
语句数量 10,000        
敏感词数量 744      

| 文本过滤器 | 运行时间(s) | 语句平均运行时间(个/ms)|
| :------ | :------ | :------ |
| 基于DFA的文本过滤器 | 0.0590 | 0.0059 |
| 朴素文本过滤器 | 0.2730 | 0.0273 |


**测试集2**
语句数量 10000      
敏感词数量 15000

| 文本过滤器 | 运行时间(s) | 语句平均运行时间(个/ms)|
| :------ | :------ | :------ |
| 基于DFA的文本过滤器 | 0.420 | 0.042 |
| 朴素文本过滤器 | 6.5060 | 0.6506 |

可以看到DFA方式的文本过滤器的速度基本是朴素写法的十倍        
在敏感词数据量增加的情况下性能表现也更稳定.

### 基于文本分类的审核
这里采用了 [文本分类语料库（复旦）测试语料](http://www.nlpir.org/?action-viewnews-itemid-103) 来进行文本分类测试      
共20种文章分类,9374篇文档,共约120M

#### 按照7:3划分训练/测试集进行文本分类测试
基于 sklearn 和 TF-IDF 结合不同文本分类模型来进行文本分类测试     
结果如下

| 文本分类器 | 训练时间(s) | 分类时间(s) | 精度 | 召回 | f1 |
| :------ | :------ | :------ | :------ | :------ | :------ |
| 多项式贝叶斯(alpha=0.1) | 0.2850 | 0.0720 | 0.77723 | 0.78500 | 0.75392 |
| 多项式贝叶斯(alpha=0.01) | 0.2810 | 0.0720 | 0.85151 | 0.85221 | 0.83334 |
| 多项式贝叶斯(alpha=0.001) | 0.2950 | 0.0740 | 0.86374 | 0.87003 | 0.85894 |
| 多项式贝叶斯(alpha=0.0001) | 0.2800 | 0.0710 | 0.86432 | 0.86929 | 0.85785 |
| 支持向量机SVM | 182.2480 | 54.6420 | 0.03297 | 0.18158 | 0.05581 |
| 决策树 | 13.9700 | 0.0410 | 0.84313 | 0.84033 | 0.84039 |
| 逻辑回归 | 13.3400 | 0.0520 | 0.82730 | 0.86558 | 0.84040 |
| 随机森林(n=10) | 4.1970 | 0.1100 | 0.77579 | 0.77089 | 0.75014 |
| 随机森林(n=20) | 8.1570 | 0.1830 | 0.82469 | 0.81545 | 0.79146 |
| 随机森林(n=25) | 10.6610 | 0.2110  | 0.83221 | 0.82436 | 0.80422 |
| 随机森林(n=30) | 14.1020 | 0.2810 | 0.83121 | 0.81730 |0.79248 |
| kNN聚类(n) | 0.0210 | 3.1180 | 0.84410 | 0.86075 | 0.84292 |
| kNN聚类(n+1) | 0.0220 | 3.2940 | 0.84291 | 0.85964 | 0.84207 |
| kNN聚类(n+2) | 0.0210 | 3.2700 | 0.84319 | 0.86038 | 0.84310 |
| kNN聚类(2n) | 0.0210 | 3.1490 | 0.83054 | 0.84515 | 0.82371 |
| GBDT|   |   |   |   |   |


### About
h-j-13      
2018-07-19 
