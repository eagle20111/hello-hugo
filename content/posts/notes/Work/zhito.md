---
title: "Zhito"
slug: ""
date: 2022-11-22T17:04:51+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: []
katex: true
mermaid: false
draft: false
---

## LaneGCN模型移植和部署

### LaneGCN模型

#### 一、数据处理

(1) `read_argo_data()`:
  - data['city']
  - data['traj']: shape: 32 x 50 x 2
  - data['steps']: shape: 32 x 50
(2) `get_obj_feat()`:
  - data['feat']: 障碍物前20秒轨迹点的相对位置坐标（vector）(只有19个，当前时刻补零)
  - data['ctrs']: 各个障碍物20帧的位置信息
  - data['orig']: agent在20帧时刻的位置，作为局部坐标的原点
  - data['theta']: agent在20帧的偏转角
  - data['rot']: 旋转矩阵以及坐标转换
  - data['gt_preds']: M x 30 x 20 各个障碍物在后3秒的真实轨迹运动信息
  - data['has_preds']: 标记在当前时刻真值是否被观测到
  > 坐标转换
  > $$\begin{bmatrix} x_g & y_g\end{bmatrix}\begin{bmatrix} cos\theta & -sin\theta \\ sin\theta & cos\theta \end{bmatrix} =\begin{bmatrix} x_l & y_l\end{bmatrix}$$

(3) `get_lane_graph()`:
  - 以agent第20帧坐标为圆心，取boundingbox范围内的所有道路
  - 取lane_ids
    - 针对lane_id获得lane，然后通过lane.centerline获取道路中心点的位置，lane.centerline代表lane的一系列中心点
    - 对中心点针对agent的坐标原点进行坐标转换
    - 取ctrs、feat、turn、control、intersect = [], [], [], [], []
      - lane.centerline有10个点的坐标，相当于取9个lane node
      - ctrs: 每条lane centerline的10个点的前后相加除以2，作为lane node的中心点
      - feat：lane.centerline的10个点的前后位置相减，相当于9个lane segment，feat指vector feat
      - turn: [a, b] a=1: 左转 b=1： 右转
      - control: 是否有交通标志
      - intersect: 是否处于路口

  - 获得lane node之间的拓扑关系
    - node_idcs: lane node index (0 ~ 9) (9~18) ...
    - num_nodes: lane node 的总数量
    - lane node 之间的拓扑关系
      - pre['u']: 1 ~ 9 ... v 是 u 的 pre
      - pre['v']: 0 ~ 8 ...
      - suc['u']: 0 ~ 8 ... v 是 u 的 suc
      - suc['v']: 1 ~ 9 ...
      > 注意： pre['u'] pre['v'] suc['u'] suc['v'] 指的是lane node之间的关系
    - pre_pairs、 suc_pairs、 left_pairs、 right_pairs: 指的是lane 与 lane 之间的关系
  - 总结:
    - graph['ctrs']: lane node 中心点的坐标
    - graph['num_nodes']: lane node 点的数量
    - graph['feat']: lane node 的前后相减 矢量特征
    - graph['turn']: 道路是否为左转或者右转
    - graph['control']: 道路是否有交通标志
    - graph['intersect']: 是否处于交通路口
    - graph['pre']: lane node 前后拓扑关系
    - graph['suc']: lane node 前后拓扑关系
    - graph['lane_idcs']: lane node index
    - graph['pre_pairs']: lane 与 lane 之间的前后关系
    - graph['suc_pairs']:
    - graph['left_pairs']:lane 与 lane 之间的左右关系
    - graph['right_pairs']:

#### 二、数据前处理

(1) `preprocess()`:
  - 主要针对lane graph的数据进行数据前处理工作
    - 第一步: 根据lane_idc获得lane node数量和lane的数量
    - 第二步: 计算lane node 两两之间的距离
    - 第三步: 根据pre_pair、 suc_pair构建pre、suc矩阵
             根据left_pair、 right_pair构建left、right矩阵
    - 第四步: 取出角度(偏转角)$\theta < \pi/4$ 的lane node 节点
      构建left['u'] v 是 u 的左边lane node 节点
         left['v']
         right['u'] v 是 u 的右边lane node 节点
         right['v']

#### 三、LaneGCN 具体的网络结构

(1)、 data输入结构(以batch_size = 2 为例)
    对于data['feat']类型为list，len(list) = 2, list[0]=>其中一个scenario
    对于data['graph']类型为list，len(list) = 2, list[0]=>其中一个dict，dict中存储lane node信息
(2)、 对于`actor_gather()`和`graph_gather()`两个函数
  - actor_gather():
    - 输入: list (data['feat']) 输出: actors (M x 3 x 20), actor_idcs
    - 作用： 在此处，把batch输入的障碍物特征进行concatenation整合到一起，并完成转置，将时序放到第一维，维后续的FPN网络做准备
  - graph_gather()
    - 输入：list (data['graph']) 输出: graph
    - 作用: 把batch中输入的lane graph特征进行叠加(concatenation)，用于后续训练
(3)、ActorNet(): 提取障碍物actor的特征
  - 输入: actors (M x 3 x 20)
  - 输出: actor net output (M x 128)

  ActorNet 网络结果：
  - groups: 
      ```c++
      group: Res1d(3, 32)     Res1d(32, 32)
      group: Res1d(32, 64)    Res1d(64, 64)
      group: Res1d(128, 128)  Res1d(128, 128)
      ```
  - outputs [ groups[0], groups[1], groups[2]]
  - lateral [conv1d[32, 128], conv1d[64, 128], conv1d[128, 128]]
  - 整体结构:
    ```c++
                (31 x 128 x 5)             (31 x 128 x 5)
    groups[2] Res1d(128, 128)  => conv1d(128, 128)     ====>  interpolate (31 x 128 x 10)
              Res1d(64, 128)                                      ||
             /\                                                   ||  
             || (31 x 64 x 10)             (31 x 128 x 10)        \/
    groups[2] Res1d(128, 128)  => conv1d(64, 128)     ====>    sum (31 x 128 x 10)    
              Res1d(64, 128)                                      || 
             /\                                               interpolate (31 x 128 x 20)
             || (31 x 32 x 20)               (31 x 128 x 20)      ||
    groups[2] Res1d(128, 128)  => conv1d(32, 128)     ====>    sum (31 x 128 x 20)            
              Res1d(64, 128)                                      ||
             /\                                                  res1d(128, 128)  
             ||                                                   ||  [:,:, -1]
        input: 31 x 3 x 20                                    output: 31 x 128
    ``` 
(4)、MapNet(): 提取lane node的特征
  - 输入: graph['idcs', 'ctrs', 'feats', 'turn', 'control', 'intersect', 'pre', 'suc', 'left', 'right']
  - 输出: feat, graph['idcs'], graph['ctrs']
    - graph['idcs']: lane node 的index
      - len(graph['ctrs'][0]): 1206
      - len(graph['ctrs'][1]): 954 
    - graph['ctrs']: 2160 x 2
  - 网络结构: 
    - self.input: Linear(2, 128) Linear(128, 128)
    - self.seg: Linear(2, 128) Linear(128, 128)

    - self.fuse() => dict()
      - self.fuse['ctr']: Linear(128, 128)
      - self.fuse['norm']: nn.GroupNorm(gcd(1, 128),  128)
      - self.fuse['ctr2']: Linear(128, 128), norm = groupNorm, ng = 1
      - self.fuse['left']: Linear(128, 128)
      - self.fuse['right']: Linear(128, 128)
      - self.fuse['pre0'] ~ self.fuse['pre5']: Linear(128, 128)
      - self.fuse['suc0'] ~ self.fuse['suc5']: Linear(128, 128)

  - 流程:
    ```c++
    lane node ctrs: n x 2 = self.input => n x 128
    lane node feats: n x 2 = self.seg => n x 128
    || relu
    n x 128
    || => resblock  =============================>
    pre0 ~ pre5
    suc0 ~ suc5  temp.index_add_(0, graph[pre][i]['u'], self.feat(graph[k1][k2]['v']))
    解释: 把feat的第v行(value)加到temp的第u行上
    对pre0 ~ pre5 / suc0 ~ suc5 / left / right执行相同操作 (图注意力)
    || 
    然后经过self.fusr['norm'] 和 relu模块加上 resblock
    ||
    得到输出: feat: n x 128
            graph["idcs"]
            graph["ctrs"]
    ```
(5)、A2M(): lane node 和 agent node 交互
  - 在A2M模块中，agent node 是 lane node, context node 是 vehicle node (以laen node为中心， actor node为context)
  - 输入: feat(nodes), graph, actors, actors_idcs, actor_ctrs
  - 输出: feat (n x n_agts)
  - 网络结构:
    - meta = torch.cat(graph['turn'], graph['control'], graph['intersect'])
    - feat = self.meta(Linear(128, 128))
    - 针对feat(lane node feature), graph['idcs'], graph['ctrs'], actors, actor_idcsm, actor_ctrs
      循环指行两次graph attention 操作
  
  - Atten网络结构；
    - 输入: agts, agts_idcs, agts_ctrs, ctx, ctx_idcs, ctx_ctrs， dist_th
    - 流程: 
      ```c++   
      - agts ======================================> resblock
      ||
      agts_ctrs 和 ctx_ctrs 两两求distance
      mask = dist < dist_th 求距离小于threshold的mask(筛选出距离小于threshold的车和道路)
      ||
      idcs = torch.nonzero(mask)
      hi.append(idcs[:, 0]) => row_idcs for agts
      wi.append(idcs[:, 1]) => col_idcs for ctxs
      || 
      dist = agt_cts[hi] - ctx_ctrs[wi] 根据threshold筛选出来的agent node 和context node 求distance
      ||  self.dist((2, n_ctx) (n_ctx, n_ctx))
      dist = self.dist(dist): n x n_ctx
      || self.query(n_agts, n_ctx)
      query = self.query(agts[hi]): n x n_ctxs
      || ctx = ctx[wi]: n x n_ctx
      ctx = torch.cat((dist, query, ctx), 1)
      ||self.ctx(3 * n_ctx, n_agt) (n_agt, n_agt)
      cts = self.ctx(ctx): n x n_agt
      ||agts = self.agts(n_agt, n_agt)
      agts.index_add_(0, hi, ctx) 把context的特征根据hi (index) 加到agents上
      || 加上resblock
      输出： n x n_agt
    ```
(6)、M2M(): map node 和 map node 交互
  - 输入： node graph
  - 输出: N x 128
  - 此处执行的操作和MapNet()相同

(7)、M2A(): map node 和 vehicle node交互
  - 输入: actors, actor_idcs, actor_ctrs, nodes, node_idcs, node_ctrs
  - 输出: actors (n x 128)
  - 以vehice node为agent，lane node为context nodes，执行Attention注意力机制
(8)、A2A(): vehicle node 和 vehicle node 交互
  - 输入: actor, actor_idcs, actor_ctrs
  - 输出: n_actos x 128
  - 以vehicle node为agent nodes，同时以vehicle node为context nodes为context nodes，执行注意力机制
(9)、PredNet() 预测网络
  - 输入：actor_feats, actor_idcs, actors_ctrs
  - 输出: out['cls'], out['reg']
  - 网络结构:
    - pred[0] [LinearRes(128, 128), Linear(128, 2 x 30)]
    - pred[1] [LinearRes(128, 128), Linear(128, 2 x 30)]
    - pred[2] [LinearRes(128, 128), Linear(128, 2 x 30)]
    - pred[3] [LinearRes(128, 128), Linear(128, 2 x 30)]
    - pred[4] [LinearRes(128, 128), Linear(128, 2 x 30)]
    - pred[5] [LinearRes(128, 128), Linear(128, 2 x 30)]
  - 流程: 
    ```c++
     根据actor_feats分别输入到PredNet中
      `Preds[n* 120, n* 120, n* 120, n* 120, n* 120, n* 120]`
       ||
       [n * 1 * 120, n * 1 * 120, n * 1 * 120, n * 1 * 120, n * 1 * 120] 
       || torch.cat()
       n x 6 x 120
       ||reg.resize()
       n x 6 x 30 x 2
       || `dest_ctrs = reg[:, :, -1].detach()` (n x 6 x 2)
       || `self.dest(actors, actors_ctrs, dest_ctrs)`： 相当于把每个障碍物不同模态的轨迹终点与原点（20帧时刻轨迹点）之间的距离叠加到输出特征上中(叠加到actors上)，用到后面去求每个模态的概率(评分)
       || `self.cls(Linear(128, 128) Linear(128, 1))`: 作为cls的模态的打分
       || `cls, sort_idcs = cls.sort(1, descend=true)` 按照1维降序排列(最后让预测结果中的每一个障碍物的6个模态按降序排列，即第一条的轨迹的打分最高)
    -   输出： out['cls']、 out['reg']
    ```
(10) PredLoss的计算
  - 取出真值轨迹中有轨迹点的最后一个时间戳
  - 根据真值轨迹是否有观测轨迹点的index取出需要进行比较的reg和cls (根据真实观测值取出相应的预测轨迹)
  - dist指的是每一个真值最后一个可观测点位置与6个模态轨迹相应预测轨迹点的l2距离
  - 把6个模态的l2距离叠加到一起，取出其中距离最小的轨迹点idx
  - mgn = cls[row_idcs, min_idcs].unsqueeze() - cls 将cls最小的分值 减去 cls分值
  - mask0：筛选出每条距离fde最小的轨迹，fde < 2 的轨迹
    mask1：筛选出距离减去fde最小轨迹距离 < 0.2 的轨迹
  - 求出mgn[mask0 * mask1]: 取min_dist < 2 但是排除距离它比较近的轨迹点
  - mask = mgn < 0.2
  - 最后计算cls和reg的loss
    - cls_loss += self.config["mgn"] * mask.sum() - mgn[mask].sum   
    $\text{cls}_\text{loss} = max(0, {c_k} + \epsilon - \hat{c_k})$
    - reg_loss = self.reg_loss(reg[has_preds], gt_preds[has_preds]) # 预测值和真值的huber loss

### 在移植以及部署过程中，碰到的问题以及解决方法

(1). 由于模型应用场景的要求，对模型进行以下修改：

  - 道路节点的upsample()和downsample()的处理：由于高速场景下，直线路段比较多，且高精地图提供的道路节点比较稀疏，我们多lane node进行upsample处理，每间隔20m取一个lane node，增加道路的头铺信息；另一方面，在路口场景，由于道路节点比较密集，造成模型计算消耗过大，我们使用downsample的方式，每间隔5个lane node 选取一个lane node，来减少算例消耗
  - 在原来的模型中，只关心focal agent的预测轨迹；在项目中，我们将ego vehicle的位置作为局部坐标原点，通过一次推理来获得周围车辆未来三秒的轨迹；
  - 在高速场景下，考虑到ego vehicle在行车过程中更倾向于考虑自车前方的道路拓扑结构和障碍物，那么我们在选取道路结构时， 倾向于以自车前方40m， 半径为200m的区域来来构造lane graph。
  - 同时，在筛选道路过程中，碰到单个lane过长的情况，采用直接提取lane node的方式，将超出范围内的lane node直接截断，来减少算力消耗
  - 考虑在高速场景下，并不需要6个模态，倾向于想模型修改为2-3模态进行训练。

(2). 基于TensorRT模型推理方面遇到的问题以及解决方案

  - scatterElement自定义算子的编写
    - 原因: torch.index_add_() 重复索引的实现
     - __global__ 函数
       ```c
       __global__ void scatter_elements_op(float *output, const int *index, const float *update, const int ncolumns)
      {
        int idx = blockDim.x * blockIdx.x + threadIdx.x;
        int transformidx = index[idx] * ncolumns + idx % ncolumns;
        atomicAdd(&output[transformidx], update[idx]);
      }
      ```
    - 

  - torch.nonzero() 操作无法实现，注意力机制的修改 