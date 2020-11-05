# 推荐系统中冷启动问题探索与实践

推荐系统挑战：

1. Open data space or incomplete input data 数据不充分
2. Statistical characteristics rather than true understanding 基于统计数据的推荐 
3. online & offline的差异
4.  信息茧房 （过分推热门 小众兴趣顾及不到）



## 冷启动问题及解决办法

**新用户，新物品**，系统冷启动（新地方，新功能）

常见的冷启动解决办法： 

1.  基于规则 

   - 非个性化 热播 排行榜
   - 简单分类- 粗分类（性别年龄地域）

2. user/item 信息充分利用 （ 自身信息， 第三方信息渠道抓取，主动询问获取，  基于内容 视觉文本 ）

3. deep Learning 模型上  DropoutNet (drop the features, not the node )

   对特征进行dropput，增加泛化性 可以看到图中out比例不高时时可以 增加cold recall的 

   ![image-20201105221158352](https://i.loli.net/2020/11/05/FLUINxrqChlXzkV.png)

4. 强化学习 bandit



## Hulu中的冷启动问题

### 新用户兴趣探索

### 新内容冷启动