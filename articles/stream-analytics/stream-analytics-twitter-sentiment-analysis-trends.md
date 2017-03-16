---
title: "使用流分析进行实时 Twitter 情绪分析 | Microsoft 文档"
description: "了解如何使用流分析进行实时 Twitter 情绪分析。 在实时仪表板上提供从事件生成到数据的分步指南。"
keywords: "实时 twitter 趋势分析、情绪分析、社交媒体分析、趋势分析示例"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 576d8663f61d344628a38a98bf9902f4194949d0
ms.lasthandoff: 03/03/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure 流分析中的实时 Twitter 情绪分析

了解如何通过将实时 Twitter 事件引入 Azure 事件中心，生成用于社交媒体分析的情绪分析解决方案。 将编写一个 Azure 流分析查询以分析数据。 然后存储结果供以后审阅或使用仪表板和 [Power BI](https://powerbi.com/) 来提供实时见解。

社交媒体分析工具可帮助组织了解热门主题，即社交媒体中拥有大量帖子的主题和观点。 情绪分析（也称为“观点挖掘”）使用社交媒体分析工具来确定大众对产品、理念等的态度。 实时 Twitter 趋势分析就是一个很好的示例，因为井号标记订阅模型使你可以侦听特定关键字并在源上开发情绪分析。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>场景：实时社交媒体情绪分析

一家拥有新闻媒体网站的公司关注的是如何打造特色网站内容，使之贴近其读者，以便获得对竞争对手的竞争优势。 该公司通过对 Twitter 数据执行实时情绪分析，以便在贴近读者的主题上使用社交媒体分析。 具体而言，要在 Twitter 上标识实时热门话题，公司需要有关键主题的推文量及情绪的实时分析。 因此本质上来说，需要一个基于该社交媒体源的情绪分析分析引擎。

## <a name="prerequisites"></a>先决条件

* Azure 订阅
* Twitter 帐户和 [OAuth 访问令牌](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* GitHub 中的 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 文件。
* 可选：[GitHub](https://aka.ms/azure-stream-analytics-twitterclient) 中 twitter 客户端的源代码

## <a name="create-an-event-hub-input"></a>创建事件中心输入

示例应用程序生成事件并将其推送到事件中心实例（简称“事件中心”）。 服务总线事件中心是适合流分析的首选事件引入方法。 有关详细信息，请参阅 [Azure 服务总线文档](/azure/service-bus/)中的事件中心文档。

### <a name="use-the-following-steps-to-create-an-event-hub"></a>按照下列步骤创建一个事件中心。

1. 在“Azure 门户”中[](https://portal.azure.com)，单击“新建”，键入“事件中心”，然后从生成的搜索中选择“事件中心”。 然后单击“创建” 。
2. 提供事件中心的名称，并创建资源组。 我已分别指定 `socialtwitter-eh` 和 `socialtwitter-rg`。 选中框以便将帐户固定到仪表板，然后单击“创建”按钮。
3. 部署完成后，单击“事件中心”，然后单击“实体”下面的“事件中心”。
4. 单击“+ 事件中心”按钮以创建事件中心。 再次提供你的名称（我的为 `socialtwitter-eh`），然后单击“创建”。
5. 若要授予对事件中心的访问权限，需创建共享访问策略。 单击“事件中心”，然后单击“设置”下面的“共享访问策略”。
6. 在“共享访问策略”下，单击“+ 添加”以使用“管理”权限创建一个新策略。 为策略指定一个名称，并选中“管理”，然后单击“创建”。
7. 创建新策略后单击它，然后针对“连接字符串 - 主键”实体单击复制按钮。 在后面的练习中需要此字符串。 然后返回到仪表板。

![共享访问策略终结点](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>配置并启动 Twitter 客户端应用程序

我们提供了一个客户端应用程序，该应用程序将通过 [Twitter 流式处理 API](https://dev.twitter.com/streaming/overview) 连接到 Twitter 数据，来收集有关参数化主题集的推文事件。 [Sentiment140](http://help.sentiment140.com/) 开源工具用于将某个情绪值分配给每个推文。

* 0 = 负面
* 2 = 中性
* 4 = 正面

然后，推文事件将推送至事件中心。  

### <a name="follow-these-steps-to-set-up-the-application"></a>请按以下步骤设置该应用程序：

1. [下载 TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 并将其解压缩。
2. 执行 `TwitterWPFClient.exe` 应用程序，并输入 Twitter API 密钥和机密、Twitter 访问令牌和密钥，以及 Azure 事件中心信息。 最后定义要依据哪些关键字确定舆情。 请注意，如果指定多个单词（使用逗号来定义多个值），并且你希望匹配其中的任意单词，则必须将控件切换到“匹配任意”。

   [生成 OAuth 访问令牌的步骤](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   你需要使用空的应用程序来生成令牌。  

3. 将 TwitterWpfClient.exe.config 中 EventHubConnectionString 和 EventHubName 的值替换为事件中心的连接字符串和名称。 之前复制的连接字符串将为你提供事件中心的连接字符串和名称，因此请确保将它们分隔开来，并将其放在正确的字段中。 例如，考虑以下连接字符串：  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   TwitterWpfClient.exe.config 文件应包含用户的设置，如下面的示例所示：
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   需要注意的一点是，文本“EntityPath=”**未**在 EventHubName 值中出现。
   
   你还可以将 Twitter 和 Azure 连接信息的值直接输入客户端。 未使用“EntityPath=”的情况同样适用相同的逻辑。
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *可选：*调整要搜索的关键字。  默认情况下，此应用程序寻找某些游戏关键字。  你可以根据需要调整 TwitterWpfClient.exe.config 中 **twitter_keywords** 的值。
5. 运行 TwitterWpfClient.exe 并单击绿色启动按钮，以收集社会舆情。 你将看到具有 **CreatedAt**、**Topic** 和 **SentimentScore** 值的推文事件，其中这些值已发送至事件中心。

## <a name="create-a-stream-analytics-job"></a>创建流分析作业

既然推文事件正在从 Twitter 实时流式传输，我们可以设置一个流分析作业来实时分析这些事件。

### <a name="provision-a-stream-analytics-job"></a>预配流分析作业

在“Azure 门户”[](https://portal.azure.com/)中，单击“新建”，键入“流分析”，然后单击“流分析”磁贴。 指定以下值，然后单击“创建”。

   * **作业名称**：输入作业名称。
   * **资源组**：从“使用现有”选项中选择此前练习中创建的资源组。
   * **存储帐户**：选择要使用的 Azure 存储帐户，以便为所有在此区域运行的流分析作业存储监视数据。 你可以选择现有存储帐户，也可以创建新的存储帐户。   

![新作业](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

创建作业后，将在 Azure 门户中打开此作业。

## <a name="specify-the-job-input"></a>指定作业输入

在“流分析”作业中，单击作业窗格中间“作业拓扑”中的“输入”，然后单击“添加”。 随后门户将提示输入以下列出的某些信息。 大部分的默认值可满足要求，但你可以参考以下对这些值的定义。
  
   * **输入别名**：输入此作业输入的友好名称，例如 `TwitterStream`。 将在后面的查询中使用此名称。
   * **事件中心名称**：选择事件中心名称。
   * **事件中心策略名称**：选择此前在本教程中创建的事件中心策略。

单击“创建”  按钮。

## <a name="specify-job-query"></a>指定作业查询

流分析支持简单的声明性查询模型，该模型用于描述转换。 若要了解有关语言的详细信息，请参阅 [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。  本教程将帮助你创作和测试多个可通过 Twitter 数据完成的查询。

为了比较各个主题的提及次数，我们使用 [Tumbling Window](https://msdn.microsoft.com/library/azure/dn835055.aspx) 每五秒按主题获取提及次数。

将代码编辑器中的查询更改为下面的代码，然后单击“保存”：

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

此查询使用 **TIMESTAMP BY** 关键字指定在临时计算中使用的有效负载的时间戳字段。 如果未指定此字段，将根据每个事件到达事件中心的时间执行窗口化操作。  通过 [Stream Analytics Query Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)（流分析查询参考）的“Arrival Time vs Application Time”（到达时间与应用程序时间比较）部分了解详细信息。

此查询还可以通过使用 **System.Timestamp** 属性访问每个窗口结束时的时间戳。

![查询框](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>创建输出接收器

现在，我们已经定义了事件流、用于引入事件的事件中心输入，以及用于通过流执行转换的查询，最后一步是为作业定义输出接收器。  我们需要将聚合的推文事件从作业查询写入 Azure Blob 存储。  你也可以将结果推送到 Azure SQL 数据库、Azure 表存储或事件中心，具体取决于特定应用程序需要。

## <a name="specify-job-output"></a>指定作业输出

在流分析作业中，单击“作业拓扑”中的“输出”，然后单击“添加”。 随后在窗格中键入或选择以下值：
   
   * **输出别名**：输入此作业输出的友好名称。 本演示使用 `Output`。
   * **接收器**：选择“Blob 存储”。
   * **存储帐户**：选择“创建新的存储帐户”。
    * **存储帐户**：为新的存储帐户指定名称（本示例中为 `socialtwitter`）
    * **容器**：为新容器指定名称（本示例中为 `socialtwitter`）

将其他项保留为默认值。 最后，单击“创建”。

## <a name="start-the-job"></a>启动作业

由于作业输入、查询和输出均已指定，因此我们可以启动流分析作业。

在作业概览窗格中，只需单击页面顶部的“启动”。

在打开的对话框中，单击“作业开始时间”，然后单击对话框底部的“勾选”按钮。 作业状态将更改为“正在启动”，然后很快变为“正在运行”。

![作业正在运行](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>查看观点分析的输出

在作业运行和处理实时 Twitter 流后，选择你希望如何查看情绪分析的输出。 使用 [Azure 存储资源管理器](https://http://storageexplorer.com/)或 [Azure 资源管理器](http://www.cerebrata.com/products/azure-explorer/introduction)之类的工具实时查看作业输出。 在这里，你可以使用 [Power BI](https://powerbi.com/) 来扩展应用程序，以包括自定义仪表板，如以下屏幕截图所示。

![powerbi](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest--in-this-scenario"></a>本场景中的另一个偏好查询

我们为此场景创建的另一个示例查询基于[滑动窗口](https://msdn.microsoft.com/library/azure/dn835051.aspx)。 为了标识热门话题，我们将查找给定时间内超出某一提及次数阈值的主题。 对于本教程而言，我们检查过去五秒内提及次数超过 20 次的主题。

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


