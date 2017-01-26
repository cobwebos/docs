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
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3c97604b17636f011ddb2acda40fbc77afeab590
ms.openlocfilehash: 9f7e9008f29b2b1a3a0422133e15871c4ce7cca8


---
# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>社交媒体分析：在 Azure 流分析中进行实时 Twitter 观点分析
了解如何通过将实时 Twitter 事件引入 Azure 事件中心，生成用于社交媒体分析的情绪分析解决方案。 将编写一个 Azure 流分析查询以分析数据。 将存储以后审阅的结果或使用仪表板和 [Power BI](https://powerbi.com/) 来提供实时见解。

社交媒体分析工具可帮助组织了解热门主题，即社交媒体中拥有大量帖子的主题和观点。 情绪分析（也称为“观点挖掘”）使用社交媒体分析工具来确定大众对产品、理念等的态度。 实时 Twitter 趋势分析就是一个很好的示例，因为井号标记订阅模型使你可以侦听特定关键字并在源上开发情绪分析。

## <a name="scenario-sentiment-analysis-in-real-time"></a>方案：实时情绪分析
一家拥有新闻媒体网站的公司关注的是如何打造特色网站内容，使之贴近其读者，以便获得对竞争对手的竞争优势。 该公司通过对 Twitter 数据执行实时情绪分析，以便在贴近读者的主题上使用社交媒体分析。 具体而言，要标识 Twitter 上的实时热门话题，公司需要关于关键主题推文量及情绪的实时分析。 因此本质上来说，需要一个基于该社交媒体源的情绪分析分析引擎。

## <a name="prerequisites"></a>先决条件
* Twitter 帐户和 [OAuth 访问令牌](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* 来自 Microsoft 下载中心的 [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)
* 可选：[GitHub](https://aka.ms/azure-stream-analytics-twitterclient) 中 twitter 客户端的源代码

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>创建事件中心输入和使用者组
示例应用程序将生成事件并将其推送到事件中心实例（简称“事件中心”）。 服务总线事件中心是适合流分析的首选事件引入方法。 请参阅 [Azure 服务总线文档](/azure/service-bus/)中的事件中心文档。

按照下列步骤创建一个事件中心。

1. 在 Azure 门户中，依次单击“新建” > “应用程序服务” > “服务总线” > “事件中心” > “快速创建”，然后提供名称、区域以及新的或现有的命名空间。  
2. 最佳做法是让每个流分析作业都从单个事件中心使用者组进行读取。 我们稍后将引导你完成创建使用者组的过程。 可以在 [Azure 事件中心概述](https://msdn.microsoft.com/library/azure/dn836025.aspx)上了解有关使用者组的详细信息。 若要创建使用者组，请转到新创建的事件中心，单击“使用者组”选项卡、单击页面底部的“创建”，然后为使用者组提供名称。
3. 若要授予对事件中心的访问权限，需创建共享访问策略。 单击事件中心的“配置”选项卡。
4. 在“共享访问策略”下，使用“管理”权限创建一个新策略。

   ![共享访问策略：你可以在其中使用管理权限创建策略。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)
5. 单击页面底部的“保存”。
6. 转到“仪表板”，单击页面底部的“连接信息”，然后复制并保存该连接信息。 （使用显示在搜索图标下的复制图标。）

## <a name="configure-and-start-the-twitter-client-application"></a>配置并启动 Twitter 客户端应用程序
我们提供了一个客户端应用程序，该应用程序将通过 [Twitter 流式处理 API](https://dev.twitter.com/streaming/overview) 连接到 Twitter 数据，来收集有关参数化主题集的推文事件。 [Sentiment140](http://help.sentiment140.com/) 开源工具用于将某个情绪值分配给每个推文。

* 0 = 负面
* 2 = 中性
* 4 = 正面

然后，推文事件将推送至事件中心。  

请按以下步骤设置该应用程序：

1. [下载 TwitterClient 解决方案](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)。
2. 打开 TwitterClient.exe.config，然后将 oauth_consumer_key、oauth_consumer_secret、oauth_token 和 oauth_token_secret 替换为具有你自己的值的 Twitter 令牌。  

   [生成 OAuth 访问令牌的步骤](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   请注意，你需要使用空的应用程序来生成令牌。  
3. 将 TwitterClient.exe.config 中 EventHubConnectionString 和 EventHubName 的值替换为事件中心的连接字符串和名称。 之前复制的连接字符串将为你提供事件中心的连接字符串和名称，因此请确保将它们分隔开来，并将其放在正确的字段中。 例如，考虑以下连接字符串：

     Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

   TwitterClient.exe.config 文件应该包含你的设置，如下面的示例所示：

     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"   add key="EventHubName" value="yourhub"

   需要注意的一点是，文本“EntityPath=”**未**在 EventHubName 值中出现。
4. *可选：*调整要搜索的关键字。  目前情况下，此应用程序会搜索“Azure,Skype,XBox,Microsoft,Seattle”。  你可以根据需要调整 TwitterClient.exe.config 中 **twitter_keywords** 的值。
5. 运行 TwitterClient.exe 以启动应用程序。 你将看到具有 **CreatedAt**、**Topic** 和 **SentimentScore** 值的推文事件，其中这些值已发送至事件中心。

   ![观点分析：发送到事件中心的 SentimentScore 值。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>创建流分析作业
既然推文事件正在从 Twitter 实时流式传输，我们可以设置一个流分析作业来实时分析这些事件。

### <a name="provision-a-stream-analytics-job"></a>预配流分析作业
1. 在 [Azure 门户](https://manage.windowsazure.com/)中，依次单击“新建” > “数据服务” > “流分析” > “快速创建”。
2. 指定以下值，然后单击“创建流分析作业”：

   * **作业名称**：输入作业名称。
   * **区域**：选择要运行作业的区域。 考虑将作业和事件中心放在同一区域，以确保获得更好的性能，并确保在不同区域之间传输数据时不需付费。
   * **存储帐户**：选择要使用的 Azure 存储帐户，以便为所有在此区域运行的流分析作业存储监视数据。 你可以选择现有存储帐户，也可以创建新的存储帐户。
3. 单击左窗格中的“流分析”，列出流分析作业。  
   ![流分析服务图标](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

   新作业显示的状态为“已创建”。 请注意，页面底部的“启动”按钮已禁用。 你必须先配置作业输入、输出和查询，然后才能启动作业。

### <a name="specify-job-input"></a>指定作业输入
1. 在流分析作业中，单击页面顶部的“输入”，然后单击“添加输入”。 此时会打开一个对话框，引导你完成设置输入所需的一系列步骤。
2. 单击“数据流”，然后单击右侧按钮。
3. 选择“事件中心”，然后单击右侧按钮。
4. 在第三页中键入或选择以下值：

   * **输入别名**：输入此作业输入的友好名称，例如 *TwitterStream*。 请注意，将在后面的查询中使用此名称。
     **事件中心**：如果创建的事件中心与流分析作业属于同一订阅，请选择事件中心所在的命名空间。

     如果事件中心属于其他订阅，请单击“使用其他订阅的事件中心”，然后手动输入以下项目的相关信息：**服务总线命名空间**、**事件中心名称**、**事件中心策略名称**、**事件中心策略密钥**以及**事件中心分区计数**。
   * **事件中心名称**：选择事件中心名称。
   * **事件中心策略名称**：选择此前在本教程中创建的事件中心策略。
   * **事件中心使用者组**：键入此前在本教程中创建的使用者组。
5. 单击右侧按钮。
6. 指定以下值：

   * **事件序列化程序格式**：JSON
   * **编码**：UTF8
7. 单击相应“勾选”按钮以添加此源，并验证流分析是否可以成功连接到事件中心。

### <a name="specify-job-query"></a>指定作业查询
流分析支持简单的声明性查询模型，该模型用于描述转换。 若要了解有关语言的详细信息，请参阅 [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。  本教程将帮助你创作和测试多个可通过 Twitter 数据完成的查询。

#### <a name="sample-data-input"></a>示例数据输入
若要针对实际的作业数据来验证查询，可使用“示例数据”功能从流中提取事件，然后创建包含要测试事件的 .json 文件。

1. 选择事件中心输入，然后单击页面底部的“示例数据”。
2. 在出现的对话框中，指定开始收集数据的“开始时间”，以及使用额外数据的“持续时间”。
3. 单击“详细信息”按钮，然后单击“单击此处”链接以下载并保存所生成的 .json 文件。

#### <a name="pass-through-query"></a>传递查询
开始时，我们将进行简单的传递查询来投射事件中的所有字段。

1. 单击流分析作业页顶部的“查询”。
2. 在代码编辑器中，用以下内容替换初始查询模板：

     SELECT * FROM TwitterStream

   请确保输入源的名称与此前指定的输入的名称相匹配。
3. 单击查询编辑器下的“测试”。
4. 转到示例 .json 文件。
5. 单击**复选**按钮，将在查询定义下方看到结果。

   ![显示在查询定义下方的结果](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>按主题的推文计数：带聚合功能的翻转窗口
为了比较主题的提及次数，我们将使用 [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) 每五秒按主题获取提及次数。

1. 在代码编辑器中将查询更改为：

     SELECT System.Timestamp as Time, Topic, COUNT(*)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic

   此查询使用 **TIMESTAMP BY** 关键字指定在临时计算中使用的有效负载的时间戳字段。 如果未指定此字段，将根据每个事件到达事件中心的时间执行窗口化操作。  通过[流分析查询参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)的“到达时间与应用程序时间”部分了解详细信息。

   此查询还可以通过使用 **System.Timestamp** 属性访问每个窗口结束时的时间戳。
2. 单击查询编辑器下的“重新运行”，查看查询结果。

#### <a name="identify-trending-topics-sliding-window"></a>标识热门话题：滑动窗口
为了标识热门话题，我们将查找给定时间内超出某一提及次数阈值的主题。 为实现本教程的目的，我们将使用 [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx) 查看过去五秒内提及次数超过 20 次的主题。

1. 将代码编辑器中的查询更改为以下内容：   SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY SLIDINGWINDOW(s, 5), topic   HAVING COUNT(*) > 20
2. 单击查询编辑器下的“重新运行”，查看查询结果。

   ![滑动窗口查询输出](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>提及次数和观点：带聚合的翻转窗口
测试的最后一个查询会使用 **TumblingWindow** 获取每五秒钟每个主题的提及次数、情绪分数的最小、最大和标准偏差。

1. 在代码编辑器中将查询更改为：

     SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),   Max(SentimentScore), STDEV(SentimentScore)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic
2. 单击查询编辑器下的“重新运行”，查看查询结果。
3. 这是将要用于仪表板的查询。  单击页面底部的“保存”。

## <a name="create-output-sink"></a>创建输出接收器
现在，我们已经定义了事件流、用于引入事件的事件中心输入，以及用于通过流执行转换的查询，最后一步是为作业定义输出接收器。  我们需要将聚合的推文事件从作业查询写入 Azure Blob 存储。  你也可以将结果推送到 Azure SQL 数据库、Azure 表存储或事件中心，具体取决于特定应用程序需要。

使用以下步骤为 Blob 存储创建容器（如果还没有容器）：

1. 使用现有存储帐户，或者通过依次单击“新建” > “数据服务” > “存储” > “快速创建”，然后按照屏幕上的说明来创建新的存储帐户。
2. 选择存储帐户，单击页面顶部的“容器”，然后单击“添加”。
3. 指定容器的“名称”，然后将其“访问权限”设置为“公共 Blob”。

## <a name="specify-job-output"></a>指定作业输出
1. 在流分析作业中，单击页面顶部的“输出”，然后单击“添加输出”。 此时会打开一个对话框，引导你完成设置输出的若干步骤。
2. 单击“Blob 存储”，然后单击右侧按钮。
3. 在第三页中键入或选择以下值：

   * **输出别名**：输入此作业输出的友好名称。
   * **订阅**：如果创建的 Blob 存储与流分析作业属于同一订阅，请单击“使用当前订阅中的存储帐户”。 如果存储属于其他订阅，请单击“使用其他订阅中的存储帐户”，然后针对“存储帐户”、“存储帐户密钥”和“容器”手动输入相关信息。
   * **存储帐户**：选择存储帐户的名称。
   * **容器**：选择容器名称。
   * **文件名前缀**：键入写入 blob 输出时要使用的文件前缀。
4. 单击右侧按钮。
5. 指定以下值：
   * **事件序列化程序格式**：JSON
   * **编码**：UTF8
6. 单击相应“勾选”按钮以添加此源，并验证流分析是否可以成功连接到存储帐户。

## <a name="start-job"></a>启动作业
由于作业输入、查询和输出均已指定，因此我们可以启动流分析作业。

1. 在作业“仪表板”上，单击页面底部的“启动”。
2. 在打开的对话框中，单击“作业开始时间”，然后单击对话框底部的“勾选”按钮。 作业状态将更改为“正在启动”，然后很快变为“正在运行”。

## <a name="view-output-for-sentiment-analysis"></a>查看观点分析的输出
在作业运行和处理实时 Twitter 流后，选择你希望如何查看情绪分析的输出。 使用 [Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)或 [Azure 资源管理器](http://www.cerebrata.com/products/azure-explorer/introduction)之类的工具实时查看作业输出。 在这里，你可以使用 [Power BI](https://powerbi.com/) 来扩展应用程序，以包括自定义仪表板，如以下屏幕截图所示。

![社交媒体分析：Power BI 仪表板中的流分析观点分析（观点挖掘）输出。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


