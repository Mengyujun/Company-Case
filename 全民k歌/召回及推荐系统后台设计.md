# 全民k歌推荐后台架构

大纲：

![image-20210201161835703](https://i.loli.net/2021/02/01/wE4y2ku8PD1v7ah.png)



## K歌推荐后台架构

![image-20210201161928644](C:\Users\MYJ\AppData\Roaming\Typora\typora-user-images\image-20210201161928644.png)



## 召回

召回过程演进：

![image-20210201162208383](https://i.loli.net/2021/02/01/hFKgsrbowOkdz89.png)



### v1： 基于redis的kv倒排索引

![image-20210201162848549](https://i.loli.net/2021/02/01/LsF5Xm3W6TtewNj.png)



### v2：双mongo+本地KV索引

![image-20210201163121400](https://i.loli.net/2021/02/01/gyJiGhoqslRvCW7.png)



### v3： 双mongo + 本地双buff全缓存

![image-20210201163445458](https://i.loli.net/2021/02/01/s1McaIuQxoegHLO.png)



## 排序

### 特征平台

![image-20210201164822259](https://i.loli.net/2021/02/01/3KxUY5XZ6G4iOmn.png)



### 特征格式选择（收益极大）

主要改进：

- 去掉map

- 去掉string

![image-20210201165444091](https://i.loli.net/2021/02/01/3ljiT9h6ws2p5mn.png)



### 特征聚合 与 预测框架

![image-20210201165352680](https://i.loli.net/2021/02/01/fc91pRHTMDynaZB.png)

## 推荐去重

![image-20210201170724217](https://i.loli.net/2021/02/01/8DTi2ZxWkVIdKrL.png)

改进布隆过滤器：

![image-20210201170959879](https://i.loli.net/2021/02/01/Ifga9ZhwiMjHKoQ.png)

![image-20210201171433342](https://i.loli.net/2021/02/01/TEZCA8FfhGsHWjU.png)





## debug

**日志回溯**可视化帮助定位推荐流程

![image-20210201171659735](https://i.loli.net/2021/02/01/ricZuEjK4pxQPD2.png)

![image-20210201171812160](https://i.loli.net/2021/02/01/axvTKIHOQhjPwWk.png)



# 全民k歌内容挖掘与召回

内容挖掘：

![image-20210201181040819](https://i.loli.net/2021/02/01/1ntVxv3HFcAdwQG.png)



## 召回

召回模型总览： 

![image-20210201181719697](https://i.loli.net/2021/02/01/yYFWqN48SmpunEU.png)

主要有 **画像属性召回， 模型召回， 其他召回（热点， 地理）**



四个迭代流程：

- 共线性  -- cf

- 属性向量化 

  ![image-20210201182139951](https://i.loli.net/2021/02/01/9JNOYymzpB6Qohl.png)

- 深度表征（模型化召回）

- 因果纠偏 



### 模型化召回

![image-20210201182310684](https://i.loli.net/2021/02/01/5iKgdF6ltumIb9P.png)

注意其中的问题 召回的几大问题：

- 负样本选择
- 无法引入交叉特征
- 需要debias



### 负样本选择

![image-20210201182828260](https://i.loli.net/2021/02/01/Qi5G2VAaEkecHt1.png)



### 增强单塔的表达能力  

增加了序列attention![image-20210201183043358](https://i.loli.net/2021/02/01/zvnalxWfIP4ZYqo.png)

### 最终模型

![image-20210201183149374](https://i.loli.net/2021/02/01/2FLN4rtxcwgXkjv.png)

### 召回模型bias

![image-20210201183435146](https://i.loli.net/2021/02/01/4PT1VnoAqDykWva.png)

![image-20210201183455399](https://i.loli.net/2021/02/01/i9WxP8XgKdQmzyj.png)

![image-20210201183647602](https://i.loli.net/2021/02/01/c1OAqIfmrXnH8eg.png)