# 外滩大会蚂蚁开源大规模图学习系统AGL

AGL（AntGraphLearning）的GitHub 地址：https://github.com/TuGraph-family/TuGraph-AntGraphLearning



## 1.图：是一种描述和认知世界的方式

图是一种描述和认知世界的方式，它能够表达万物之间的关联，是事物关系的原生表达。基于图进行学习，可以挖掘出事物的内在属性以及事物之间的联系。图的描述能力很强，能够进行高阶严谨的数据建模。在工业界，图学习技术有广泛的应用，如社交推荐、资金风控、企业授信、药物设计等。从 2016 年以来，图学习在各领域都得到了广泛的应用，图学习也成了当下人工智能领域的研究热点之一。

蚂蚁多元化的业务场景在生产经营、风险防控过程中，围绕商户、用户、企业、产品、风险事件等沉淀了海量的图数据和复杂的动态网络关联，自 2016 年开始，蚂蚁机器智能图学习团队持续深耕复杂、异构、动态网络的图学习应用，实现对复杂关系的拓扑感知和长尾稀薄客群的客户画像和风险防控。图学习技术在蚂蚁的业务中也得到了广泛的应用，并取得了优秀的业务成果。

蚂蚁图学习系统极大地提升了蚂蚁业务的效益和效率，如应用于网商银行数字化供应链“大雁”系统，使供应链识别精度提升 50%，贷款可得率从 30% 提升到 80%；应用于支付宝数字开放平台，帮助生态商家提升其权益、供给分发效率超过 50%。

图学习通过机器学习方法对图数据进行分析和建模的一种方法，TuGraph-AntGraphLearning（以下简称为 AGL）是行业首个通用的工业图学习系统，实现了万亿规模图数据上的信息协同和结构感知，构建了多个行业数字化图智能方案，也沉淀了多个优秀的算法实践，发表 CCF-A/B 类国际期刊会议论文 60 余篇，授权发明专利 40 余项，五项国际榜单/竞赛第一，也是图神经网络国家标准的核心参与单位。

2023年9月7日，AGL 外滩大会开源蚂蚁多年打磨的工业级图学习系统和一系列经过业务实践验证的图学习算法，通过开源这些技术和成果，希望为广大开发者提供一个强大的工具和平台，帮助大家更好地应用图学习技术解决实际业务问题，同时通过社区共建，吸收产研优秀的系统与算法实践，持续降低图学习的应用门槛，促进图学习技术的交流和创新，推动图学习在各行各业更的广泛应用。



## 2. 图学习要做什么？

古人云 “物以类聚，人以群分”。那么反过来根据人的关联关系，以及关联的这些人的特征，大致也能够定义一个人是什么样的人。图学习要做的事便是联合图中的结构信息（边，关联关系）以及特征信息（点/边特征）学习出图（点/边）的内在属性，从而应用在推理预测任务中，典型的有节点分类、链接预测、图预测等。

通常可以通过图神经网络进行图学习任务，基本思想是节点汇集邻居信息，并进行聚合得到当前节点的属性信息。通过嵌套多次这样的处理，节点便能汇聚到更远的邻居的信息。由于通过“边”汇聚邻居的信息，这样的方式可以巧妙的将图的结构信息和属性信息结合起来。

## 3.工业级图学习任务有什么特点？

工业场景的复杂性给图学习任务带来巨大挑战。以蚂蚁的图学习任务为例，其特点概括如下：

图数据复杂：典型的图学习业务场景有数十亿点，数百亿边，复杂的业务场景有千亿条边。而图学习过程中，每个点的信息（embedding）依赖其邻居的信息，需要跨节点的数据交换和聚合传播，这样的数据依赖使得图学习难以像经典 DNN 一样进行并行/分布式训练。此外图数据中可能存在丰富的点/边类型，图也可能具有时序动态性。

任务类型复杂：为满足风险防控的实效性、一致性、计算性能要求，图学习需要提供离线全图学习、近线准事实增量预测、在线毫秒级高性能推理等要求，也给图学习的训练和推理提出了更高的要求。

因此工业场景中，需要图学习框架能够适应大规模的图数据，具有良好的扩展性，能够表达丰富类型的图数据；同时能够完善提供训练与推理能力，适应工业场景中稳定性，一致性，资源成本等方面的要求。

## 4. AGL如何应对这些挑战

AGL 为工业级大规模图学习任务提供全链路解决方案，包括支撑大规模图学习的离线批量预处理及在线交互式实时采样学习方案。其中，本次开源的离线批量预处理方案，在图表示及样本生成阶段，为了应对工业级图学习任务中复杂而规模庞大的图数据，AGL 支持了同质、异质、动态等图数据类型，设计了 k 阶邻域生成器，预先抽取目标节点的子图信息，从而把大图转换为一个个数据独立的小图。在抽取过程中，AGL 设计了索引和采样机制，通过配置可以实现数值过滤/逻辑过滤，权重采样/Top K 采样等，支持点/边/图级别的图样本生成。AGL 将这些不同种类的图样本通过统一的结构表达出来，把这个关于图的结构与特征的综合体称为 Graph Feature.

在图训练和预测阶段，因预先生成的图样本解决了数据依赖问题，图训练可以复用成熟的 DNN 训练方案（如分布式方案 PS，AllReduce 等）。通过图样本解析工具将统一表达的 Graph Feature 解析成模型所需的 点/边 特征矩阵，邻接矩阵等信息。在建模层，用经典的 Encoder-Decoder 的范式描述建模过程，并进行图卷积层、图算法层、图模型层的划分，方便用户复用。图预测过程可以直接使用图样本生成阶段已经生成好的样本，保证训练和预测阶段图样本的一致性。

在图学习算法层面，本次开源也提供一系列经典的开箱即用且业务实践应用过的算法，涵盖异质/同质/动态图等不同算法，对应节点表征/链接预测/自监督等多种任务。同时我们适配了开源算法库（如 PyG）, 减轻用户开发负担。

## 5. AGL 当前开源版本（v0.1）的主要能力

AGL 当前开源版本已经能够支持较大规模复杂图数据的全链路图学习任务。总结起来，AGL 目前主要提供如下能力：

灵活可扩展的图样本生成：基于 Spark 支持大规模图数据k阶邻域生成，解除图学习任务中的数据依赖问题；支持丰富的图类型，如同质、异质、动态等图数据类型；抽取子图过程，支持灵活丰富的过滤与采样能力；支持点/边/图多个级别的图样本生成，并通过统一的 GraphFeature 进行表达。

简洁易用的图训练与图预测：因预先生成的图样本解决了数据依赖问题，用户可以使用像使用 DNN 的方式一样训练 GNN ，入门简单；使用统一的数据格式表达和解析不同类型，不同级别的图样本，用户只需简单的配置就能支持读取和解析相应的图样本，获得模型所需的点/边 特征矩阵，邻接矩阵等信息，方便易用；图预测过程可以直接使用图样本生成阶段已经生成好的样本，保证训练和预测阶段图样本的一致性。

丰富的图学习算法：用经典的 Encoder-Decoder 范式描述算法建模过程，并进行图卷积层、图算法层、图模型层的划分，可复用性强；提供提供一系列经典的开箱即用且业务实践应用过的算法，涵盖异质/同质/动态图等不同算法，对应节点表征/链接预测/自监督等多种任务；适配开源算法库，减轻开发负担；

目前 AGL 以 Docker 镜像的形式提供全流程的运行环境。即使是在普通PC电脑上，也能方便的基于 AGL 在一些小型图数据上进行图学习任务。

## 6. 未来工作

AGL 将持续的系统优化和能力创新，并将优秀的系统和算法实践开放到社区，本次开源为 AGL v0.1 版本。未来，AGL 在系统层面未来会开放交互式采样、TuGraph 多引擎联动、复杂图特征计算等，为用户提供高效易用的图学习系统。在图数据表示层面，通过与新一代图谱语义框架（Semantic-enhanced Programmable Graph, SPG）的联动，将复杂的多主体深度关联显性知识化、稀疏关系稠密化，实现数据知识化增强。在推理学习能力上，驱动逻辑规则与图学习的双向驱动，支撑逻辑规则挖掘、规则引导可解释图学习等。欢迎各界同仁一起建设 AGL，推进图学习的技术创新和各领域落地应用。