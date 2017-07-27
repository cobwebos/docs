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
ms.date: 06/27/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: d55cadbd60ee47b9c4b551f1b2b5bc4431bfac97
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>流分析和 Power BI：针对流数据的实时分析仪表板
Azure 流分析使你可以利用其中一种领先的商业智能工具 [Microsoft Power BI](https://powerbi.com/)。 本文将介绍如何使用 Power BI 作为 Azure 流分析作业的输出，以创建商业智能工具。 此外，还将介绍如何创建和使用实时仪表板。

本文是流分析[实时欺诈检测](stream-analytics-real-time-fraud-detection.md)教程的延续。 本文是在该教程中所创建工作流的基础上编写的，并添加了 Power BI 输出，以便可视化流分析作业检测到的欺诈性电话呼叫。 

可观看演示此方案的[视频](https://www.youtube.com/watch?v=SGUpT-a99MA)。


## <a name="prerequisites"></a>先决条件

在开始之前，请确保做好以下准备：

* 一个 Azure 帐户。
* Power BI 帐户。 可使用工作帐户或学校帐户。
* [实时欺诈检测](stream-analytics-real-time-fraud-detection.md)教程的完整版本。 本教程包括的应用可生成虚拟的电话呼叫元数据。 在本教程中，需创建一个事件中心，并将流式处理电话呼叫数据发送到该事件中心。 编写查询，以便检测欺诈性呼叫（同一时间来自不同地点的相同号码呼叫）。 


## <a name="add-power-bi-output"></a>添加 Power BI 输出
在实时欺诈检测教程中，输出将被发送到 Azure Blob 存储。 本部分将添加向 Power BI 发送信息的输出。

1. 在 Azure 门户中，打开前面创建的流分析作业。 如果使用建议的名称，则作业名为 `sa_frauddetection_job_demo`。

2. 在作业仪表板中间，选择“输出”框，然后选择“+ 添加”。

3. 对于“输出别名”，请输入“`CallStream-PowerBI`”。 可使用其他名称。 如果使用其他名称，请记下该名称，稍后会用到。 

4. 在“接收器”下，选择“Power BI”。

   ![创建 Power BI 的输出](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. 单击“授权”。

    此时将打开一个窗口，可在其中为工作或学校帐户提供 Azure 凭据。 

    ![输入凭据，以便访问 Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. 输入凭据。 请注意，输入凭据还意味着允许流分析作业访问 Power BI 区域。

7. 返回“新建输出”边栏选项卡后，请输入以下信息：

    * 组工作区：在 Power BI 租户中选择要在其下创建数据集的工作区。
    * 数据集名称：输入“`sa-dataset`”。 可使用其他名称。 如果使用其他名称，请记下该名称，稍后会用到。
    * 表名称：输入“`fraudulent-calls`”。 目前，流分析作业的 Power BI 输出在 1 个数据集中只能有 1 个表。

    ![PBI 工作区](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > 如果 Power BI 已有 1 个数据集和 1 个表，且与流分析作业中指定的数据集和表同名，则会覆盖现有的数据集和表。
    > 建议不要在 Power BI 帐户中显式创建此数据集和表。 在启动流分析作业，并且该作业开始向 Power BI 发送输出时，这些文件将自动创建。 如果作业查询没有返回任何结果，则无法创建数据集和表。
    >

8. 单击“创建” 。

数据集是使用以下设置创建的；

* **defaultRetentionPolicy: BasicFIFO**：数据为 FIFO，最多 200,000 行。
* defaultMode: pushStreaming：数据集支持流磁贴和基于报表的传统视觉对象（即 推送）。

目前，无法其他标志创建数据集。

有关 Power BI 数据集的详细信息，请参阅 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) 参考。


## <a name="write-the-query"></a>编写查询

1. 关闭“输出”边栏选项卡，然后返回到“作业”边栏选项卡。

2. 单击“查询”框。 

3. 输入以下查询。 此查询类似于欺诈检测教程中创建的自联接查询。 区别在于，此查询将结果发送到创建的新输出 (`CallStream-PowerBI`)。 

    >[!NOTE]
    >如果在欺诈检测教程中未对输入 `CallStream` 命名，请在查询的 FROM 和 JOIN 子句中替换 `CallStream` 的名称。

        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))

4. 单击“保存” 。


## <a name="test-the-query"></a>测试查询
本部分是可选的，但建议执行。 

1. 如果 TelcoStreaming 应用当前未运行，请按照以下步骤启动此应用：

    * 打开命令窗口。
    * 请转到 telcogenerator.exe 和修改的 telcodatagen.exe.config 文件所在的文件夹。
    * 运行以下命令：

            telcodatagen.exe 1000 .2 2

2. 在“查询”边栏选项卡中，单击 `CallStream` 输入旁边的点，然后选择“来自输入的示例数据”。

3. 指定你需要 3 分钟的数据，然后单击“确定”。 请等待，直到收到数据已经采样的通知。

4. 单击“测试”，确保获取结果。


## <a name="run-the-job"></a>运行作业

1. 请确保 TelcoStreaming 应用正在运行。

2. 关闭“查询”边栏选项卡。

3. 在“作业”边栏选项卡中，单击“启动”。

    ![启动流分析作业](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

流分析作业开始在传入流中查找欺诈性呼叫。 该作业还会在 Power BI 中创建数据集和表，并开始向其发送欺诈性呼叫相关数据。


## <a name="create-the-dashboard-in-power-bi"></a>在 Power BI 中创建仪表板

1. 转到 [Powerbi.com](https://powerbi.com)，然后使用工作或学校帐户登录。 如果流分析作业查询输出了结果，则可看到数据集已创建：

    ![Power BI 中的流式处理数据集](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. 在工作区中，单击“+&nbsp;创建”。

    ![Power BI 工作区中的“创建”按钮](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. 创建新的仪表板，并将其命名为 `Fraudulent Calls`。

    ![创建仪表板，并在 Power BI 工作区中对其命名](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. 在窗口顶部，单击“添加磁贴”，选择“自定义流式处理数据”，然后单击“下一步”。

    ![自定义流数据集](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. 在“你的数据集”下，选择数据集，然后单击“下一步”。

    ![你的流数据集](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. 在“可视化效果类型”下选择“卡”，然后在“字段”列表中选择“fraudulentcalls”。

    ![新磁贴的可视化效果详细信息](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. 单击“下一步”。

8. 填写磁贴详细信息，例如标题和副标题。

    ![新磁贴的标题和副标题](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. 单击“应用” 。

    现已创建一个欺诈计数器！

    ![欺诈计数器](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. 再次按照上述步骤添加磁贴（从步骤 4 开始）。 这一次，请执行以下操作：

    * 转到“可视化效果类型”后，选择“折线图”。 
    * 添加轴，然后选择“windowend”。 
    * 添加值，然后选择“fraudulentcalls”。
    * 对于“要显示的时间窗口”，请选择最近 10 分钟。

    ![创建折线图磁贴](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. 单击“下一步”，添加标题和副标题，然后单击“应用”。

    现在，Power BI 仪表板显示关于流式处理数据中检测到的欺诈性呼叫数据的两个视图。

    ![已完成的 Power BI 仪表板，其中显示欺诈性呼叫的两个磁贴](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>详细了解 Power BI

本教程演示了如何为数据集创建仅几种类型的可视化效果。 Power BI 可帮助你为组织创建其他客户商业智能工具。 有关详细信息，请参阅以下资源：

* 如需 Power BI 仪表板的其他示例，请观看 [Power BI 入门](https://youtu.be/L-Z_6P56aas?t=1m58s)视频。
* 若要详细了解如何配置 Power BI 的流分析作业输出以及如何使用 Power BI 组，请参阅[流分析输出](stream-analytics-define-outputs.md)一文中的 [Power BI](stream-analytics-define-outputs.md#power-bi) 部分。 
* 若要了解 Power BI 的常规使用方法，请参阅 [Power BI 中的仪表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)。


## <a name="learn-about-limitations-and-best-practices"></a>了解限制和最佳做法
目前，大约每秒可调用 Power BI 一次。 流视觉对象支持 15 KB 的数据包。 超过该大小的流视觉对象将会失败（但推送将继续工作）。 由于这些限制，Power BI 最适合用于可通过 Azure 流分析来大幅减少数据加载的案例。 建议使用“翻转窗口”或“跳跃窗口”来确保数据推送速率最大为每秒推送一次，并且查询满足吞吐量要求。

可以使用以下公式来计算时间范围值（以秒为单位）：

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

例如：

* 1,000 个设备以一秒为间隔发送数据。
* 使用的 Power BI Pro SKU 支持 1,000,000 行/小时。
* 你想要将每个设备的平均数据量发布到 Power BI。

因此，公式为：

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

对于此配置，可将原始查询更改为以下内容：

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>续订授权
如果自作业创建后或上次身份验证后更改了密码，需要重新对 Power BI 帐户进行身份验证。 如果在 Azure Active Directory (Azure AD) 租户中配置了多重身份验证，还需要每两周续订一次 Power BI 授权。 如果不续订，操作日志中会出现缺少作业输出或者 `Authenticate user error` 之类的表现。

同样，如果作业在令牌过期后启动，则会发生错误且作业将失败。 若要解决此问题，请停止正在运行的作业并转到 Power BI 输出。 为了避免数据丢失，请选择“续订授权”链接，然后从“上次停止时间”重新启动作业。

使用 Power BI 刷新授权后，授权区域中会出现一条绿色通知，指出问题已解决。

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

