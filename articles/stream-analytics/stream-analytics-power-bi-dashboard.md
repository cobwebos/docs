---
title: "流分析的上的 Power BI 仪表板 | Microsoft 文档"
description: "使用实时流式处理 Power BI 仪表板来采集商业智能信息，并分析流分析作业中的大量数据。"
keywords: "分析仪表板, 实时仪表板"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>流分析和 Power BI：针对流式数据的实时分析仪表板
Azure 流分析允许你利用领先的商业智能工具 Microsoft Power BI。 了解如何使用 Azure 流分析来分析大量流式数据，以便在实时 Power BI 分析仪表板中获得相关见解。

使用 [Microsoft Power BI](https://powerbi.com/) 快速构建实时仪表板。 [观看演示本方案的视频](https://www.youtube.com/watch?v=SGUpT-a99MA)。

在本文中，你将了解如何使用 Power BI 作为 Azure 流分析作业的输出，以便创建你自己的自定义商业智能工具，同时充分利用实时仪表板。

## <a name="prerequisites"></a>先决条件
* Microsoft Azure 帐户
* 用于 Power BI 的工作或学校帐户
* 完成入门方案[实时欺诈检测](stream-analytics-real-time-fraud-detection.md)。 本文基于该工作流，并添加了一个 Power BI 流数据集输出。

## <a name="add-power-bi-output"></a>添加 Power BI 输出
为作业指定输入后，可以定义 Power BI 的输出。 选中“输出”作业仪表板中间的框。 然后单击我们所熟悉的“+添加”按钮并创建输出。

![添加输出](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

提供“输出别名”– 可以输入任何便于引用的输出别名。 如果决定为你的作业设置多个输出，则此输出别名特别有用。 在这种情况下，你必须在查询中引用此输出。 例如，使用输出别名值“StreamAnalyticsRealTimeFraudPBI”。

然后单击“授权”按钮。

![添加授权](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

这会出现一个要求提供 Azure 凭据（工作或学校帐户）的提示窗口。然后，你的 Azure 作业便可访问 Power BI 区域。

![授权字段](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

满足要求后，授权窗口将会消失，“新输出区域”中将包含“数据集名称”和“表名称”的字段。

![pbi 工作区](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

按如下所述定义这些值：
* **组工作区** – 在 Power BI 租户中选择一个可在其下创建数据集的工作区。
* **数据集名称** - 提供希望 Power BI 输出使用的数据集名称。 例如，使用“StreamAnalyticsRealTimeFraudPBI”。
* **表名** - 在 Power BI 输出数据集下提供表名。 我们将它命名为“StreamAnalyticsRealTimeFraudPBI”。 目前，流分析作业的 Power BI 输出只能在数据集中设置一个表。

单击“创建”，输出配置现已完成。

> [!WARNING]
> 请注意，如果 Power BI 已包含与在此流分析作业中提供的数据集和表同名的数据集和表，现有数据将被覆盖！
> 另外，不应在 Power BI 帐户中显式创建此数据集和表。 在启动流分析作业，并且该作业开始向 Power BI 发送输出时，这些文件将自动创建。 如果作业查询没有返回任何结果，数据集和表将无法创建。
> 
> 

数据集是使用以下设置创建的；
* defaultRetentionPolicy: BasicFIFO - 数据为 FIFO，最多 20 万行
* defaultMode: pushStreaming：支持流磁贴和基于报表的传统视觉对象（即推送）
* 目前不支持创建包含其他标志的数据集

有关 Power BI 数据集的详细信息，请参阅 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) 参考。


## <a name="write-query"></a>编写查询
转到作业的“查询”选项卡。 编写查询，你希望其输出位于 Power BI 中。 例如，可以使用类似于下面的 SQL 查询来捕获电信行业中的 SIM 欺诈行为：


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>在 Power BI 中创建仪表板

转到 [Powerbi.com](https://powerbi.com)，使用工作或学校帐户登录。 如果流分析作业查询输出了结果，你会看到你的数据集已经创建：

![流数据集](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

单击“添加”磁贴并选择自定义流数据。

![自定义流数据集](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

然后从列表中选择你的数据集：

![你的流数据集](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

现在创建可视化效果卡并选择字段 fraudulentcalls。

![添加欺诈行为](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

现已创建一个欺诈计数器！

![欺诈计数器](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

再次演练添加磁贴，但这一次请选择折线图。 添加 fraudulentcalls 作为值，添加 windowend 作为轴。 我选择过去 10 分钟：

![欺诈呼叫](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


请注意，本教程演示了如何为数据集创建一种类型的图表。 Power BI 可帮助你为组织创建其他客户商业智能工具。 如需 Power BI 仪表板的其他示例，请观看 [Power BI 入门](https://youtu.be/L-Z_6P56aas?t=1m58s)视频。

如需进一步了解如何配置 Power BI 输出以及如何利用 Power BI 组，请参阅[了解流分析输出](stream-analytics-define-outputs.md "了解流分析输出")的 [Power BI部分](stream-analytics-define-outputs.md#power-bi)。 若要详细了解如何通过 Power BI 来创建仪表板，请参阅另一有用的资源：[Power BI 中的仪表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)。

## <a name="limitations-and-best-practices"></a>限制和最佳实践
Power BI 同时部署并行性和吞吐量约束，如下所述：[https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI 定价")

目前，大约每秒可调用 Power BI 一次。 流视觉对象支持大小 15kb 大小的数据包。 超过该大小的流视觉对象将会失败（但推送将继续工作）。

由于这些因素，Power BI 最适合可通过 Azure 流分析来大幅减少数据加载的案例。
我们建议你使用 TumblingWindow 或 HoppingWindow 来确保数据推送速率最多为 1 次推送/秒，并且你的查询满足吞吐量要求 – 你可以使用以下公式来计算提供你的窗口所需的值（以秒为单位）：

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

例如，如果你每秒有 1,000 个设备在发送数据，并且你使用的 Power BI Pro SKU 支持 1,000,000 行/小时，而你需要获取每个设备在 Power BI 上的平均数据，则每个设备最多可以每 4 秒进行一次推送（如下所示）：  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

这意味着，我们需要将原始查询更改为：

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>续订授权
如果自作业创建后或上次身份验证后更改了密码，需要重新对 Power BI 帐户进行身份验证。 如果在 Azure Active Directory (AAD) 租户上配置了 Multi-Factor Authentication (MFA)，还需要每 2 周续订一次 Power BI 授权。 此问题的症状是没有作业输出，并且操作日志存在“验证用户错误”。

同样，如果作业尝试在令牌已过期时启动，将发生错误并且作业启动将失败。 要解决此问题，请停止正在运行的作业并转到你的 Power BI 输出。  单击“续订授权”链接，并在“上次停止时间”重新启动你的工作以避免数据丢失。 使用 Power BI 刷新授权后，授权区域中会出现一条绿色警报，指明问题已解决。

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


