# 推荐系统架构及粗排设计

招聘联系方式： 

![image-20210130190615748](https://i.loli.net/2021/01/30/c4zi5JY28CNWZy3.png)

## 推荐系统架构

整体架构图：

![image-20210130192109897](https://i.loli.net/2021/01/30/jDNv57wPFGOqEtR.png)

**召回：**

索引召回： 依据画像结果做 精准id  如 感兴趣歌曲 创作者召回

泛社交召回： 社交关系 关注 社团等

模型召回 



**粗排层**：

粗排模型-模型蒸馏？ 



### 重点1 - 召回

召回模型迭代过程（四类方法）：

![image-20210130193748416](https://i.loli.net/2021/01/30/6KZW1bDjiGQ2NzB.png)

共线性： 利用共线性得到相似关系

向量化： 矩阵分解 以及 item2vec  利用序列关系去生成 item向量

深度表征： 仍然是向量化（不仅利用序列特征，还用到了user，item本身特征）**YoutubeDNN 以及DSSM**

因果纠偏：针对bias问题 **负样本选择**  **反事实镜像人群**



### **重点2 - 内容冷启动及筛选**

![image-20210130195610004](https://i.loli.net/2021/01/30/ATgLievlu56XayP.png)

私域流量和公域流量？



### 重点3 - 排序模型多维迭代

![image-20210130201327782](https://i.loli.net/2021/01/30/OwY3G284yo6dbRP.png)



主要在deepfm的基础上改进：

deepfm 要求 fm中 **不同特征要求embedding维度相同**  **KFM**可以自定义宽度 （二阶特征交叉）

muti-head attention 学习特征与特征之间的重要性

cgc、ple 相关知识 



## 粗排模块算法设计

**粗排模型**的设计以及 **多样性**保证 两方面内容

![image-20210130203612886](https://i.loli.net/2021/01/30/E4Hg38IlDrycAq5.png)

全民k歌是一个非常大的ugc平台 **级联架构**



**粗排存在的必要性**： 

· 防止召回到精排的漏斗过大
· 相较于召回，更强调排序（目标约束） --------- 召回内部没有排序 一种策略 召回许多条目 此时 粗排可以有打分 
· 相较于精排，更强调性能（算力约束） --------- 精排无法处理较大规模的候选集  

可以针对不同策略的召回有统一的打分 （对不对不确定 是一种思路）



**目前的两种思路：** 

ROUTE1 从召回的角度：集合选择方案   最大问题- 目标不一致 各个环节目标不一致 

ROUTE2 从排序的角度：排序预估方案   表达能力强，目标对齐程度高  



route2 双塔模型结构：

![image-20210130225230866](C:\Users\MYJ\AppData\Roaming\Typora\typora-user-images\image-20210130225230866.png)

**双塔优缺点**：

![image-20210131152645894](https://i.loli.net/2021/01/31/6EMH5daFrsGmBKx.png)

引出模型蒸馏： Transfer Learning with Soft Labels  （0-1之间的连续值）

![image-20210131152950298](https://i.loli.net/2021/01/31/4Js3fotz5TnFLyj.png)

其中上面的是Teacher模型，复杂的模型，下方的student模型，简单

预训练好的teacher网络输出 一个 logits， student网络不仅要拟合真实label， 还要拟合logits（soft labels）

（相当于 teacher对子网络的学习加了指导以及约束）

![image-20210131153502507](https://i.loli.net/2021/01/31/B5YIPKUs8Dl9ST1.png)

分为模型蒸馏 和 特征蒸馏 ：

![image-20210131153754010](https://i.loli.net/2021/01/31/qnNGW8rO62x74Bw.png)

之前双塔无法做到 **特征交叉**  使用模型蒸馏 即引入更复杂的模型 又在teacher网络中引入了交叉特征

teacher网络即 精排网络？使用精排模型来蒸馏 （目标一致性 以及 学习上限都有保证）

![image-20210131154008653](https://i.loli.net/2021/01/31/mvPag1dOIoFkfcY.png)

具体训练上线实践： 

![image-20210131154717083](C:\Users\MYJ\AppData\Roaming\Typora\typora-user-images\image-20210131154717083.png)

更多探索方向：

![image-20210131155342033](https://i.loli.net/2021/01/31/gPcXhZMAwBOdGV8.png)

召回可能偏重于**多样性** 精排偏重于具体顺序 精准性  粗排如何衡量二者 



## 多样性调节算法

![image-20210131161039033](https://i.loli.net/2021/01/31/dmAOnR7aMBfUNc3.png)

目前常见的一些方案路线： 

![image-20210131161251249](https://i.loli.net/2021/01/31/mjVkgLX3BT6Fiao.png)