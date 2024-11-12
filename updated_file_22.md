# TuGraph 更新日志

# 4.5.0 (2024-09-05)

**重大变更:**

1. cypher & gql ast统一完成，查询引擎全面升级。
2. browser前端架构升级，支持图分析。

**优化和错误修复:**
1. cypher支持解析emoji符号。
2. lgraph_cli输出执行计划格式优化。
3. 优化可变长路径查询性能。
4. 支持使用pair unique索引upsert边。
5. cypher查询点边时支持map类型参数。

**接口更变:**
1. list plugin返回结果增加code type。
2. 丰富algo procedure返回结果内容，支持输出到文件。
3. 支持使用DataX导入TuGraph。


# 4.3.2 (2024-07-25)

**重大变更:**

1. browser前端架构优化，并设置为默认前端。
2. 初步支持向量数据类型。

**优化和错误修复:**

1. 修复边属性条件过滤不生效。
2. 优化RPM包产出物的大小，去掉一些不再用的文件。
3. cypher语句性能优化。
4. 修复cypher order by报错。
5. bolt客户端支持不强制设置graph名字。
6. 文档修复。
7. cypher & gql ast统一。

**接口更变:**

1. 内置最短路径存储过程支持多个边条件过滤。

# 4.3.1 (2024-06-13)

**重大变更:**

1. 支持非唯一组合索引
2. cypher支持GQL ASTNode解析链路
3. 支持通过websocket进行Bolt数据传输

**优化和错误修复:**

1. 修复内置存储过程algo.native.extract
2. 其他错误修复

**接口更变:**

1. lgraph_server启动时默认不打开增删procedure的功能，需配置enable_procedure为true才可增删procedure
2. java client新增callCypher接口可返回header信息

# 4.3.0 (2024-05-21)

**重大变更:**

1. DB-GPT中实现TuGraph上的知识图谱构建与检索
2. DB-GPT支持TuGraph
3. 属性分离模式下的schema变更优化，支持快速变更
4. 支持组合索引

**优化和错误修复:**

1. 新增内置存储过程：upsertVertexByJson、upsertEdgeByJson、createVertexLabelByJson、createEdgeLabelByJson、dropAllVertex
2. 支持自定义HA模式下snapshot开始时间
3. 新增地理类型数据相关内置函数
4. 新增开发者说明文档
5. 其他错误修复

**接口更变:**

1. 数据导入时默认为属性分离模式
2. 启动参数新增`ha_first_snapshot_start_time`

# 4.2.0 (2024-04-11)

**重大变更:**

1. 更新2024年的RoadMap
2. 文档重构：更加清晰文档目录结构
3. HA支持witness角色，并新增HA管理工具`lgraph_peer`
4. bolt支持流式返回、参数化查询

**优化和错误修复:**

1. 错误码优化
2. TuGraph-DB-Browser集成打包优化
3. 支持HA模式下的全量快速在线导入
4. 存储过程支持多个cpp文件上传
5. Audit log变为Json形式
6. 文档错误修复
7. 其他错误修复

**接口更变:**

1. bolt支持流处理和参数化查询
2. 存储过程支持多个cpp文件上传

# 4.1.0 (2023-12-25)

**重大变更:**

1. 兼容bolt协议，支持java/js/python/go/rust/cli客户端；

2. 支持全量快速在线导入

3. 支持异步任务管理

4. 支持空间数据类型

5. GQL功能演进

**优化和错误修复:**

1. 图学习引擎支持异构图

2. 高可用支持异步存储快照

3. 完善边索引，添加 pair-unique 唯一性

4. 解耦KV层抽象

5. 支持M1 Docker

6. 优化CI&CD流程，显著缩短时间

**接口更变:**

1. 新增bolt协议标准

2. 新增空间数据类型

3. 索引中 is_unique 参数改用 IndexType 枚举，涉及 AddVertexIndex/AddEdgeIndex 接口；

# 4.0.1 (2023-9-28)

**重大变更:**

1. 支持 Temporal 属性排序的升序和降序

2. 添加 5 个图算法

**优化和错误修复:**

1. 修复Python存储过程不能立马kill的问题

2. 类型名称支持 255 字节

3. 其他错误修复

**接口更变:**

1. 修复 import.conf 中的 temporal 关键字

# 4.0.0 (2023-9-6)

**重大变更:**

1. ISO GQL 标准查询语言支持

2. 开源11个常用图算法

**优化和错误修复:**

1. 提供m1芯片的编译和运行Docker

2. 前端支持丰富的探索功能

3. 修复导入时的索引BUG

4. 其他错误修复

**接口更变:**

1. Proto版本更新至1.2.0

2. 客户端支持ISO GQL

# 3.6.0 (2023-8-11)

**重大变更:**

1. 支持高可用组件

2. 日志系统升级

**优化和错误修复:**

1. 修复令牌（Token）问题

2. 更加友好的 CI 日志

3. 支持微秒级的时间（Datetime）属性

4. 其他错误修复

# 3.5.1 (2023-7-14)

**重大变更:**

1. 支持图学习引擎

2. 支持图属性和拓扑分离存储

3. 支持 Rust 存储过程

**优化和错误修复:**

1. 将 fma-common 移动至 include

2. 整合存储过程文档

3. 新增点和边的实时统计

4. 其他错误修复

# 3.5.0 (2023-6-5)

**重大变更:**

1. 支持POG（APOC）

2. 全新的可视化操作页面

3. 全新的产品文档，支持 readthedocs

**优化和错误修复:**

1. 修复边索引错误

2. 更新 antlr

3. 其他错误修复

# 3.4.0 (2023-3-11)

**重大变更:**

1. 支持图分析（OLAP） Python 接口（API）

2. 全新的 lgraph_import

3. 支持 DataX 导出

**优化和错误修复:**

1. 支持 4MB 的字符串（String）属性

2. 更新 lgraph_result 的 json 文件格式

3. 其他错误修复

# 3.3.4 (2023-1-28)

**重大变更:**

1. 暂无

**优化和错误修复:**

1. 修复 WAL 同步错误

2. 更新 Python REST 客户端

3. 其他错误修复

# 3.3.3 (2022-12-23)

**重大变更:**

1. 优化 WAL 性能

2. 新增英文文档

3. 更新 JWT 安全问题

**优化和错误修复:**

1. 修复边约束问题

2. 支持 Ubuntu 的 Docker

3. 支持使用 pthread 运行全文索引

4. 其他错误修复

# 3.3.2 (2022-11-21)

**重大变更:**

1. 支持 OGM

2. 提升 UT 覆盖率至 87%

3. 支持使用静态库创建链接

**优化和错误修复:**

1. 更新 python 存储过程接口文档

2. 更新文档

3. 添加 khop 存储过程

4. 其他错误修复

# 3.3.1 (2022-10-14)

**重大变更:**

1. 优化图分析引擎

2. Cypher 支持路径语法

**优化和错误修复:**

1. 修复 RWLock 错误

2. 重写文档

3. 清理 Dockerfile

# 3.3.0 (2022-08-05)

正式开源