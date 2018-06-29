---
title: 使用 Azure 流分析进行实时 Twitter 情绪分析
description: 本文说明如何使用流分析进行实时 Twitter 情绪分析。 在实时仪表板上提供从事件生成到数据的分步指南。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 0b920d21486fc0003d8b11bef79bd44be4b28adf
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030608"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure 流分析中的实时 Twitter 情绪分析

了解如何通过将实时 Twitter 事件引入 Azure 事件中心，生成用于社交媒体分析的情绪分析解决方案。 然后，可以编写 Azure 流分析查询来分析数据，并存储结果以供将来使用，或者使用仪表板和 [Power BI](https://powerbi.com/) 提供实时见解。

社交媒体分析工具可帮助组织了解热门话题。 热门话题是在社交媒体中有大量博文的主题和态度。 情绪分析（也称为“观点挖掘”）使用社交媒体分析工具来确定大众对产品、理念等的态度。 

实时 Twitter 趋势分析就是一个很好的分析工具示例，因为井号标签订阅模型使你可以侦听特定关键字（井号标签）并开发源的情绪分析。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>场景：实时社交媒体情绪分析

一家拥有新闻媒体网站的公司关注如何打造特色网站内容，使之迅速贴近其读者，以便增加相较于其竞争对手的竞争优势。 该公司通过对 Twitter 数据执行实时情绪分析，以便在贴近读者的主题上使用社交媒体分析。

若要标识 Twitter 上的实时热门话题，公司需要关于关键主题推文量及情绪的实时分析。 换而言之，需要一个基于该社交媒体源的情绪分析分析引擎。

## <a name="prerequisites"></a>先决条件
本教程将使用客户端应用程序连接到 Twitter，并查找具有特定井号标签（可设置）的推文。 为了运行应用程序并使用 Azure 流分析来分析推文，必须具备以下内容：

* Azure 订阅
* Twitter 帐户 
* Twitter 应用程序，以及该应用程序的 [OAuth 访问令牌](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)。 稍后将提供有关如何创建 Twitter 应用程序的高级说明。
* TwitterWPFClient 应用程序，用于读取 Twitter 源。 若要获取此应用程序，请从 GitHub 下载 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 文件，然后将包解压缩到计算机上的文件夹中。 若要查看源代码，并在调试程序中运行该应用程序，请从 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) 获取源代码。 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>为流分析输入创建事件中心

示例应用程序生成事件并将其推送到 Azure 事件中心。 Azure 事件中心是适合流分析的首选事件引入方法。 有关详细信息，请参阅 [Azure 事件中心文档](../event-hubs/event-hubs-what-is-event-hubs.md)。


### <a name="create-an-event-hub-namespace-and-event-hub"></a>创建事件中心命名空间和事件中心
在此过程中，首先创建事件中心命名空间，然后将事件中心添加到该命名空间。 事件中心命名空间用于逻辑分组相关的事件总线实例。 

1. 登录 Azure 门户，然后依次单击“创建资源” > “物联网” > “事件中心”。 

2. 在“创建命名空间”边栏选项卡中，输入命名空间名称，例如 `<yourname>-socialtwitter-eh-ns`。 可以对命名空间使用任何名称，但该名称必须对 URL 有效，并且在 Azure 中必须唯一。 
    
3. 选择订阅并创建或选择一个资源组，然后单击“创建”。 

    ![创建事件中心命名空间](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. 完成部署命名空间后，在 Azure 资源列表中找到事件中心命名空间。 

5. 单击新的命名空间，然后在“命名空间”边栏选项卡中，单击“+&nbsp;事件中心”。 

    ![用于创建新事件中心的“添加事件中心”按钮 ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. 将新事件中心命名为 `socialtwitter-eh`。 可使用其他名称。 如果使用其他名称，请记下该名称，稍后会用到。 不需要为事件中心设置任何其他选项。

    ![用于创建新事件中心的边栏选项卡](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. 单击“创建”。


### <a name="grant-access-to-the-event-hub"></a>授予对事件中心的访问权限

在进程可以将数据发送到事件中心之前，事件中心必须具有允许适当访问的策略。 访问策略生成包含授权信息的连接字符串。

1.  在“事件命名空间”边栏选项卡中，单击“事件中心”，然后单击新事件中心的名称。

2.  在“事件中心”边栏选项卡中，单击“共享访问策略”，然后单击“+&nbsp;添加”。

    >[!NOTE]
    >确保使用的是事件中心，而不是事件中心命名空间。

3.  添加名为 `socialtwitter-access` 的策略并对“声明”选择“管理”。

    ![用于创建新事件中心访问策略的边栏选项卡](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  单击“创建”。

5.  部署策略后，在共享访问策略列表中单击该策略。

6.  找到标记为“连接字符串 - 主键”的框，然后单击连接字符串旁边的“复制”按钮。 
    
    ![从访问策略复制主连接字符串密钥](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  将连接字符串粘贴到文本编辑器中。 对此连接字符串稍加编辑，以便在下一部分中使用。

    连接字符串如下所示：

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    请注意，连接字符串包含多个键值对，用分号分隔：`Endpoint`、`SharedAccessKeyName`、`SharedAccessKey` 和 `EntityPath`。  

    > [!NOTE]
    > 为安全起见，已删除示例中的部分连接字符串。

8.  在文本编辑器中，删除连接字符串的 `EntityPath` 对（务必记得删除字符串前面的分号）。 完成后，连接字符串如下所示：

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>配置并启动 Twitter 客户端应用程序
客户端应用程序直接从 Twitter 获取推文事件。 为了做到这一点，需要为其赋予调用 Twitter 流式处理 API 的权限。 若要配置该权限，可在 Twitter 中创建一个应用程序，该应用会生成唯一凭据（例如 OAuth 标记）。 然后，可配置客户端应用程序，以便在其执行 API 调用时使用这些凭据。 

### <a name="create-a-twitter-application"></a>创建 Twitter 应用程序
如果还没有可用于本教程的 Twitter 应用程序，则可以创建一个。 必须已具有 Twitter 帐户。

> [!NOTE]
> Twitter 中创建应用程序以及获取密钥、机密和令牌的确切过程可能会改变。 如果这些说明与你在 Twitter 网站上看到的内容不符，请参阅 Twitter 开发人员文档。

1. 转到 [Twitter 应用程序管理页](https://apps.twitter.com/)。 

2. 创建新应用程序。 

    * 对于网站 URL，请指定有效的 URL。 它不必是活动站点。 （不能仅指定 `localhost`。）
    * 将回叫字段留空。 本教程使用的客户端应用程序不需要回叫。

    ![在 Twitter 中创建应用程序](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. （可选）将应用程序的权限更改为只读。

4. 创建应用程序后，转到“密钥和访问令牌”页面。

5. 单击此按钮，生成访问令牌和访问令牌机密。

为方便起见，请保存此信息，因为下一个过程将用到它。

>[!NOTE]
>输入 Twitter 应用程序的密钥和机密即可访问 Twitter 帐户。 与 Twitter 密码一样，请将此信息视为敏感信息对待。 例如，不要在你提供给其他人的应用程序中嵌入此信息。 


### <a name="configure-the-client-application"></a>配置客户端应用程序
我们创建了一个客户端应用程序，该应用程序使用 [Twitter 流式处理 API](https://dev.twitter.com/streaming/overview) 连接到 Twitter 数据，来收集有关特定主题集的推文事件。 应用程序使用 [Sentiment140](http://help.sentiment140.com/) 开放源代码工具，该工具会为每篇推文分配以下情绪值：

* 0 = 负面
* 2 = 中性
* 4 = 正面

推文事件分配有情绪值后，系统就会将其推送到之前创建的事件中心。

运行应用程序之前，需要提供某些信息，例如 Twitter 密钥和事件中心连接字符串。 可通过以下介绍的方法提供配置信息：

* 运行应用程序，然后使用该应用程序的 UI 输入密钥、机密和连接字符串。 如果这样做，配置信息将用于当前会话，但不会保存。
* 编辑该应用程序的 .config 文件，并在此处设置值。 此方法会保留配置信息，但这也意味着此潜在的敏感信息会以纯文本形式存储在计算机上。

以下过程介绍了两种方法。 

1. 请确保已下载并解压缩 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 应用程序，如先决条件中所述。

2. 若要在运行时设置值（且仅针对当前会话），请运行 `TwitterWPFClient.exe` 应用程序。 应用程序出现提示时，请输入以下值：

    * Twitter 使用者密钥（API 密钥）。
    * Twitter 使用者机密（API 机密）。
    * Twitter 访问令牌。
    * Twitter 访问令牌机密。
    * 之前保存的连接字符串信息。 请务必使用从中删除 `EntityPath` 键值对的连接字符串。
    * 要为其确定情绪的 Twitter 关键字。

   ![正在运行的 TwitterWpfClient 应用程序，其中显示模糊设置](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. 若要永久设置值，请使用文本编辑器打开 TwitterWpfClient.exe.config 文件。 然后在 `<appSettings>` 元素中，执行此操作：

    * 将 `oauth_consumer_key` 设置为 Twitter 使用者密钥（API 密钥）。 
    * 将 `oauth_consumer_secret` 设置为Twitter 使用者机密（API 机密）。
    * 将 `oauth_token` 设置为 Twitter 访问令牌。
    * 将 `oauth_token_secret` 设置为 Twitter 访问令牌机密。

    然后在 `<appSettings>` 元素中，执行以下更改：

    * 将 `EventHubName` 设置为事件中心名称（即实体路径的值）。
    * 将 `EventHubNameConnectionString` 设置为连接字符串。 请务必使用从中删除 `EntityPath` 键值对的连接字符串。

    `<appSettings>` 部分如以下示例所示。 （为清楚和安全起见，我们包装了一些行并删除了一些字符。）

    ![文本编辑器中的 TwitterWpfClient 应用程序配置文件，其中显示 Twitter 密钥和机密，以及事件中心连接字符串信息](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. 如果尚未启动应用程序，请立即运行 TwitterWpfClient.exe。 

5. 单击绿色启动按钮，以收集社交情绪。 会看到具有 **CreatedAt**、**Topic** 和 **SentimentScore** 值的推文事件，其中这些值已发送至事件中心。

    ![正在运行的 TwitterWpfClient 应用程序，其中显示推文列表](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >如果看到错误，并且在窗口下半部分看不到显示的推文流，请仔细检查密钥和机密。 还需检查连接字符串（请确保其不包含 `EntityPath` 键和值。）


## <a name="create-a-stream-analytics-job"></a>创建流分析作业

既然推文事件正在从 Twitter 实时流式传输，那就可以设置一个流分析作业来实时分析这些事件。

1. 在 Azure 门户中，单击“创建资源” > “物联网” > “流分析作业”。

2. 将作业命名为 `socialtwitter-sa-job`，然后指定订阅、资源组和位置。

    为获得最佳性能，最好将作业和事件中心放置在同一区域，这样就不需要在不同区域之间传输数据。

    ![创建新的流分析作业](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. 单击“创建”。

    创建作业后，门户就会显示作业详细信息。


## <a name="specify-the-job-input"></a>指定作业输入

1. 在“流分析”作业的作业边栏选项卡中间的“作业拓扑”中，单击“输入”。 

2. 在“输入”边栏选项卡中，单击“+&nbsp;添加”，然后在边栏选项卡中填写以下值：

    * 输入别名：使用名称 `TwitterStream`。 如果使用其他名称，请将其记录下来，因为稍后需要该名称。
    * 源类型：选择“数据流”。
    * 源：选择“事件中心”。
    * 导入选项：选择“从当前订阅使用事件中心”。 
    * 服务总线命名空间：选择之前创建的事件中心命名空间 (`<yourname>-socialtwitter-eh-ns`)
    * 事件中心：选择之前创建的事件中心 (`socialtwitter-eh`)。
    * 事件中心策略名称：选择之前创建的访问策略 (`socialtwitter-access`)。

    ![为流分析作业创建新输入](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. 单击“创建”。


## <a name="specify-the-job-query"></a>指定作业查询

流分析支持简单的声明性查询模型，该模型用于描述转换。 若要了解有关语言的详细信息，请参阅 [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。  本教程会帮助你创作和测试多个针对 Twitter 数据的查询。

若要比较各个主题的提及次数，可使用[翻转窗口](https://msdn.microsoft.com/library/azure/dn835055.aspx)每五秒按主题获取提及次数。

1. 如果尚未关闭“输入”边栏选项卡，请将其关闭。

2. 在“作业”边栏选项卡中，单击“查询”框。 Azure 会列出为作业配置的输入和输出，并允许创建查询，以便在将输入流发送到输出时对其进行转换。

3. 请确保 TwitterWpfClient 应用程序正在运行。 

3. 在“查询”边栏选项卡中，单击 `TwitterStream` 输入旁边的点，然后选择“来自输入的示例数据”。

    ![对流分析作业条目使用示例数据的菜单选项，其中选择了“来自输入的示例数据”](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    这会打开一个边栏选项卡，可在其中指定要获取的示例数据量，具体取决于读取输入流的时长。

4. 将“分钟”设置为 3，然后单击“确定”。 
    
    ![用于对输入流进行采样的选项，其中选择了“3 分钟”。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure 会从输入流中进行 3 分钟的数据采样，并在示例数据准备就绪时发出通知。 （这用不了多长时间。） 

    查询窗口打开时，示例数据会临时存储并可供使用。 如果关闭查询窗口，示例数据将被丢弃，必须创建新的示例数据集。 

5. 在代码编辑器中将查询更改为以下内容：

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    如果未将 `TwitterStream` 用作输入的别名，请在查询中将别名替换为 `TwitterStream`。  

    此查询使用 **TIMESTAMP BY** 关键字指定在临时计算中使用的有效负载的时间戳字段。 如果未指定此字段，会根据每个事件到达事件中心的时间执行窗口化操作。 通过 [Stream Analytics Query Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)（流分析查询参考）的“Arrival Time vs Application Time”（到达时间与应用程序时间比较）部分了解详细信息。

    此查询还可以通过使用 **System.Timestamp** 属性访问每个窗口结束时的时间戳。

5. 单击“测试”。 查询会针对已采用的数据运行。
    
6. 单击“ **保存**”。 这将查询保存为流分析作业的一部分。 （不会保存示例数据。）


## <a name="experiment-using-different-fields-from-the-stream"></a>使用流中的不同字段进行试验 

下表列出了属于 JSON 流式处理数据的字段。 请随意在查询编辑器中练习。

|JSON 属性 | 定义|
|--- | ---|
|CreatedAt | 推文的创建时间|
|主题 | 与指定的关键字匹配的主题|
|SentimentScore | Sentiment140 的观点分数|
|作者 | 发送推文的 Twitter 句柄|
|文本 | 推文的完整正文|


## <a name="create-an-output-sink"></a>创建输出接收器

现已定义事件流、用于引入事件的事件中心输入，以及用于通过流执行转换的查询。 最后一步是为作业定义输出接收器。  

本教程介绍将聚合的推文事件从作业查询写入 Azure Blob 存储。  也可以将结果推送到 Azure SQL 数据库、Azure 表存储、事件中心或 Power BI，具体取决于应用程序需求。

## <a name="specify-the-job-output"></a>指定作业输出

1. 在“作业拓扑”部分，单击“输出”框。 

2. 在“输出”边栏选项卡中，单击“+&nbsp;添加”，然后在边栏选项卡中填写以下值：

    * “输出别名”：使用名称 `TwitterStream-Output`。 
    * **接收器**：选择“Blob 存储”。
    * “导入选项”：选择“从当前订阅使用 blob 存储”。
    * **存储帐户**： 选择“创建新存储帐户”。
    * “存储帐户”（第二个框）。 输入 `YOURNAMEsa`，其中 `YOURNAME` 是你的姓名或另一唯一字符串。 该名称只能使用小写字母和数字，并且在 Azure 中必须唯一。 
    * “容器”。 输入 `socialtwitter`。
    存储帐户名称和容器名称结合使用，以便为 Blob 存储提供 URI，如下所示： 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![流分析作业的“新建输出”边栏选项卡](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. 单击“创建”。 

    Azure 创建存储帐户，并自动生成密钥。 

5. 关闭“输出”边栏选项卡。 


## <a name="start-the-job"></a>启动作业

指定作业输入、查询和输出。 已准备好启动流分析作业。

1. 请确保 TwitterWpfClient 应用程序正在运行。 

2. 在“作业”边栏选项卡中，单击“启动”。

    ![启动流分析作业](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. 在“启动作业”边栏选项卡中，为“作业输出开始时间”选择“现在”，然后单击“开始”。 

    ![流分析作业的“启动作业”边栏选项卡](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    作业启动后，Azure 会发出通知，并且“作业”边栏选项卡中的状态显示为“正在运行”。

    ![作业正在运行](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>查看观点分析的输出

作业已经开始运行，并开始处理实时 Twitter 流后，即可查看情绪分析的输出。

可使用 [Azure 存储资源管理器](https://storageexplorer.com/)或 [Azure 资源管理器](http://www.cerebrata.com/products/azure-explorer/introduction)之类的工具实时查看作业输出。 在这里，可以使用 [Power BI](https://powerbi.com/) 扩展应用程序以包括自定义仪表板，如以下屏幕截图所示：

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>创建另一个查询以标识热门话题

可用于了解 Twitter 情绪的另一个查询是基于[滑动窗口](https://msdn.microsoft.com/library/azure/dn835051.aspx)。 为了确定热门话题，需查找指定时间内超出某一提及次数阈值的主题。

对于本教程，需检查过去 5 秒内提及次数超过 20 次的主题。

1. 在“作业”边栏选项卡中，单击“停止”以停止作业。 

2. 在“作业拓扑”部分，单击“查询”框。 

3. 将查询更改为以下内容：

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. 单击“ **保存**”。

5. 请确保 TwitterWpfClient 应用程序正在运行。 

6. 单击“启动”重启使用新查询的作业。


## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
