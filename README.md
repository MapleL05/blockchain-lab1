# Blockchain-lab1
区块链与数字货币 大作业1 TuGraph图数据库操作
## 1.实验基础知识
### 1.1图数据库

图数据库是一种以属性图模型为核心的数据库，由顶点（节点）、边（关系）、属性三部分组成。擅长存储和分析关联性强的数据，适合金融交易、社交网络、资金溯源、风控检测等场景，相比传统关系型数据库，在多层级关联查询上效率更高、结构更直观。
### 1.2TuGraph简介
TuGraph是由蚂蚁集团与清华大学联合研发的高性能分布式国产图数据库，支持万亿级数据容量、低延迟图查询与图计算，提供Web可视化管理界面，支持Cypher查询语言，可通过Docker快速部署，适合高校课程实验与企业真实业务落地。
### 2.3 实验数据集介绍
本次实验使用Elliptic++比特币交易数据集，包含两个核心文件：
1.elliptic_txs_classes.csv：交易节点数据，包含交易编号txId、交易类别class。
2. elliptic_txs_edgelist.csv：交易边数据，包含转出编号txId1、转入编号txId2，代表比特币资金流向关系。
类别说明：class=1为合法交易，class=2为非法交易，其余为未知交易。
## 2.TuGraph安装与启动步骤
1. 启动电脑端Docker软件，确保左下角显示Engine running正常运行。
2.  在D盘新建共享文件夹，用于Docker容器文件挂载。
3.   打开Windows命令行，执行镜像拉取与容器启动命令，配置7070网页端口、7687协议端口映射。
```cypher
docker pull tugraph/tugraph-runtime-ubuntu18.04
docker run -d -v D:\tugraph:/mnt -p 7070:7070 -p 7687:7687 tugraph/tugraph-runtime-ubuntu18.04 lgraph_server -d run --enable_plugin true
```
4.   在浏览器中输入地址localhost:7070，输入账号admin与密码登录TuGraph可视化操作界面，进入系统主页面。
<img width="2256" height="1504" alt="cac130c8aacadb0349aff806884ea112" src="https://github.com/user-attachments/assets/19c75e03-8773-42a9-91c1-850f0d3dbdd2" />

## 3.图建模与数据导入过程
### 3.1图建模操作
1. 进入TuGraph左侧建模模块，新建顶点标签：Transaction（交易节点）。
2. 为节点添加属性：id（INT64、主键）、class（INT64）、time_step（INT64）。
3. 新建关系标签：Transfer（资金流向），起始标签和目标标签均选择Transaction，代表交易与交易之间的资金流转关系。
<img width="2256" height="1504" alt="image" src="https://github.com/user-attachments/assets/39013fce-063b-40b5-bb7d-69f0942e3205" />
<img width="2256" height="1504" alt="image" src="https://github.com/user-attachments/assets/f00afa6e-dd0e-484e-8c6d-35f6b4fb7fe1" />

### 3.2数据导入步骤
1. 进入**数据导入**页面，上传两个CSV数据集文件。
2. 对elliptic_txs_classes.csv选择类型为点，映射关系：txId对应id、class对应class。
3. 对elliptic_txs_edgelist.csv选择类型为边，映射关系：txId1对应起点、txId2对应终点。
4. 分别保存映射规则，执行导入操作，两个文件均导入成功，数据完整入库。
<img width="2256" height="1504" alt="image" src="https://github.com/user-attachments/assets/d86d905f-c5ba-4236-86fc-ee7651fe8d58" />

## 4.Cypher语句增删改查实验
### 4.1新增操作
```cypher
CREATE (n:Transaction {id:999999, class:2})
```
功能：手动新增一条编号为999999、类别为非法的交易测试节点。
<img width="2256" height="1504" alt="b205e69a3736710f51ec999a0c2de1d9" src="https://github.com/user-attachments/assets/868b3e0b-8402-46b5-aaec-310ac74011ac" />

### 4.2修改操作
```cypher
MATCH (n:Transaction {id:999999}) SET n.class=1
```
功能：匹配指定交易节点，将其类别从非法修改为合法交易
<img width="2256" height="1504" alt="a15fa203a441111216e67b6c570a222b" src="https://github.com/user-attachments/assets/9dd71a0c-34e7-404f-aa05-e6f3b7ad94d2" />

### 4.3删除操作
```cypher
MATCH (n:Transaction {id:999999}) DELETE n
```
功能：匹配自定义测试节点并删除，恢复原始数据集环境。
<img width="2256" height="1504" alt="11341e5357473c29247694794def4227" src="https://github.com/user-attachments/assets/43c5cafa-a4b3-4619-b802-29d7e1b3628e" />

### 4.4基础查询
```cypher
MATCH (n:Transaction) RETURN n LIMIT 10
```
功能：查询并返回前10条交易节点数据，可切换图谱视图和表格视图查看详情。
<img width="2256" height="1504" alt="b745033b4cce3d9364733d0280416ca0" src="https://github.com/user-attachments/assets/9945ca0e-7259-4a54-8fa4-74ea1bcd5ba7" />
<img width="2256" height="1504" alt="81b1b5b31cd1dea58cd1ea119993f839" src="https://github.com/user-attachments/assets/927c204b-8c93-4201-939b-b514071f3b3c" />

### 4.5复杂条件查询
```cypher
MATCH (n:Transaction)
WHERE n.class = 2
RETURN n.id, n.class
```
功能：筛选查询所有class为2的非法交易，输出交易编号与类别，完成条件过滤复杂查询。
<img width="2256" height="1504" alt="be099bedd015bab4a3a1e12aadc9499c" src="https://github.com/user-attachments/assets/51ae2a4d-3e7f-4229-b8fd-38617287af42" />

## 5.实验总结
通过本次实验，我完成了TuGraph图数据库的Docker部署、网页端登录使用，掌握了图数据库节点与关系的建模方法，成功完成比特币交易CSV数据集的上传、字段映射与批量导入。同时熟练掌握Cypher语言的增、删、改、基础查询、复杂条件查询语法，理解了属性图模型在金融交易网络中的应用价值。
