# 精排与多目标模型迭代  

![image-20210131164949231](https://i.loli.net/2021/01/31/7nHGrmwOE1jUVFh.png)



## 特征处理

常见的特征处理方式： 

![image-20210131165953679](https://i.loli.net/2021/01/31/nuFKkTjw1WCSyrc.png)

### 采样以及加权

负样本的选取

**skip-above**： 同一session中 最后一次点击之前的所有曝光样本作为负样本  （在快速划过的场景可能未真正被用户观察到）

**click-around**: 点击周围的上下曝光作为负样本

 

## CTR模型演进

**CTR预估-业界发展史**  

![image-20210131170732906](https://i.loli.net/2021/01/31/OIjzR6rhTnacymv.png)

其中树模型的 缺点注意一下 



### CTR预估-自我演进  

![image-20210131170927592](https://i.loli.net/2021/01/31/OIjzR6rhTnacymv.png)

主要是一个 **DeepKFM**

![image-20210131213430843](https://i.loli.net/2021/01/31/Y5PAFMOmdn2xvXe.png)

增加autoint 之后:

![image-20210131214103179](https://i.loli.net/2021/01/31/9ucWLhJ5lMix1rB.png)



## CVR预估

**ctr的缺陷:**

缺陷：
 只关注点击， 用户点击后有很大比例没有达到有效播放
 封面党CTR高， 内容质量差
 用户其他显式反馈没有得到刻画， 需求无法得到满足  



播放时长20s以上的作为 正样本 





### CVR预估

**ESMM**

![image-20210131214745213](https://i.loli.net/2021/01/31/Zqiy9TUgoEBN14s.png)

预测时长是回归任务,和正常的ctr预估(分类任务)不同, 

其中右上图是 有关观看时长和次日留存的关系， 尝试将回归转化为分类 



cvr结构的改进： 

![image-20210131215653245](https://i.loli.net/2021/01/31/rgPmw7tCpGMuoV4.png)

mmoe机构 global step 过低 -》 snr？

snr的具体结构呢？ 



![image-20210131215933427](C:\Users\MYJ\AppData\Roaming\Typora\typora-user-images\image-20210131215933427.png)

注意多任务学习中的问题 



展望： 

 GNN For CTR
 MultiConvertTask
 CGC+L0正则化  



# 多目标模型在推荐短视频流的应用  

大纲：

![image-20210131220503102](https://i.loli.net/2021/01/31/Vqw63R7ZsgFlPEk.png)

天级更新



## 多目标的意义

- 评估指标的多样性
  - 用户留存， 人均VV、 时长， 关注率、 点赞率等  
- 行为差异性  （不同行为本身的差异）
  - 不同行为表达兴趣强度差异、 行为稀疏性不一样  
  - 可以分为 显示 隐式 正负反馈 等不同行为
- 物品偏差： 单目标衡量存在不足，诱导分享等  
- 用户表达偏好不一样  



**当前迭代**：

![image-20210131221521957](https://i.loli.net/2021/01/31/zihmUubTsYSdJFw.png)



## 多目标label优化



- 一开始是一个信息流推荐场景，主要对不同的行为赋予**不同的样本权重**  （即**点击率+样本权重**）

![image-20210131221852702](https://i.loli.net/2021/01/31/YXpm9gbkqTINC13.png)



- 之后更新升级场景为大卡片形式（自动播放）， 优化目标发生变化：

![image-20210131222058632](https://i.loli.net/2021/01/31/8HxRIKWvctjUuQA.png) 

此时缺点比较明显, 即阈值n的选取 以及对效果的影响



- **完播目标**

  不同时长 选取80%的分位点作为正负样本区分依据

  确定正负样本比例： **4~5:1， 取80%分位点**  

  ![image-20210131222525510](https://i.loli.net/2021/01/31/HKQWdbcMjnm5sY8.png)



- 停留时长目标 **时长模型**

  由下图可见 播放时长和 留存指标关联很大， 因此转化为将 停留时长作为 目标

  ![image-20210131222957495](https://i.loli.net/2021/01/31/CYduVefXZOD97Ex.png)

  其时长模型的迭代:

  1. 以**log(停留时长)作为样本权重**训练完播模型   (迭代周期长 优化空间小  )

  2. 时长模型单独建模 

     

- 其他时长目标

  在完播以及时长预估的基础上 增加其他目标 （有效播放， 完成，快划）

  ![image-20210131224027676](C:\Users\MYJ\AppData\Roaming\Typora\typora-user-images\image-20210131224027676.png)



-  交互多目标

  区分一级行为和二级行为 一级行为构建交互多目标 二级行为因为稀疏的原因 以样本权重的方式加入模型

![image-20210131224306296](https://i.loli.net/2021/01/31/h42Hb1tKPOEAUS8.png)



## 多目标建模

迭代过程： 

- 单目标单独建模  -优化效率低

- 多目标目标选择 - 完播和时长联合优化 

- 多目标模型2.0 - 增加**有效播放**的指标 模型结构改进

- 多目标模型3.0 - 增加播放完成的指标 模型结构改进 

- 交互多目标模型

  



### 多目标模型1.0

![image-20210131224831867](https://i.loli.net/2021/01/31/WnyfLudEpAgXcDP.png)

**uid 以及 itemid 因为其数量较大 8维embedding不能表征 因此单独构建embedding（64或者128）**



### **多目标模型2.0**

![image-20210131225224397](https://i.loli.net/2021/01/31/TrZcnDl5XO17FCB.png)

注意几大改进： sequence attention（din网络） 以及 dcn 以及单独的 wide以及fm侧  以及 跨场景 



### 多目标模型3.0

![image-20210131230528743](https://i.loli.net/2021/02/01/tSBmwFQgGP9kNU6.png)



### 交互多目标模型

![image-20210131230822155](https://i.loli.net/2021/02/01/an8QBXtfSHuWFLV.png)



## 多目标模型融合

### loss融合

![image-20210201154239200](https://i.loli.net/2021/02/01/bd8X53r4N9MBxSi.png)

### 多目标分数融合

![image-20210201154336707](https://i.loli.net/2021/02/01/Rq92wWJ13Yr5Vbt.png)

![image-20210201154707066](https://i.loli.net/2021/02/01/Q6amKh7yI9fGcxC.png)

## 总结和展望

![image-20210201154557881](https://i.loli.net/2021/02/01/ZgdKXm4AsSwpcOE.png)