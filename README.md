
大富翁 (Zillionare)是开源量化框架，提供数据本地化、回测、交易接入和量化分析底层库支持等一站式服务。<br><br>大富翁的起名有两重寓意，一是希望她的使用者们都能实现财富自由。另一方面，大富翁也是一款投资游戏的名字 -- 财富终究只是一场大富翁游戏，以示提醒大家，不要忽视运气的因素。<br><br>在投资中的运气，其实就是周期。千万不要做逆周期的投资。<br><br>Zillionare 最新版本是2.0，提供了海量数据存储（在我们的生产环境下，存储超过30亿条记录）和高性能访问能力。Zillionare是开源框架，您可以自行研究、拓展该框架。我们也提供付费服务。比如，2.0的Docker-compse 安装版本我们目前只对学员提供。<br><br>关于Zillionare的更多细节请访问[链接](articles/products/)

## 最新文章

!!! info "存了50TB！pyarrow + parquet"
    <br>在上一篇笔记中，我们指出，如果我们只在日线级别上存储行情数据和因子，HDF5 无论如何都是够用了。即使是在存储了 40 年分钟线的单个股数据集上，查询时间也只花了 0.2 秒 -- 这个速度已经足够快了，如果我们不需要在分钟级别上进行横截面数据查询的话。<br><br><text-right>发表于 2024-01-03 [阅读](./docs/blog/posts/quantlib/pyarrow-plus-parquet.md)</text-right>

!!! info "200倍速！基于 HDF5 的证券数据存储"
    去年 15 日的笔记挖了个坑，给出了量化数据和因子的存储方案技术导图。这一篇笔记就开始填坑。即使我们购买了在线数据服务，比如 tushare, 聚宽的账号，我们仍然要构建自己的本地存储，为什么？<br><text-right>发表于 2024-01-02 [阅读](./docs/blog/posts/quantlib/hdf5.md)</text-right>

!!! info "年终特稿：这个指标我愿称之为年度最强发现"
    如果说在多因子时代，我们可以仅凭一个因子就构建出策略，并且还很有可能跑赢市场的话，这个因子就是不二之选。<br><text-right>发表于 2023-12-29 [阅读](./docs/blog/posts/strategy/connors-rsi.md)</text-right>

!!! info "羊群效应及其因子化"
    在之前的笔记中，我们多次将现代金融理论与A股中流行的股谚、规律和大V的经验之谈结合起来，我们戏称为现代金融理论的中国化。本篇笔记将继续沿着这一思路展开，介绍羊群效应，以及在A股中，它有哪些表现，如何实现因子化，等等。今天我们要介绍的股谚，是<red>养家心法</red>里中的一条，<red>得散户者得天下</red>。它实际上讲的是要充分利用羊群效应。<br><text-right>发表于 2023-12-28 [阅读](./docs/blog/posts/strategy/herd-behaviour.md)</text-right>

!!! info "xtquant 中的板块数据"
    !!! tip 笔记要点1. xtquant 中有哪些板块和板块分类？<br>    1. 如何获取板块的成份股？<br>    2. 如何获取指数的行情数据？<br><text-right>发表于 2023-12-27 [阅读](./docs/blog/posts/quantlib/qmt-get-sector.md)</text-right>

!!! info "Sharpe 5.5!遗憾规避因子"
    如果在你买入之后，股价下跌，你会在第二天急着抛吗？反之，如果在你卖出之后，股价上涨，你会反手追入吗？先别急着回答，我们来看看科学研究的结论是怎样的。关于这类问题，都是行为金融学研究的范畴。具体到这个场景，我们可以运用遗憾理论来解释。遗憾理论，又称遗憾规避理论（ Fear of Regret Theory），是行为金融学的重要理论之一，该理论认为，非理性的投资者在做决策时，会倾向于避免产生后悔情绪并追求自豪感，避免承认之前的决策失误。<br><text-right>发表于 2023-12-26 [阅读](./docs/blog/posts/strategy/regret.md)</text-right>

!!! info "Santa Claus Rally"
    <!--  --><br>每天坚持发贴 。千字左右，图文并茂。声明：这一天我们也没有漏发。<br>---Santa Claus Rally 是指 12 月 25 日圣诞节前后股市的持续上涨这样一个现象。《股票交易员年鉴》的创始人 Yale Hirsch 于 1972 年创造了这个定义，他将当年最后五个交易日和次年前两个交易日的时间范围定义为反弹日期。<br><text-right>发表于 2023-12-25 [阅读](./docs/blog/posts/strategy/santa-clause.md)</text-right>

!!! info "净新高占比因子"
    个股的顶底强弱比较难以把握，它们的偶然性太强。董事长有可能跑路，个股也可能遇到突发利好（比如竞争对手仓库失火）。在个股的顶底处，**情绪占据主导地位，理性退避次席，技术指标出现钝化**，进入<red>现状不可描述，一切皆有可能</red>的状态。但是，行业指数作为多个随机变量的叠加，就会出现一定的规律性（受A4系统性影响的偶然性我们先排除在外，毕竟也不是天天有A4）。这是因子分析和技术分析可以一展身手的地方。<br><text-right>发表于 2023-12-24 [阅读](./docs/blog/posts/strategy/nh-nl.md)</text-right>

!!! info "球队和硬币因子"
    球队和硬币因子最初来自于耶鲁大学 Tobias Moskowitz 的发表于 2021 年 9 月的一篇论文，发布以来，得到了超过 14 次以上的引用。这篇论文名为《Asset Pricing and Sports Betting》。<br><text-right>发表于 2023-12-23 [阅读](./docs/blog/posts/strategy/hockey-and-coin.md)</text-right>

!!! info "QMT/XtQuant 之开发环境篇"
    !!! tip 笔记要点<br>    1. XtQuant 获取及安装<br>    2. XtQuant 工作原理 （图2）<br>    3. 版本和文档一致性问题 （图3）<br>    4. 使用 VsCode 远程开发<br><text-right>发表于 2023-12-22 [阅读](./docs/blog/posts/quantlib/work-with-xtquant.md)</text-right>





