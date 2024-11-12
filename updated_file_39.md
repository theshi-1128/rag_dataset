# 技术规划

## 1. 简介

该文档是 TuGraph-DB 未来开发的规划，包括正在开发中、不在开发计划里、已经开发完成但不在开源版本里的功能等。

TuGraph-DB定位开源高性能图数据库，图数据采用集中存储的方式，短期 **不会** 考虑基于分片（Sharding）的数据切分，
而将支持主备复制的模式来解决高并发读的场景，采用云上存储解决存储容量的问题。

## 2. 已完成功能

TuGraph-DB于2022年9月1日开源，TuGraph-DB在社区的反馈声中，进行日常BUG修复，自身能力得到了完善。

| 版本号   | 功能                               | 时间         |
|-------|----------------------------------|------------|
| 3.3.0 | 开源初版                             | 2022.9.1   |
| 3.3.1 | 图分析引擎重构，多模式支持                    | 2022.10.14 |
| 3.3.2 | OGM支持，UT覆盖率提升                    | 2022.11.21 |
| 3.3.3 | 链接认证机制迭代，加入英文文档                  | 2022.12.23 |
| 3.3.4 | 支持上云，梳理LDBC SNB Audit流程          | 2023.1.28  |
| 3.4.0 | 支持OLAP Python API, 离线导入升级        | 2023.3.11  |
| 3.5.0 | 支持POG，前端升级，文档梳理                  | 2023.6.5   |
| 3.5.1 | 图学习引擎，Procedure Rust API，存储属性分离  | 2023.7.14  |
| 3.6.0 | 高可用开源，日志系统升级                     | 2023.8.11  |
| 4.0.0 | ISO GQL支持，新增11个开源图算法，支持m1 Docker | 2023.9.6   |
| 4.0.1 | 支持时序边排序，新增5个开源图算法                | 2023.9.28  |
| 4.1.0 | 支持Bolt协议，支持快速在线全量导入，支持地理空间数据类型   | 2023.12.25 |
表格内容描述：该表格记录了不同版本号的软件更新信息，包含三列：版本号、功能和时间。

1. 在版本3.3.0中，发布了开源初版，时间为2022年9月1日。
2. 版本3.3.1进行了图分析引擎重构，并且支持多模式，发布时间为2022年10月14日。
3. 版本3.3.2增加了OGM支持，并提升了UT覆盖率，发布时间为2022年11月21日。
4. 版本3.3.3迭代了链接认证机制，并加入了英文文档，发布日期是2022年12月23日。
5. 版本3.3.4支持上云，并梳理了LDBC SNB Audit流程，时间为2023年1月28日。
6. 版本3.4.0支持OLAP Python API，并对离线导入进行了升级，发布时间为2023年3月11日。
7. 版本3.5.0支持POG，进行了前端升级，并梳理了文档，发布时间为2023年6月5日。
8. 版本3.5.1发布了图学习引擎，增加了Procedure Rust API，并实现了存储属性分离，时间为2023年7月14日。
9. 版本3.6.0实现了高可用开源，并升级了日志系统，发布时间为2023年8月11日。
10. 版本4.0.0支持ISO GQL，并新增了11个开源图算法，还支持m1 Docker，发布时间为2023年9月6日。
11. 版本4.0.1支持时序边排序，并新增了5个开源图算法，发布时间为2023年9月28日。
12. 版本4.1.0支持Bolt协议，快速在线全量导入，并支持地理空间数据类型，发布日期是2023年12月25日。

总体来看，该表格展示了该软件从版本3.3.0到4.1.0的发展历程，涵盖了功能的持续增强、支持的新技术、以及版本升级的时间节点。

除此之外，TuGraph-DB搭建了较为完善的质量体系，涵盖自动化的单元测试、集成测试、性能测试等。

更详细的描述可以在源码目录在的 "[root]/release/CHANGELOG.md" 文件查看。

## 3. 2024年功能更新

在2024年度，我们计划的功能更新包括：

| 版本号   | 功能                 | 计划时间    |
|-------|--------------------|---------|
| 4.2.x | HA支持Witness角色和管理工具 | 2024.3  |
| 4.2.x | Bolt支持流处理和参数化查询    | 2024.3  |
| x.x.x | GeaX支持Cypher       | 2024.6  |
| x.x.x | 支持组合索引             | 2024.6  |
| x.x.x | 数据导入功能优化           | 2024.6  |
| x.x.x | 【社区功能】支持地理数据类型使用   | 2024.6  |
| x.x.x | Cypher能力提升         | 2024.9  |
| x.x.x | 支持Schema快速变更       | 2024.9  |
| x.x.x | 向量化支持              | 2024.12 |
| x.x.x | RPQ支持              | 2024.12 |
| x.x.x | 【可选】查询引擎升级         | 2024.12 |
| x.x.x | 【社区功能】支持GraphAr    | 2024.12 |
表格内容描述: 该表格包含三个列名：版本号、功能和计划时间。

- 第一行：版本号为4.2.x，功能为HA支持Witness角色和管理工具，计划时间为2024年3月。
- 第二行：版本号为4.2.x，功能为Bolt支持流处理和参数化查询，计划时间为2024年3月。
- 第三行：版本号为x.x.x，功能为GeaX支持Cypher，计划时间为2024年6月。
- 第四行：版本号为x.x.x，功能为支持组合索引，计划时间为2024年6月。
- 第五行：版本号为x.x.x，功能为数据导入功能优化，计划时间为2024年6月。
- 第六行：版本号为x.x.x，功能为支持地理数据类型使用的社区功能，计划时间为2024年6月。
- 第七行：版本号为x.x.x，功能为Cypher能力提升，计划时间为2024年9月。
- 第八行：版本号为x.x.x，功能为支持Schema快速变更，计划时间为2024年9月。
- 第九行：版本号为x.x.x，功能为向量化支持，计划时间为2024年12月。
- 第十行：版本号为x.x.x，功能为RPQ支持，计划时间为2024年12月。
- 第十一行：版本号为x.x.x，功能为可选查询引擎升级，计划时间为2024年12月。
- 第十二行：版本号为x.x.x，功能为支持GraphAr的社区功能，计划时间为2024年12月。

总体来说，该表格概述了不同版本号下计划实施的功能及其对应的时间安排，涵盖了多个功能优化和社区支持，预计执行时间从2024年3月至12月不等。

## 4. 期望社区共创的功能

目前团队研发精力并不能实现我们对TuGraph-DB的全部期望，在功能的梳理中，我们发现有一系列值得挖掘的想法，
团队也有一些初步的探索，期望下面功能能够在社区中共同研发。

| 版本号   | 功能                      | 计划时间   |
|-------|-------------------------|--------|
| x.x.x | 图算法库丰富                  | 2024.x |
| x.x.x | 属性默认值支持                 | 2024.x |
| x.x.x | Embedded TuGraph-DB最佳实践 | 2024.x |
| x.x.x | Bolt显式事务支持              | 2024.x |
| x.x.x | List、Map和Decimal等数据类型扩展 | 2024.x |
| x.x.x | 探索多存储引擎                 | 2024.x |
表格内容描述：该表格包含三列，分别是“版本号”、“功能”和“计划时间”。 

1. 第一行数据显示，版本号为x.x.x，功能为“图算法库丰富”，计划时间为2024.x。
2. 第二行数据显示，版本号为x.x.x，功能为“属性默认值支持”，计划时间为2024.x。
3. 第三行数据显示，版本号为x.x.x，功能为“Embedded TuGraph-DB最佳实践”，计划时间为2024.x。
4. 第四行数据显示，版本号为x.x.x，功能为“Bolt显式事务支持”，计划时间为2024.x。
5. 第五行数据显示，版本号为x.x.x，功能为“List、Map和Decimal等数据类型扩展”，计划时间为2024.x。
6. 第六行数据显示，版本号为x.x.x，功能为“探索多存储引擎”，计划时间为2024.x。

总体而言，该表格展示了即将推出的版本中主要功能的计划时间，所有功能均定于2024年的某个时间实施，涵盖图算法、数据库最佳实践及多种数据类型的支持等方面。

一些更加简单的功能，我们会在github的issue中打上 good first issue 的标签，欢迎对图数据库感兴趣的技术爱好者共同研讨。