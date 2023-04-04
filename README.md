![](http://images.jieyu.ai/images/hot/zillionbanner.jpg)

<h1 align="center">大富翁AI量化社区欢迎您!</h1>

<p align="right">
<a href="https://zillionare.readthedocs.io/zh_CN/latest/?badge=latest"><img src="https://readthedocs.org/projects/zillionare/badge/?version=latest"></img></a>
<a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/License-MIT-yellow.svg"></a>
</p>

# 重要声明

一些朋友关注了本项目，也接到一些咨询。关于本项目状态进行一些说明。本项目1.0版于2022年初发布，当前仍然可用，但只提供了以下功能：
1. Omega服务器，进行行情数据同步和保存。行情数据保存在redis中（受限于物理内存容量，建议只存到30分钟），财务数据保存在postgres中
2. Omicron数据SDK。SDK提供了对行情数据的存取，以及证券列表的运算、日期时间的计算等。
3. 基于docker compose的部署

## 2.0的状态
2.0已经开发完毕，在公司内部使用已超过9个月，代码也开源了，但对外发布之前，还有不少工作要做，主要是：
1. 文档补全
2. 安装部署补全
3. 部分维护工作（特别是由于第三方造成的数据维护）还需要进一步自动化和智能化。

预计这个开源发布还要等较长时间，目前我们只为商业合作伙伴提供咨询、培训和部署服务（基于全部或者部分组件）。

## 2.0的功能
1. Omega服务器，盘中提供1分钟行情及实时行情（延时低至5秒）。数据存储使用了InfluxDB（时间序列数据库）和minio。在公司内部，我们已保存了超过30亿条行情数据（按关系型数据库的记录概念算），即从2005年以来的每一分钟数据。
2. Omicron数据SDK。接口基本同1.0版，主要是实现上，由对接redis改为从InfluxDB中取数据
3. backtesting server。提供回测时的交易撮合（使用分钟线，更准确）、账户管理和回测指标计算。
4. trader server。提供一个标准化的交易服务器，并且使用与backtesting server一样的API。 trader server通过各种adaptor来对接券商实盘API。
5. trader client。对接trader server和backtesting server的交易客户端。
6. Alpha research platform。投研平台界面及通用算法因子等。
7. Pluto（暂未开源），内部使用的策略和算法。基于这些算法，我们的一个内部实盘在2021年取得年代156%的最高收益。

**以下内容基于1.0**

Zillionare（大富翁）是一个分布式高速量化交易框架。它基于Python 3.8以上版本构建，使用了异步IO和微服务技术，其目标是：

- 分布式高速量化计算平台，算力可根据需要无缝扩展
- 行情数据本地化存储，低延时同步，轻松支持实时触发分钟级别以上交易信号
- 支持私有化部署，保护您的策略
- 虚拟化部署，更易维护
- 诸多量化因子
- 基于机器学习的交易策略
- 基于深度学习的交易策略
- 高速回测框架

如果您不是从[解语科技](http://www.jieyu.ai)官方网站打开的本文档，请点击链接[解语科技](http://www.jieyu.ai)，跳转到官网阅读本文。我们在网站上提供了更多文档和教程，并且排版更适合阅读。

# **为什么要开发大富翁？**

作者从2019年起开始关注量化交易。在试用了一些商业和开源的产品后，作者发现，还没有一个产品能够以可负担的成本提供低延时、准确的数据，在基于机器学习和深度学习的交易策略方面更是完全没有。

一些产品在数据源组织上做的比较好，数据可靠性高，但是不提供离线存储。如果每一笔数据都要在线获取的话，这样产生了数据请求响应速度和费用问题。

**数据请求响应速度慢**

???+ Info
    我们的策略常常是要对全市场进行扫描，以尽可能地发现交易信号。但是这样会导致大量的数据请求。

    我们以一个均线策略为例，假设该策略用到了年线数据，则在对全市场进行扫描的情况下，一次扫描需要从服务器上取得1百万条数据（按A股全市场4000支标的，每支最大取年线250日计算）。

    要取得这么多数据，网络请求数量和耗时都是惊人的。以某sdk为例，单会话取100万条数据，一般大概需要7-10小时左右。如此一来，就不可能实现任何实时或者准实时的交易策略。即使在一些非实时的场合，比如初次安装完成后的数据库初始化，某开源项目的初始化等待时间以天计（当然支持断点），这也似乎是难以接受的。

**费用**

???+ Info

    所有数据都从服务器获取的话，也给服务器带来较大的负荷，因此服务不可能免费提供。

    比如聚宽是一个非常优秀的数据提供者，使用者可以申请一年期的免费试用权限，试用期间，每天提供100万笔（次）数据。尽管这个方案已经十分慷慨了，但不要说对于实际生产环境，就是自行进行研究也是捉襟见肘。作者自己也常常遇到一轮单元测试没跑完，Quota就不够的情况。

    解决上述问题的根本，是应该将已收盘的k线数据，在本地存储起来，然后实时同步本地与上游服务器之间的差分数据。

    一些产品提供了类似的数据本地化存储方案，但也有一些限制和缺陷。

    比如一些产品把获取数据的API分为离线和在线两类。使用离线API可以从本地存储中读取数据，但数据不是实时的，无法用于指导实时交易；要得到实时数据仍然必须使用在线API，要么一次性获取全部需要的数据（这就抛开了本地存储的优势），要么通过在线API获取差分数据，自己再读取数据库中的离线数据，自行合并。实际上产品应该能够透明地提供用户需要的数据，把这些技术细节隐藏在内部，这样使用者可以将更多精力放在策略开发上。

**技术债务**

???+ Info

    从技术上看，Python社区一直在进步，一些产品起步早，架构锁定在了一些陈旧的技术栈上，产生了技术债务。

    量化交易框架的使用者往往都要进行二次开发。在二次开发过程中，也无形中将自己的技术栈锁定在了这些老旧的技术上。这对二次开发者来说也是不公平的。

    如一些产品无论是读本地数据（从数据库中读）还是读远程数据，都在使用同步IO，这对于使用Python作为开发语言的应用来说，这是严重的性能设计缺陷。Python能利用的为数不多的计算资源，往往被消耗在IO等待上了。

**品控**

???+ Info

    一些商业项目的品控无法评估，但如果开源项目未使用CI，没有测试覆盖率报告，尽管开源了，也很难评估其品控。

因此，作者决定自行开发一个框架，致力于解决上述几个方面的问题，并且在交易策略上引入机器学习和深度学习。

当然，由于个人技术能力和眼界所限，大富翁也肯定存在各种不足。是否达到了作者的预期，也需要用户的评价。

# **大富翁有哪些技术特点？**

**高性能**
    为了确保大富翁的高性能，作者从架构、组件选型到算法和数据结构做了较深入的思考，并从产品定位角度上做了取舍。

???+ Info    
    大富翁架构在多进程、分布式微服务基础之上，使用了消息队列、负载均衡、缓存（内存）数据库等技术。在行情同步（实时和非实时）时，都使用了多进程协作模式，极大地提高了网络并发量。在进程协作方面，既有负载均衡模式（通过nginx)，也有并行模式(内置消息机制支持）。
    
    在网络组件方面尽可能使用支持异步IO的第三方组件。比如访问Redis数据库时，使用了aioredis；访问数据库时，使用了asyncpg。
    
    在对主要的数据种类--行情数据的深入研究后，根据数据的分布和处理加工特点，决定使用Redis来持久化保存行情数据；使用Numpy的结构化数组来作为行情数据的内存表示--其它框架一般使用的是Pandas。关于为何这样的选择，请参考[[TODO]]。

    另外，我们还从运营的角度不断进行优化。比如初次安装后的数据导入，由于我们使用了CDN加速和多进程模式，在大富翁中导入一年A股的数据（含30分钟及以上K线），只需要170 ~ 240秒之间（取决于你的CPU核心数和网络带宽）。如果CPU核数足够多，则导入几年的数据，也大概在15~20分钟以内（实测中导入70个月的数据，也只用了776秒。共使用了32个进程）。这些数据今后还会持续优化，以使得它们在较低配置的电脑上也可以取得较好的速度。关于大富翁的速度，对比使用过其它产品的用户可能有较深的感触。

**易用性和品控** 
    在1.0发布之前，大富翁早期最重要的两个组件都经过了多次重写，以确保功能得到恰当解耦和聚合，以保证API的稳定性和易读性。
???+ Info

    如果您有机会深入研究一下大富翁的开发工具链，您会发现品控发生在从代码提交、单元测试到持续集成的各个环。

    为促进大富翁开源社区的活跃，作者发布了一系列文章来介绍Python开发的工具链，请参考[[TODO]]

    在部署方面，我们支持虚拟化部署和pip安装两种模式。虚拟化部署一键全自动，pip安装模式则在安装之后，提供了一个安装向导来引导用户进行配置，整个过程都比较简单。

**与社区最新技术保持同步**

???+ Info
    最初Zillionare使用了cookie-cutter的pyproject模板来搭建开发框架。这个模板使用了makefile, pip和requirements文件来管理依赖和构建工程，使用twine来发布；使用tox, pytest等几种lint tools等工具来进行品控，使用sphinx来构建文档。
    
    在依赖管理上，我们发现社区正在往新的标准和工具上迁移。比如完全遵循PEP517的标准的Poetry已经成为了当前社区选择的依赖、构建及版本管理工具。因此，Zillionare在去年11月完全换掉了构建框架。
    
    文档工具也替换成了mkdocs,以反映markdown格式更受作者、读者喜欢的现实。

    此外，我们还加入了Travis CI和Codecov来进行持续集成。使用了pre-commit-hooks来保证签入代码的质量。在1.0发布之前，这个测试覆盖率已经达到了93%。

    今后大富翁将继续坚持这样的技术定位，同时在项目发布上力求更加规范。我们会通过RFC的方式来确定项目的方向，约定API接口，并技术通过测试覆盖来保证接口的可用性。


**人工智能导向** 

???+ Info

    尽管大富翁的人工智能功能目前还在实现中，但我们的各项设计充分反映了人工智能优先的导向。比如我们设计中更优先动量策略，对文本和财务数据则关注度不够。原因是，目前人工智能在文本分析上还不是特别有把握，同时依靠消息炒股，确定性也不高。财务数据也有类似的问题。目前人工智能表现最好的，就是在动量策略这一块，所以这也是大富翁的重点。

    对于量化因子，我们不象传统的金融工程那样，把它看成能通过简单组合就能下金蛋的母鸡，而是把更多地把它当成机器学习和深度学习的数据预处理和特征提取工作。这也反映了我们在设计上的人工智能导向。我想这也是一个正确的导向。

**完善和详尽的帮助文档**

???+ Info
    大富翁提供了丰富的文档，包括新手指南，教程和详尽的API文档。

    除了这两个特点以外，作者还力求在文档中既讲是什么，更讲为什么，多讲一些技术决定背后的思考，这样读者会更容易理解和变通。

# **大富翁适合哪些用户？**

1. 大富翁的定位是为A股市场提供量化交易工具和策略，重点是动量类策略。大富翁目前没有接入期货和数字货币的考虑。
2. 高频套利交易也不是大富翁的关注领域--这个领域更多地的是拼硬件和网速，或者从根本上说，是拼资本，算法和策略可能在这个领域不占主导地位。大富翁定位于服务复杂的A股市场，为A股市场带来开源的人工智能技术。大富翁的架构设计为处理分钟级别以上的行情数据。我们认为，对Tick级别的数据进行深度学习固然有其意义，但这一领域应该还是套利交易为主。
3. 大富翁提供的历史行情数据从2015年开始，目前包括了所有的股票30分钟及以上的行情数据。
4. 量化交易框架的用户往往都有自己进行二次开发的需求。大富翁的技术选型的先进性和规范性，保证了用户的投入在未来较长的时间内都会有效。
5. 从技术栈角度来说，大富翁使用纯Python开发。操作系统上限定为Ubuntu > 18。如果对这种操作系统不太熟悉的用户，可以通过我们提供的容器化部署方式来部署。

相对于其它产品而言，大富翁的功能面收得比较紧，我们更希望保持专注，集中精力把事情做好。

# **如何开始大富翁财富之旅？**
想立即开始？您可以从[新手入门](zillionare/installation.md)开始，这个教程将带领您安装大富翁，完成设置和初始化任务，并且在docker容器里提供了可执行的教程(jupyter notebook)。

[大富翁量化教程](zillionare/tutorial/preface)将带领您学习大富翁提供的各种功能，以及如何编写策略。在教程中，作为示例，我们也会分享自己的一些策略，从量化因子到人工智能的都有。

[大富翁量化框架深度解析](zillionare/diveintozillionare/index.md)从大富翁开发者的角度，介绍了大富翁开发过程中的技术选型思考、各种技术栈包括构建工具链如何使用。这也是一本面向工程实践的Python学习手册。

鸣谢
=========

Zillionare采用以下技术构建:
- ![JetBrains Black Box Logo logo](https://resources.jetbrains.com/storage/products/company/brand/logos/jb_square.svg) [Pycharm开源项目支持计划](https://www.jetbrains.com/?from=zillionare-omega)

