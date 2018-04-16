---
title: 使用 Azure 流分析进行高频交易模拟
description: 如何在 Azure 流分析作业中进行线性回归模型训练和评分。
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: get-started-article
ms.date: 11/05/2017
ms.openlocfilehash: cf04cf92f204b89c0641a23ba38b05dbcad409b2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>使用流分析进行高频交易模拟
用户可以在 Azure 流分析中结合使用 SQL 语言和 JavaScript 的用户定义函数 (UDF) 与用户定义聚合 (UDA) 进行高级分析。 高级分析可能包括在线机器学习训练和评分，以及有状态过程模拟。 本文介绍如何在 Azure 流分析作业中执行线性回归操作，该作业在高频交易方案中进行持续的训练和评分。

## <a name="high-frequency-trading"></a>高频交易
高频交易的逻辑流是关于：
1. 从证券交易所获取实时报价。
2. 围绕报价构建一个预测模型，以便预测价格波动。
3. 如果能够成功预测价格波动，则可通过买入或卖出获益。 

因此，我们需要：
* 实时报价源。
* 一个可以针对实时报价进行操作的预测模型。
* 一种交易模拟，演示交易算法的损益。

### <a name="real-time-quote-feed"></a>实时报价源
IEX 通过 socket.io 提供免费的[实时买入和卖出报价](https://iextrading.com/developer/docs/#websockets)。 可以编写简单的控制台程序来接收实时报价，并将其作为数据源推送到 Azure 事件中心。 以下代码是程序的主干。 为简便起见，代码省略了错误处理。 还需在项目中包括 SocketIoClientDotNet 和 WindowsAzure.ServiceBus NuGet 包。


    using Quobject.SocketIoClientDotNet.Client;
    using Microsoft.ServiceBus.Messaging;

    var symbols = "msft,fb,amzn,goog";
    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
    var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");

    socket.On(Socket.EVENT_MESSAGE, (message) =>
    {
        eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
    });

    socket.On(Socket.EVENT_CONNECT, () =>
    {
        socket.Emit("subscribe", symbols);
    });

下面是一些生成的示例事件：

    {"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}

>[!NOTE]
>事件的时间戳为 **lastUpdated**，采用纪元时间。

### <a name="predictive-model-for-high-frequency-trading"></a>高频交易预测模型
我们使用 Darryl Shen 在[其文章](http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf)中介绍的线性模型进行演示。

大额委托失衡 (VOI) 是基于当前买入/卖出价量和上次买入/卖出价量的一个函数。 该文章明确了 VOI 和未来价格波动的相关性。 它构建了一个线性模型，该模型基于过去 5 个 VOI 值以及接下来 10 次交易的价格变化。 使用前一天的数据对模型进行线性回归训练。 

然后，使用训练好的模型对当前交易日的报价进行实时价格变化预测。 如果预测到足够大的价格变化，则进行交易。 预计单个股票在一个交易日中可以发生成千上万的交易，具体取决于阈值设置。

![VOI 定义](./media/stream-analytics-high-frequency-trading/voi-formula.png)

现在，让我们在 Azure 流分析作业中表述训练和预测操作。

首先，清理输入。 通过 **DATEADD** 将纪元时间转换为日期时间。 使用 **TRY_CAST** 在不造成查询故障的情况下强制转换数据类型。 最好是将输入字段转换为预期的数据类型，这样就不会在操作或比较字段时出现意外行为。

    WITH
    typeconvertedquotes AS (
        /* convert all input fields to proper types */
        SELECT
            System.Timestamp AS lastUpdated,
            symbol,
            DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
            TRY_CAST(bidSize as bigint) AS bidSize,
            TRY_CAST(bidPrice as float) AS bidPrice,
            TRY_CAST(askSize as bigint) AS askSize,
            TRY_CAST(askPrice as float) AS askPrice,
            TRY_CAST(volume as bigint) AS volume,
            TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
            TRY_CAST(lastSalePrice as float) AS lastSalePrice
        FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
    ),
    timefilteredquotes AS (
        /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
        /* clean up invalid data points */
        SELECT * FROM typeconvertedquotes
        WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
    ),

接下来，使用 **LAG** 函数获取上次交易的值。 任意选择一小时的 **LIMIT DURATION** 值。 提供报价频率以后，即可假定能够找到上一次交易（回溯一小时）。  

    shiftedquotes AS (
        /* get previous bid/ask price and size in order to calculate VOI */
        SELECT
            symbol,
            (bidPrice + askPrice)/2 AS midPrice,
            bidPrice,
            bidSize,
            askPrice,
            askSize,
            LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
            LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
            LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
            LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
        FROM timefilteredquotes
    ),

然后可以计算 VOI 值。 我们会筛选掉 null 值，以防出现上次交易不存在的情况。

    currentPriceAndVOI AS (
        /* calculate VOI */
        SELECT
            symbol,
            midPrice,
            (CASE WHEN (bidPrice < bidPricePrev) THEN 0
                ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
             END) -
            (CASE WHEN (askPrice < askPricePrev) THEN askSize
                ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
             END) AS VOI
        FROM shiftedquotes
        WHERE
            bidPrice IS NOT NULL AND
            bidSize IS NOT NULL AND
            askPrice IS NOT NULL AND
            askSize IS NOT NULL AND
            bidPricePrev IS NOT NULL AND
            bidSizePrev IS NOT NULL AND
            askPricePrev IS NOT NULL AND
            askSizePrev IS NOT NULL
    ),

现在，我们再次通过 **LAG** 创建一个序列，使用 2 个连续的 VOI 值，后跟 10 个连续的中间价格值。

    shiftedPriceAndShiftedVOI AS (
        /* get 10 future prices and 2 previous VOIs */
        SELECT
            symbol,
            midPrice AS midPrice10,
            LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
            LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
            LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
            LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
            LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
            LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
            LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
            LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
            LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
            LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
            LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
            LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),

然后将数据重塑，使之成为一个双变量线性模型的输入。 再次筛选掉我们没有所有数据的事件。

    modelInput AS (
        /* create feature vector, x being VOI, y being delta price */
        SELECT
            symbol,
            (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
            VOI1 AS x1,
            VOI2 AS x2
        FROM shiftedPriceAndShiftedVOI
        WHERE
            midPrice1 IS NOT NULL AND
            midPrice2 IS NOT NULL AND
            midPrice3 IS NOT NULL AND
            midPrice4 IS NOT NULL AND
            midPrice5 IS NOT NULL AND
            midPrice6 IS NOT NULL AND
            midPrice7 IS NOT NULL AND
            midPrice8 IS NOT NULL AND
            midPrice9 IS NOT NULL AND
            midPrice10 IS NOT NULL AND
            midPrice IS NOT NULL AND
            VOI1 IS NOT NULL AND
            VOI2 IS NOT NULL
    ),

由于 Azure 流分析没有内置的线性回归函数，因此我们使用 **SUM** 和 **AVG** 聚合来计算线性模型的系数。

![线性回归公式](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

    modelagg AS (
        /* get aggregates for linear regression calculation,
         http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
        SELECT
            symbol,
            SUM(x1 * x1) AS x1x1,
            SUM(x2 * x2) AS x2x2,
            SUM(x1 * y) AS x1y,
            SUM(x2 * y) AS x2y,
            SUM(x1 * x2) AS x1x2,
            AVG(y) AS avgy,
            AVG(x1) AS avgx1,
            AVG(x2) AS avgx2
        FROM modelInput
        GROUP BY symbol, TumblingWindow(hour, 24, -4)
    ),
    modelparambs AS (
        /* calculate b1 and b2 for the linear model */
        SELECT
            symbol,
            (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
            (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
            avgy,
            avgx1,
            avgx2
        FROM modelagg
    ),
    model AS (
        /* calculate a for the linear model */
        SELECT
            symbol,
            avgy - b1 * avgx1 - b2 * avgx2 AS a,
            b1,
            b2
        FROM modelparambs
    ),

需将报价与模型联接起来，然后才能使用前一天的模型对当前事件评分。 我们使用 **UNION** 而非 **JOIN** 来合并模型事件和报价事件， 然后使用 **LAG** 将事件与前一天的模型配对，以便刚好获得一个匹配。 由于存在周末，必须回溯三天。 如果使用了简单的 **JOIN**，则每个报价事件会获得三个模型。

    shiftedVOI AS (
        /* get two consecutive VOIs */
        SELECT
            symbol,
            midPrice,
            VOI AS VOI1,        
            LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
    VOIAndModel AS (
        /* combine VOIs and models */
        SELECT
            'voi' AS type,
            symbol,
            midPrice,
            VOI1,
            VOI2,
            0.0 AS a,
            0.0 AS b1,
            0.0 AS b2
        FROM shiftedVOI
        UNION
        SELECT
            'model' AS type,
            symbol,
            0.0 AS midPrice,
            0 AS VOI1,
            0 AS VOI2,
            a,
            b1,
            b2
        FROM model
    ),
    VOIANDModelJoined AS (
        /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
        SELECT
            symbol,
            midPrice,
            VOI1 as x1,
            VOI2 as x2,
            LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
            LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
            LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
        FROM VOIAndModel
        WHERE type = 'voi'
    ),

现在，我们可以在阈值为 0.02 的情况下，根据模型进行预测并生成买入/卖出信号。 交易值为 10 表示买入。 交易值为 -10 表示卖出。

    prediction AS (
        /* make prediction if there is a model */
        SELECT
            symbol,
            midPrice,
            a + b1 * x1 + b2 * x2 AS efpc
        FROM VOIANDModelJoined
        WHERE
            a IS NOT NULL AND
            b1 IS NOT NULL AND
            b2 IS NOT NULL AND
            x1 IS NOT NULL AND
            x2 IS NOT NULL
    ),
    tradeSignal AS (
        /* generate buy/sell signals */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,     
            midPrice,
            efpc,
            CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
            DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
        FROM prediction
    ),

### <a name="trading-simulation"></a>交易模拟
有了交易信号以后，需在不进行实际交易的情况下测试交易策略的有效性。 

为了进行此测试，可以使用带跳跃窗口的 UDA，每分钟跳跃一次。 此外还使用按日期分组功能和 having 子句，使得该窗口只计同一天的事件。 对于时间跨度为两天的跳跃窗口，可以对日期执行 **GROUP BY** 操作，将日期分成前一天和当天。 **HAVING** 子句筛选掉在当天结束的窗口，而对前一天结束的窗口进行分组。

    simulation AS
    (
        /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,
            date,
            uda.TradeSimulation(tradeSignal) AS s
        FROM tradeSignal
        GROUP BY HoppingWindow(minute, 420, 1), symbol, date
        Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
    )

JavaScript UDA 在 `init` 函数中初始化所有累加器，在计算状态转换时将每个事件添加到窗口，在窗口结束时返回模拟结果。 一般交易过程是：

- 如果在不持股的情况下收到买入信号，则买入股票。
- 如果在持股的情况下收到卖出信号，则卖出股票。
- 如果没有持股，则表明已空仓。 

如果在空仓情况下收到买入信号，则通过买入来补仓。 在此模拟中，我们从来没有持有或空仓 10 股股票。 交易费始终为 8 美元。


    function main() {
        var TRADE_COST = 8.0;
        var SHARES = 10;

        this.init = function () {
            this.own = false;
            this.pos = 0;
            this.pnl = 0.0;
            this.tradeCosts = 0.0;
            this.buyPrice = 0.0;
            this.sellPrice = 0.0;
            this.buySize = 0;
            this.sellSize = 0;
            this.buyTotal = 0.0;
            this.sellTotal = 0.0;
        }

        this.accumulate = function (tradeSignal, timestamp) {

            if(!this.own && tradeSignal.trade == 10) {
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
            } else if(!this.own && tradeSignal.trade == -10) {
              // Sell to open
              this.own = true;
              this.pos = -1
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
              // Sell to close
              this.own = false;
              this.pos = 0;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;

              // Sell to open
              this.own = true;
              this.pos = -1;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;        
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
              // Buy to close
              this.own = false;
              this.pos = 0;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;

              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;         
              this.buyTotal += SHARES * tradeSignal.midprice;
            }
        }

        this.computeResult = function () {
            var result = {
                "pnl": this.pnl,
                "buySize": this.buySize,
                "sellSize": this.sellSize,
                "buyTotal": this.buyTotal,
                "sellTotal": this.sellTotal,
                "tradeCost": this.tradeCost
                };
            return result;
        }
    }

最后，我们将结果输出到 Power BI 仪表板进行可视化。

    SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
    SELECT
        symbol,
        time,
        date,
        TRY_CAST(s.pnl as float) AS pnl,
        TRY_CAST(s.buySize as bigint) AS buySize,
        TRY_CAST(s.sellSize as bigint) AS sellSize,
        TRY_CAST(s.buyTotal as float) AS buyTotal,
        TRY_CAST(s.sellTotal as float) AS sellTotal
        INTO pnlDashboard
    FROM simulation /* output trade simulation to PBI */

![交易](./media/stream-analytics-high-frequency-trading/trades.png)

![PNL](./media/stream-analytics-high-frequency-trading/pnl.png)


## <a name="summary"></a>摘要
可以在 Azure 流分析中使用中等复杂程度的查询来实现逼真的高频交易模型。 由于缺少内置的线性回归函数，我们必须将模型从五个输入变量简化为两个。 但如果用户有决心，也可以 JavaScript UDA 方式实现更高维且更复杂的算法。 

需要指出的是，除 JavaScript UDA 之外的大部分查询可以在 Visual Studio 中通过[用于 Visual Studio 的 Azure 流分析工具](stream-analytics-tools-for-visual-studio.md)进行测试和调试。 在编写初始查询以后，作者在 Visual Studio 中花不到 30 分钟的时间对查询进行了测试和调试。 

目前，UDA 不能在 Visual Studio 中调试。 我们正在努力实现该功能，希望能够对 JavaScript 代码进行单步调试。 另请注意，进入 UDA 的字段的名称为小写。 在查询测试过程中，这并没有引起注意。 但在兼容性级别为 1.1 的 Azure 流分析中，我们保留字段名称的大小写，这样更显自然。

我希望本文能够激发所有 Azure 流分析用户的热情，促使他们使用我们的服务在近实时环境中持续进行高级分析。 如果你有任何反馈，请告知我们，以便我们改进高级分析方案的查询实现。
