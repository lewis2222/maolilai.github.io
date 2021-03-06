# 数据仓库与ETL面试问题总结


---
### 1. 什么叫数据仓库？数据仓库的特点？
- 数据建模大师Inmon的定义是：数据仓库是一个面向主题的、集成的、相对稳定的、反映历史变化的数据集合，其作用是支持企业的数据分析；
- 数据仓库的核心要点有两个：集成，规范；
- 用自己的话来说，首先，能够有效集成多个异构的数据源，集成后按照主题进行了重组，并包含历史数据，而存放在数据仓库中的数据一般不再修改；
- 其次，用于支持业务分析，这一点不同于操作型数据库、事务型系统、OLTP等，数据仓库偏向于OLAP系统；


---
### 2. 什么叫OLAP？用途是什么？
- Online Analytical Processing，即联机分析处理；
- 它使分析人员能够迅速、一致、交互地从各个方面观察信息，以达到深入理解数据的目的；
- OLAP是数据仓库的主要应用，支持复杂的查询分析，并提供直观简易的查询结果，最终目的在于指导业务决策；


---
### 3. 数据仓库和数据库有什么区别？
- 数据库一般是面向事务的OLTP系统，有频繁的CRUD操作；数据仓库是面向分析的OLAP系统，一般一次写入，多次读取；
- 数据库一般是用来存储当前交易数据；数据仓库存储一般存储的是历史全量数据；
- 数据库的设计一般符合三范式，有最大的精确度和最小的冗余度，有利于数据的插入；数据仓库一般遵循星型模型，有利于查询；


---
### 4. 数据仓库的基本架构是什么？
- 贴源层ods：将多个异构的数据源的数据，导入到贴源层，用于仓库层数据的加工；
- 仓库层dw：对贴源层数据进行清洗、转换、解析等操作，生成可用于数据分析的细粒度数据，一般是多个字段组成的宽表；
- 应用层dm：按照一定的业务逻辑，对仓库层的数据进行各种聚合、分组、累加等计算，从而支持业务分析，满足特定的业务需要；


---
### 5. 数据仓库有哪几种模型设计方法？特点分别是什么？
- 实体建模法：按照业务主体、完成的业务流程、及其说明，来构建数据仓库，是最能简单划分业务的建模方法，本质上是一种描述客观事件的方法；
- 维度建模法：由Kimball大师所提倡，按照事实表、维表来构建数据仓库，其代表就是星型模型、雪花模型；该方法针对各个维度，进行了大量的统计、分类、排序等预处理，极大的提升了数据仓库的处理能力，比范式建模法具有性能上的优势；
- 范式建模法：由Inmon大师提倡，主要解决在关系型数据库中存储数据的问题，三范式也是大部分RDBMS采用的建模方法。三范式的含义是：每个属性值唯一，不具有多种含义；每个非主属性值必须完全依赖于整个主键；每个非主属性不能依赖于其他关系中的属性；
- 不同的方法，其实就是从不同的角度看业务中的问题，不管从技术层面还是业务层面，其实代表的是哲学上的一种世界观；


---
### 6. 模型设计的思路是什么？是业务需求驱动，还是数据驱动？
- 构造数据仓库有两种方式：一是自上而下，一是自下而上。二者都要达到同一个目标，就是构建企业级数据仓库。实际上在建设数据仓库的时候，一般都参照这两种方式结合使用，没有硬性规定；
- Kimball大师推崇“自下而上”的方式，即是业务需求驱动。他认为建设数据仓库应该按照实际的应用需求，加载需要的数据；对于不需要的数据，则不要加载到数据仓库中。这种方式建设周期较短，客户能够很快看到结果。针对客户的需求，需求要什么就做什么，是一种短平快的设计思路，也是实际工作当中较多使用的思路；
- Inmon大师推崇“自上而下”的方式，即是数据驱动。他认为，一个企业建立唯一的数据中心，其中数据是经过整合、清洗、去掉脏数据、标准的，能够提供统一的视图。要建立这样的数据仓库，并不需要去思考支持哪些应用，而是要从整个企业的环境入手，分析其中的概念，应该有什么样的数据，有着完整的设计蓝图，会考虑到很全面的设计。但因此，这样的设计也很耗费时间；


---
### 7. 模型设计的步骤？
- 以维度建模法为例，构建企业级数据仓库五步法如下：
- 一、确定主题。即确定数据分析或前端展现的主题(例:某年某月某地区的啤酒销售情况)。主题要体现出某一方面的各分析角度(维度)和统计数值型数据(量度)之间的关系，确定主题时要综合考虑；
- 二、确定量度。确定主题后，需要考虑分析的技术指标(例:年销售额等等)。它们一般为数据值型数据，其中有些度量值不可以汇总；些可以汇总起来，以便为分析者提供有用的信息。量度是要统计的指标，必须事先选择恰当，基于不同的量度可以进行复杂关键性指标(KPI)的设计和计算；
- 三、确定事实数据粒度。确定量度之后，需要考虑该量度的汇总情况和不同维度下量度的聚合情况.例如在业务系统中数据最小记录到秒，而在将来分析需求中，时间只要精确到天就可以了，在ETL处理过程中，按天来汇总数据，些时数据仓库中量度的粒度就是”天”。如果不能确认将来的分析需求中是否要精确的秒，那么我们要遵循”最小粒度原则”，在数据仓库中的事实表中保留每一秒的数据，从而在后续建立多维分析模型(CUBE)的时候，会对数据提前进行汇总，保障产生分析结果的效率；
- 四、确定维度。维度是分析的各个角度.例:我们希望按照时间，或者按照地区，或者按照产品进行分析。那么这里的时间，地区，产品就是相应的维度。基于不同的维度，可以看到各个量度汇总的情况，也可以基于所有的维度进行交叉分析。维度的层次(Hierarchy)和级别(Level)。例:在时间维度上，按照”度-季度-月”形成了一个层次，其中”年”，”季度”，”月”成为了这个层次的3个级别。我们可以将“产品大类-产品子类-产品”划为一个层次，其中包含“产品大类”、“产品子类”、“产品”三个级别。我们可以将3个级别设置成一张数据表中的3个字段，比如时间维度；我们也可以使用三张表，分别保存产品大类，产品子类，产品三部分数据，比如产品维度。建立维度表时要充分使用代理键.代理键是数据值型的ID号码(每张表的第一个字段)，它唯一标识了第一维度成员。在聚合时，数值型字段的匹配和比较，join效率高。同时代理键在缓慢变化维中，起到了对新数据与历史数据的标识作用。
- 五、创建事实表。在确定好事实数据和维度后，将考虑加载事实表。业务系统的的一笔笔生产，交易记录就是将要建立的事实表的原始数据。我们的做法是将原始表与维度表进行关联，生成事实表。关联时有为空的数据时(数据源脏)，需要使用外连接，连接后将各维度的代理键取出放于事实表中，事实表除了各维度代理键外，还有各度量数据，不应该存在描述性信息。事实表中的记录条数据都比较多，要为其设置复合主键各蛇引，以实现数据的完整性和基于数据仓库的查询性能优化；


---
### 8. 谈谈你对星形模型和雪花模型的理解和认识？
- 星型模型和雪花模型，都是应用维度建模法的典型代表；
- 星型模型：核心是一个事实表、及多个维度表组成；
- 雪花模型：它是星型模型的扩展，不同的是其维度表也会被维度划分，进一步分解到附加的维度表当中；
- 星座模型：由多个事实表组合，维表是公共的，可以被多个事实表共享。星座模型是数据仓库最常使用的模型；


---
### 9. 什么叫维度和度量值？
- 维度是观察事物的角度，例如，观察销售额，可以从日期、品类、价格范围等方面去分析，这个方面就是维度；
- 度量值是被观察的结果，或者说，是根据维度、对事物进行聚合计算的结果，例如，销售额在周末有一万元；


---
### 10. 什么是元数据？有什么作用？
- 元数据，MetaData/ data about data，是描述数据及其环境的数据；
- 元数据可以帮助用户理解数据仓库的数据；
- 元数据是保证数据质量的关键；
- 元数据独立于平台，可以支持数据需求的变化，保证了数据平台的通用性和移植性；


---
### 11. OLTP与OLAP的区别？
- 基本含义不同。OLTP指的是联机事务处理，OLAP指的是联机分析处理；
- 功能不同。OLTP是事务性的，目的是处理日常事务，例如数据库的增删查改；OLAP是分析性的，目的是为业务决策提供数据支持；
- 实时性要求不同。OLTP对实时性要求高，OLTP对实时性要求没那么高；
- 数据量不同。OLTP处理的数据量一般没有OLAP那么大；


---
### 12. 说一说你整个项目从数据采集到指标、报表的这整个过程是怎么做的？


---
### 13. 拉链表是什么？怎么实现的？
- 拉链表的作用，是针对一些数据量很大、而每天变化的字段相对不多的表，为了达到记录其每天的状态、而又要节省存储空间的目的，采取的一种方案；
- 遇到上述的情况，一般处理方法有三种：
    - 1、每天都重抽数据，删除旧表。这种方法使用简单，节省空间，但无法保存历史快照；
    - 2、每天的数据都保留一个备份。这种方法使用简单，但需要的存储空间实在太大；
    - 3、使用拉链表。
- 实现拉链表的方法，是在原表的结构上，新增两个字段，分别表示该条记录的有效开始时间start_date、有效结束时间end_date。如果拉链表中的数据需要更新，那么将end_date改为当日；然后在拉链表中插入新的记录，对应的start_date改为当日，end_date改为'9999-99-99'；以此类推。


---
### 14. 事实表有哪几种类型？（阿里云面试题）
- 事务事实表。记录事务层面的数据，是最原子的数据，因此也被成为原子事实表。在事务发生后，事实数据会被插入表内，数据就不再进行更新，每次数据变化都会新增插入一条记录。例如在信贷行业当中，每张借款的订单都是一张事务事实表的记录；
- 周期快照事实表。采用周期性的时间间隔来记录事实表的每一份快照，是在事务事实表基础之上加工出来的，只会新增插入。例如在信贷行业当中，对于每张订单的还款状态，会在每个月月末的时候进行一次统计，因此其周期为月；
- 累积快照事实表。对于一条交易记录，在累积快照事实表中只会有一条记录来记录整个过程，保持更新、直至更新到过程结束。例如在信贷行业当中，对于每张订单，对应有一条记录，标记其订单的状态为还款中、还是已经逾期、或者已经结清，这样的事实表称之为累计快照事实表；


---
### 15. 数据总线矩阵是怎么理解的，为什么要做这个？
- Kimball的理论。


---
### 16. 不同的维度建模方法，各有什么优缺点？
- 范式建模法。
- 维度建模法。
- 实体建模法。


---
### 17. 如何评价一个数据仓库的好坏？
- 从技术角度：高并发、低延迟、负载均衡、备灾容错机制完善、可扩展性、组件通用性、组件丰富性、运维成本等；
- 从数据角度：分层合理性、元数据治理、数据质量、维度划分合理性、ETL鲁棒性、数据耦合程度等；
- 从业务角度：数据支持时效性、准确性、业务告警机制、可回溯性等；


---
### 18. 什么叫缓慢维度变化？
- 指的是，维度并不是静态的，会随着时间发生缓慢的变化；
- 处理缓慢维度变化的方式有三种：直接替代法、更换维度法、新增维度法；
- 直接替代法。删除原来的维度，直接使用变化后的维度，这种方法最简单，但无法记录之前的数据；
- 更换维度法。在维度表中新增一行，对应变化后的维度；在事实表中增加一列，映射到新的维度上。这样同时保留了新旧的维度；
- 新增维度法。在维度表中新增一列，记录了旧维度和新维度的映射关系，这样同时保留了新旧的历史变化情况；


---
### 19. 什么叫查找表，为什么使用替代键？
- 其实目的和上面一样，从基础表到缓慢维度表的过程中的一种实现途径；


---
### 22. 用过什么报表工具(bo，hyperion，congo，reporing servce)，以及该工具基本特点
- Almonds，内部开发工具；
- PowerBI。微软开发的，开源易用；
- FineBI网页版；
- MetaBase，丰富的图例展示模板，可直连各种数据库；
- SuperSet，对接Apache Kylin；


---
### 23. 数据仓库项目最重要或需要注意的是什么，以及如何处理？
- 数据质量，主要是数据源的数据质量分析，数据清洗转换，当然也可以定量分析；
- 数据仓库有两个重要目的，一是数据集成，二是服务BI；
- 数据准确性是数据仓库的基本要求，而效率是BI分析的前提，数据质量、运行效率和可扩展性是数据仓库项目设计、实施高明与否的三大标志；


---
### 24. 什么是数据库的三范式？


---
### 25. 如何实施数据质量管理？
- 数据质量表征的是，使用者对于数据仓库的满意程度。数据质量可以从以下六个维度展开：
    - 准确性：数据是否正确，明细数据和汇总数据是否吻合；
    - 合规性：数据的存储格式是否标准；
    - 完整性：是否存在null值的数据；
    - 及时性：数据加工是否准时准点，不会延迟；
    - 一致性：上下游、不同表的同一类数据，是否会冲突；
    - 重复性：数据是否会重复；
- 数据质量分析的主要任务，是检查数据中是否存在脏数据。脏数据的类型，及处理方式，如下所述：
    - 数据缺失。缺失记录，或某些字段存在null值。其原因，可能是源数据本身就缺失，或是ETL过程中人为导致的缺失。对于缺失的记录，可以从数据源再次抽取；对于存在的null值，既可以不对其纳入统计范围，也可以平均、随机、给定确切值等方式来填补；
    - 数据重复。重复的记录出现多条，需要结合业务逻辑、时间属性，来判断是否应该去重；
    - 数据错误。数据没有严格按照规范来记录，例如人的年龄是200岁。对于异常值，通过区间限定的方式来发现和排除；对于格式的错误，需要从加工脚本、ETL工具等方面来定位错误原因；对于其他问题，可能只能通过人工干预的方式来处理；
    - 数据不可用。这种情况数据数据实际正确，但无法用于数据分析，例如：某用户的地址是“深圳南山滨海大厦”，本身是正确的数据，但是无法定位到区级别的分析，要把南山拆出来才能使用。这种情况下就只能从源头规范好数据的结构，或者通过关键词匹配的方式解决；
- 数据质量，可以从如下几个具体的方面来保证：
    - 构建一套高效、健壮的ETL系统，去保证数据源的数据仓库的一致性、及时性；
    - 配置清晰、合理的任务依赖关系，以及预警机制；


---
### 26. 数据治理有哪些方法？
- 约束输入：你永远想不到用户会输入哪些值，所以别给用户太多发挥的空间，做好约束工作。该用户填写的，系统必须设置为“必填”；值有固定选项的，一定用列表让用户选，别再手工输入；系统在录入提交时就做好检查，格式不对，值不在正常范围内，直接报错的情况必须让用户重新输入；设计录入表单时尽量原子化字段，比如上面说的地址，设计时就分成国家、省、市、区、详细地址等多个字段，避免事后拆分；录入数据保存的数据表也尽量统一，不要产生有大量相同数据的表，造成数据重复隐患。当然，对输入的约束主要是在业务库、开发系统中进行的;
- 规范输出：老板看不同人做的报表，同一个“收益率”指标，每张报表的值都不一样，老板的内心一定是崩溃的，不知该骂谁，只能全骂。排除计算错误的情况，一般都是统计口径不一致造成的。所以要统一语义，做一个公司级别的语义字典（不是数据库的数据字典）。所有给人看的报告上的指标名称，都要在语义字典中备案，语义字典明确定义其统计口径和含义。不同统计口径的指标必须用不同的名词。如果发现一个词已经在语义字典中有了，就必须走流程申请注册一个新词到语义字典。这一步则是在数据仓库中完成的；
- 元数据管理，权限控制等；


---
### 27. 如何处理脏数据？
- 数据缺失。缺失记录，或某些字段存在null值。其原因，可能是源数据本身就缺失，或是ETL过程中人为导致的缺失。对于缺失的记录，可以从数据源再次抽取；对于存在的null值，既可以不对其纳入统计范围，也可以平均、随机、给定确切值等方式来填补；
- 数据重复。重复的记录出现多条，需要结合业务逻辑、时间属性，来判断是否应该去重；
- 数据错误。数据没有严格按照规范来记录，例如人的年龄是200岁。对于异常值，通过区间限定的方式来发现和排除；对于格式的错误，需要从加工脚本、ETL工具等方面来定位错误原因；对于其他问题，可能只能通过人工干预的方式来处理；
- 数据不可用。这种情况数据数据实际正确，但无法用于数据分析，例如：某用户的地址是“深圳南山滨海大厦”，本身是正确的数据，但是无法定位到区级别的分析，要把南山拆出来才能使用。这种情况下就只能从源头规范好数据的结构，或者通过关键词匹配的方式解决；


---
### 28. 面对维度的很多的数据时，需要进行降维处理吗？如何进行降维处理？
- 


---
### 29. 处理的最大数据量是多大？是如何处理的？
- 最大的一张埋点表有600G，台账表的有300G；
- 数据存储在HDFS中，使用Hive作为上层接口，使用Hive-QL作为开发语言，基于MapReduce进行计算；少部分也会用到Spark-sql；


---
### 30. 使用了哪些任务调度系统？
- 在2018年初期，我们的数据仓库是基于SQL Server创建的，那时候的作业不太多，使用的是一套自研的、基于Python和SQL Server作业调度器的脚本，来进行任务调度；但后来随着数据量的增大，任务的增多，已经不能承担的其BI数据支持了；而且自研的调度脚本没有配套权限管理、可视化等功能，急需用新的工具取代；
- 在2018年下半年，开始基于HDFS作为数据仓库的存储介质，使用Hive作为数据仓库的构建工具，使用领英开源的任务调度工具Azkaban来进行任务调度；Azkaban提供了基于Linux的可视化调度，以及较为基本的依赖配置功能，但是Azkaban没有很好的权限分离，只能使用若干个账号进行任务的上线、下线、配置等操作；同时，Azkaban只能将所有的任务提交在集群的一个节点上，无法充分利用分布式的优点，因此，在调度任务的早高峰，有时会出现延迟的不稳定现象；
- 为了解决以上Azakaban的问题，在2019年下半年，开始推进Azkaban的退役，使用EasyScheduler进行任务调度。EasyScheduler是国内易观千帆数据团队开源，目前已经成为了Apache基金会的顶级项目，官方名称叫Apache Dolphin。相比于Azkaban，EasyScheduler提供了更加精细的权限管理，同时，它还可以将多个任务较为均衡的分配在集群中不同的节点上，从而充分利用好了分布式的性能，使得作业流整体上运行的更快速、稳定；此外，EasyScheduler还增加了作业延时告警、可视化配置等功能，整体上使得任务配置更加简单、任务的依赖关系更加清晰，极大的增强了数据仓库的鲁棒性、BI支持的及时性，也是一直沿用至今的调度器；


---
### 31. 多个数据源同步，用到了哪些工具或方法？
- 我们的数据仓库是基于HDFS来建设的，那么对于不同类型的数据源，会采用不同的工具来讲源数据同步到HDFS中；
- 对于业务库MySQL的数据，一般使用Sqoop直拉；
- 对于日志数据，一般使用Flume；
- 对于一些离线的txt/csv文本数据，则可以直接将文件传输到HDFS中存储起来；
- 此外，还可以使用kette、informatic等工具，在RDBMS中传输数据；


---
### 32. 什么是逻辑数据映射？它对ETL项目组的作用是什么？
- 逻辑数据映射本质上是一种说明文档，用于描述源系统的数据定义、目标数据仓库的模型、以及二者之间ETL的方式；
- 该文档一般需要描述如下信息：源系统库名、源系统表名、数据仓库的目标库名、数据仓库的目标表名、目标表类型（事实表、维度表）、ETL方式等；
- 逻辑数据映射应贯穿ETL项目的始终，应该在物理迁移之前就建立好逻辑迁移的映射，从而起到了元数据的作用；


---
### 33. 在数据仓库ETL项目中，数据探索阶段的主要目的是什么？
- 在逻辑数据映射进行之前，需要首先对所有的源系统进行分析。对源系统的分析通常包括两个阶段，一个是数据探索阶段，另一个是异常数据检测阶段；
- 数据探索阶段包括以下内容：
    - 1．收集所有的源系统的文档、数据字典等内容；
    - 2．收集源系统的使用情况，如谁在用、每天多少人用、占多少存储空间等内容；
    - 3．判断出数据的起始来源；
    - 4．通过数据概况来对源系统的数据关系进行分析；
- 数据探索阶段的主要目的是理解源系统的情况，为后续的数据建模和逻辑数据映射打下坚实的基础；


---
### 34. 如何确定起始来源数据？
- 对于来源数据，不同的人对它有不同的定义。Kimball认为，数据来源，指的是最初生成数据的地方；
- 在大企业内，冗余的数据会被保存在多个系统当中；在其ETL的过程中，会使得与原始数据不同；
- 在离来源数据越远、越下游的地方建立数据仓库，遇到垃圾数据、错误数据的风险就越大；


---
### 35. 在ETL过程中四个基本的过程分别是什么？
- 在Kimball的理论中，ETL分为四个阶段：抽取extract、清洗clean、一致性处理comform、交付delivery，简称ECCD；
- 抽取：连接并读取源系统的数据，捕获变化数据，并抽取到数据准备区；
- 清洗：根据业务规则去除脏数据，增补字段及数据项的值，建立元数据系统，并将清洗后的数据保存在数据准备区；
- 一致性处理：按业务规则加工维度表，一致性处理事实表，将一致性处理后的数据保存在数据准备区；
- 交付：加载雪花模型、星型模型，加载缓慢变化维度，并将数据交付到数据仓库；


---
### 36. 简述ETL过程中哪个步骤应该出于安全的考虑将数据写到磁盘上？
- 出于安全及ETL能方便重新开始，在数据准备区（Staging Area）中的每个步骤中都应该将数据写到磁盘上，即生成文本文件或者将建立关系表保存数据，而不应该以数据不落地方式直接进行ETL；
- 例如，在数据抽取阶段，我们需要连接到源系统，为了对源系统的影响尽量小，我们需要将抽取的数据保存成文本文件或者放入数据准备区的表中，这样，当ETL过程出现错误而失败时，我们就可以从这些文本文件开始ETL，而不需要再次影响源系统；


---
### 37. 简述直接连接数据库和使用ODBC连接数据库进行通讯的优缺点？
- 接连接的方式主要是通过COBOL、PL/SQL、Transact-SQL等方式连接数据库。优点是运行性能高，可以使用DBMS提供的一些特殊功能。缺点是通用性差；
- ODBC是为windows应用程序访问数据库提供的一组接口。优点是灵活性，通过改变驱动和连接方式可以使用不同的数据库；缺点是性能差。使用ODBC连接方式实现ETL的话，在ETL程序至少要有两层，分别是ODBC Manager层和ODBC Driver层。另外，使用ODBC方式不能使用DBMS提供的一些特殊的功能；


---
### 38. 简述出三种变化数据捕获技术及其优缺点？
- 采用审计列。例如表中的“添加日期create_time”、“修改日期update_time”等字段。应用程序在对该表的数据进行操作时，同时更新这些字段，或者建立触发器来更新这些字段。这种方式进行变化数据捕获的优点是方便，容易实现。缺点是如果业务库没有相应的审计字段，需要改变已有的表结构，以保证获取过程涉及的每张表都有审计字段；
- 数据库日志。这是一种通过DBMS提供的日志系统来获得变化的方式。它的优点是对数据库影响最小；缺点是要求DBMS支持，并且对日志记录的格式非常了解，并且也有一定的开发成本；
- 全表扫描。全表扫描或者全表导出文件后进行扫描对比也可以进行变化数据捕获，尤其是捕获删除的数据时。这种方法的优点是，思路清晰，适应面广，缺点是效率比较差，因此只适用于偶尔这样操作；


---
### 39. 数据质量检查的四大类是什么？为每类提供一种实现技术？
- 正确性检查（Corret）。检查数据值及其描述是否真实的反映了客观事务，例如地址的描述是否完全；
- 明确性检查（Unambiguous）。检查数据值及其描述是否只有一个意思或者只有一个解释，例如地名相同的两个县需要加区分方法；
- 一致性检查（Consistent）。检查数据值及其描述是否统一的采用固定的约定符号来表示。例如币别中人民币用'CNY'；
- 完全性检查（Complete）。完全性有两个需要检查的地方，一个是检查字段的数据值及其描述是否完全，例如检查是否有空值；另一个是检查记录的统计值是否完全，有没有遗忘某些条件；


---
### 40. 简述应该在ETL的哪个步骤来实现概况分析？
- 数据概况分析是对源数据内容的概况进行分析，应该在项目的开始后尽早完成，它会对设计和实现有很大的影响。在完成需求收集后就应该立即开始数据概况分析；
- 数据概况分析不光是对源系统的数据概况的定量描述，而且为ETL系统中需要建立的错误事件事实表（Error Event Table）和审计维度表（Audit Dimension）打下基础，为其提供数据；


---
### 41. ETL项目中的数据质量部分核心的交付物有那些？
- 数据概况分析结果。数据概况分析结果是对源系统的数据状况的分析产物，包括如源系统中有多少个表，每个表有多少字段，其中多少为空，表间的外键关系是否存在等反映源系统数据质量的内容。这些内容用来决定数据迁移的设计和实现，并提供给错误事件事实表和审计维度表需要的相关数据；
- 错误事件事实表。错误事件事实表及相关的一系列维度表是数据质量检查部分的一个主要交付物。粒度是每一次数据质量检查中的错误信息。相关维度包括日期维度表、迁移信息维度表、错误事件信息维度表，其中错误事件信息维度表中检查的类型、源系统的信息、涉及的表信息、检查使用的SQL等内容。错误事件事实表不提供给前台用户；
- 审计维度表。审计维度表是给最终用户提供数据质量说明的一个维度表。它描述了用户使用的事实表的数据来源，数据质量情况等内容；


---
### 42. 如何来量化数据仓库中的数据质量？
- 对于数据量小的表，通常可以采用分组group by来检查数据是否符合域的定义规则；
- 对于数据量大的表，一般通过采样技术来减少数据量，然后进行不规则数据检测；


---
### 43. 为什么在ETL的过程中需要对日期进行特殊处理？
- 在数据仓库的项目中，数据分析是主导需求，而基于日期和时间的分析更是占了很大的比重。而在业务库当中，日期通常都是SQL的datetime类型的。如果在数据仓库的分析时，使用SQL对这种类型的字段临时处理会出现一些问题，如效率很差，不同的用户会采用不同的格式化方法导致报表不统一。所以，在数据仓库的建模时都会建立日期维度表和时间维度表，将用到的和日期相关的描述都冗余到该表中；
- 但是，并不是所有的日期都能使用日期维度表来关联。日期维度表中的记录是有限的，有些日期如生日等可能会比日期维度表中记录的最小日期还要早，这类字段可以直接在数据仓库中保存为datetime型。而像购买日期等与分析的业务紧密相关的通常都需要转化为日期维度表的外键，可以用日期维度表中统一的描述信息进行分析；


---
### 44. 为什么要使用数据仓库？比起不用数据仓库，有什么提升？


---
### 45. 大公司和小公司，使用数据仓库一般会有什么区别？
- 对于很多数据指标的定义，大公司需要规范
