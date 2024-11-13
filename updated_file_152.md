# 蚂蚁图数据库再获LDBC权威测试世界第一

近日，国际权威图数据库测试机构国际关联数据基准委员会（LDBC）公布了行业通用的社交网络基准测试（LDBC SNB）最新结果。蚂蚁集团图数据库TuGraph打破官方审计测试纪录，再次拿到世界第一，这一纪录较LDBC早前公布的最高纪录吞吐量提升了52%，也超过了两年前由TuGraph保持的世界纪录1倍以上。

据LDBC官方发布的报告，在本次测试中，TuGraph在不同规模的数据集下均表现优异，在最大数据规模300G的数据集（8亿个结点，53亿条边）上，TuGraph的吞吐率较上一次官方纪录提升了52%，在系统事务性、可恢复性、正确性、稳定性等方面均达到官方标准，体现了TuGraph高并发低延迟的强大性能优势。

为了更加贴近真实场景使测试更加严谨，TuGraph还采用了Client/Server部署，将客户端和服务器分别部署在两台服务器上，在更严苛的条件下（固有网络延迟与网络波动）完成了本次测试。

蚂蚁集团也是LDBC最新的金融图数据测试基准Finbench的发起人和主要建设者。

## 关于LDBC和SNB测试

LDBC，即“关联数据基准测评委员会”（Linked Data Benchmark Council），是全球公认的图数据库领域基准指南制定者与测试机构，与TPC并称为国际数据库行业两大权威技术组织。

SNB，即社交网络基准测试（Social Network Benchmark），是由LDBC开发的面向图数据库的基准测试（Benchmark）之一。SNB测试由于更贴近现实系统，同时包含了读写任务，简单和复杂查询，规定了系统的响应时间，更能体现系统的综合性能，是目前图数据行业最成熟和通用的性能测试。

LDBC SNB测试由指定的第三方机构进行，从数据导入到结果验证均由第三方在云平台上执行，最终结果由LDBC执行委员会进行审计并公布，最大限度的保证了结果的可信性。同时，SNB还公布了测试过程所用的程序和脚本，以及测试过程中产生的详细结果，进一步确保了测试的可复现性。

## 关于TuGraph

蚂蚁集团图数据库TuGraph是基于图模型的一站式数据存储和分析系统，擅长处理大规模关联数据的管理和分析，如社交关系、物流服务、设备管网、金融交易等场景，数千倍优化分析性能，天然具备数据可视化展示。

TuGraph拥有业界领先的集群规模和性能，是蚂蚁集团金融风控能力的重要基础设施，显著提升了欺诈洗钱等金融风险的实时识别能力和审理分析效率，提供了稳定的决策支持能力，其中，支撑支付宝的重要风险识别能力提升了近10倍，风险审理分析效率提升90%。TuGraph已被成熟应用于安全风控、信贷风控、知识图谱、数据血缘、资金分析、流量归因分析、会员关系等场景，并面向金融、工业、政务服务等行业客户。