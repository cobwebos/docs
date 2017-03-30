---
title: "Azure 流分析中的 Power BI 仪表板 | Microsoft 文档"
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
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3dbc49d35def6d7b12ade529d1dd1156dee9d75b
ms.openlocfilehash: 09c54f8cce119c1cbe6a08e969236612447d9e17
ms.lasthandoff: 02/27/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>流分析和 Power BI：针对流数据的实时分析仪表板
在 Azure 流分析中，可以利用领先的商业智能工具 Microsoft Power BI。 了解如何使用 Azure 流分析来分析大量流式数据，以便在实时 Power BI 分析仪表板中获得相关见解。

使用 [Microsoft Power BI](https://powerbi.com/) 快速构建实时仪表板。 [观看演示此方案的视频](https://www.youtube.com/watch?v=SGUpT-a99MA)。

本文介绍如何使用 Power BI 作为 Azure 流分析作业的输出创建自己的自定义商业智能工具。 此外，介绍如何利用实时仪表板。

## <a name="prerequisites"></a>先决条件
* Microsoft Azure 帐户。
* Power BI 的工作或学校帐户。
* 完成[实时欺诈检测](stream-analytics-real-time-fraud-detection.md)方案。 本文是在[实时欺诈检测](stream-analytics-real-time-fraud-detection.md)中所述工作流的基础上编写的，并添加了一个 Power BI 流数据集输出。

## <a name="add-power-bi-output"></a>添加 Power BI 输出
为作业指定输入后，可以定义 Power BI 的输出。

1. 在作业仪表板中间选中“输出”框。 然后选择“+ 添加”创建输出。

    ![添加输出](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. 提供“输出别名”。 可以使用任何便于引用的输出别名。 如果决定为作业设置多个输出，则此输出别名将很有用。 在这种情况下，需要在查询中引用此输出。 例如，使用输出别名值“StreamAnalyticsRealTimeFraudPBI”。

3. 选择“授权”。

    ![添加授权](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. 此时将打开一个窗口，可在其中提供 Azure 凭据（工作或学校帐户的凭据）。 该操作还可让 Azure 作业有权访问 Power BI 区域。

    ![授权字段](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. 提供所需的信息后，授权将会消失。 “新建输出”区域中包含“数据集名称”和“表名称”的字段。

    ![PBI 工作区](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. 按如下所述定义这些值：
    * **组工作区**：在 Power BI 租户中选择一个可在其下创建数据集的工作区。
    * **数据集名称**：提供希望 Power BI 输出使用的数据集名称。 例如，使用“StreamAnalyticsRealTimeFraudPBI”。
    * **表名称**：在 Power BI 输出数据集下提供表名称。 例如，使用“StreamAnalyticsRealTimeFraudPBI”。 目前，流分析作业的 Power BI 输出只能在数据集中设置一个表。

7. 选择“创建” 。 现已完成输出配置。

> [!WARNING]
> 如果 Power BI 已有一个数据集和表，且与此流分析作业中的数据集和表同名，将会覆盖现有数据。
> 另外，建议不要在 Power BI 帐户中显式创建此数据集和表。 在启动流分析作业，并且该作业开始向 Power BI 发送输出时，这些文件将自动创建。 如果作业查询没有返回任何结果，数据集和表将无法创建。
>
>

数据集是使用以下设置创建的；
* **defaultRetentionPolicy: BasicFIFO**：数据为 FIFO，最多 200,000 行。
* **defaultMode: pushStreaming**：支持流磁贴和基于报表的传统视觉对象（即推送）。

目前，无法其他标志创建数据集。

有关 Power BI 数据集的详细信息，请参阅 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) 参考。


## <a name="write-query"></a>编写查询
转到作业的“查询”选项卡。 编写要在 Power BI 中生成输出的查询。 例如，可以使用类似于下面的 SQL 查询来捕获电信行业中的 SIM 欺诈行为：


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>在 Power BI 中创建仪表板

1. 转到 [Powerbi.com](https://powerbi.com)，然后使用工作或学校帐户登录。 如果流分析作业查询输出了结果，可以看到数据集已创建，如下图所示：

    ![流数据集](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. 选择“添加磁贴”，然后选择自定义流数据。

    ![自定义流数据集](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. 从列表中选择你的数据集。

    ![你的流数据集](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. 创建可视化效果卡。 然后选择“fraudulentcalls”字段。

    ![添加欺诈行为](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    现已创建一个欺诈计数器！

    ![欺诈计数器](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. 再次演练添加磁贴， 但这一次请选择折线图。 添加 **fraudulentcalls** 作为值，添加 **windowend** 作为轴。 我们选择了过去 10 分钟的数据，如以下屏幕截图中所示：

![欺诈呼叫](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


本教程仅演示了如何为数据集创建一种类型的图表。 Power BI 可帮助你为组织创建其他客户商业智能工具。 如需 Power BI 仪表板的其他示例，请观看 [Power BI 入门](https://youtu.be/L-Z_6P56aas?t=1m58s)视频。

若要详细了解如何配置 Power BI 输出以及如何利用 Power BI 组，请参阅[了解流分析输出](stream-analytics-define-outputs.md "了解流分析输出")的 [Power BI部分](stream-analytics-define-outputs.md#power-bi)。 [Power BI 中的仪表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)是另一个有用的资源。

## <a name="learn-about-limitations-and-best-practices"></a>了解限制和最佳做法
Power BI 同时采用并行性和吞吐量约束，如[有关 Power BI 的此页面](https://powerbi.microsoft.com/pricing "Power BI 定价")中所述。

目前，大约每秒可调用 Power BI 一次。 流视觉对象支持 15 KB 的数据包。 超过该大小的流视觉对象将会失败（但推送将继续工作）。

由于这些限制，Power BI 最适合用于可通过 Azure 流分析来大幅减少数据加载的案例。
我们建议使用“翻转窗口”或“跳跃窗口”来确保数据推送速率最大为每秒推送一次，并且查询满足吞吐量要求。

可以使用以下公式来计算时间范围值（以秒为单位）：

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

例如：
- 1,000 个设备以一秒为间隔发送数据。
- 使用的 Power BI Pro SKU 支持 1,000,000 行/小时。
- 你想要将每个设备的平均数据量发布到 Power BI。

因此，公式为：

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

这意味着，可以将原始查询更改为：

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
如果自作业创建后或上次身份验证后更改了密码，需要重新对 Power BI 帐户进行身份验证。 如果在 Azure Active Directory (Azure AD) 租户中配置了多重身份验证，还需要每两周续订一次 Power BI 授权。 如果不续订，操作日志中会出现缺少作业输出或者“用户身份验证出错”之类的症状。

同样，如果作业尝试在令牌已过期后启动，将发生错误并且作业无法启动。 若要解决此问题，请停止正在运行的作业并转到 Power BI 输出。 为了避免数据丢失，请选择“续订授权”链接，然后从“上次停止时间”重新启动作业。

使用 Power BI 刷新授权后，授权区域中会出现一条绿色通知，指出问题已解决。

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

