---
title: 使用 Azure 流分析进行实时 Twitter 情绪分析
description: 本文说明如何使用流分析进行实时 Twitter 情绪分析。 在实时仪表板上提供从事件生成到数据的分步指南。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240300"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure 流分析中的实时 Twitter 情绪分析

本文将实时 Twitter 事件引入 Azure 事件中心，教您如何构建社交媒体情绪分析解决方案。 编写 Azure 流分析查询以分析数据并存储结果以便以后使用，或创建[Power BI](https://powerbi.com/)仪表板以实时提供见解。

社交媒体分析工具可帮助组织了解热门话题。 热门话题是社交媒体上大量帖子的主题和态度。 情绪分析，也称为*意见挖掘*，使用社交媒体分析工具来确定对产品或想法的态度。 

实时 Twitter 趋势分析是分析工具的一个很好的例子，因为井号标签订阅模型使您能够收听特定的关键字（hashtag）并开发源的情绪分析。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>场景：实时社交媒体情绪分析

一家拥有新闻媒体网站的公司关注如何打造特色网站内容，使之迅速贴近其读者，以便增加相较于其竞争对手的竞争优势。 该公司通过对 Twitter 数据执行实时情绪分析，以便在贴近读者的主题上使用社交媒体分析。

若要标识 Twitter 上的实时热门话题，公司需要关于关键主题推文量及情绪的实时分析。

## <a name="prerequisites"></a>先决条件

本操作指南将使用连接到 Twitter 的客户端应用程序，并查找具有特定井号标签（可设置）的推文。 为了运行应用程序并使用 Azure 流分析来分析推文，必须具备以下项：

* 如果没有 Azure 订阅，请创建[一个免费帐户](https://azure.microsoft.com/free/)。

* [推特](https://twitter.com)帐户。

* TwitterClientCore应用程序，它读取推特饲料。 要获得此应用程序，请下载[TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)。

* 安装[.NET 核心 CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)版本 2.1.0。

## <a name="create-an-event-hub-for-streaming-input"></a>创建流式输入的事件中心

示例应用程序生成事件并将其推送到 Azure 事件中心。 Azure 事件中心是流分析的首选事件引入方法。 有关详细信息，请参阅 [Azure 事件中心文档](../event-hubs/event-hubs-what-is-event-hubs.md)。

### <a name="create-an-event-hub-namespace-and-event-hub"></a>创建事件中心命名空间和事件中心
在本节中，您将创建一个事件中心命名空间，并将事件中心添加到该命名空间。 事件中心命名空间用于逻辑分组相关的事件总线实例。 

1. 登录到 Azure 门户并选择 **"创建资源**"。 然后。 搜索**事件中心**并选择 **"创建**"。

2. 在 **"创建命名空间"** 页上，输入命名空间名称。 可以为命名空间使用任何名称，但名称必须对 URL 有效，并且它必须在整个 Azure 中是唯一的。 
    
3. 选择定价层和订阅，然后创建或选择资源组。 然后，选择一个位置，然后选择 **"创建**"。 
 
4. 命名空间完成部署后，导航到资源组，并在 Azure 资源列表中查找事件中心命名空间。 

5. 从新的命名空间中，选择**+&nbsp;事件中心**。 

6. 命名新的事件中心*社交推特-eh*。 可使用其他名称。 如果使用其他名称，请记下该名称，稍后会用到。 不需要为事件中心设置任何其他选项。
 
7. 选择 **“创建”**。

### <a name="grant-access-to-the-event-hub"></a>授予对事件中心的访问权限

在进程将数据发送到事件中心之前，事件中心需要允许访问的策略。 访问策略生成包含授权信息的连接字符串。

1.  在事件中心命名空间左侧的导航栏中，选择位于 **"实体"** 部分**的事件中心**。 然后，选择您刚刚创建的事件中心。

2.  在左侧的导航栏中，选择位于 **"设置"** 下的**共享访问策略**。

    >[!NOTE]
    >活动中心命名空间和事件中心下有共享访问策略选项。 请确保在事件中心上下文中工作，而不是整个事件中心命名空间。

3.  在访问策略页中，选择 **"添加**"。 然后输入**策略名称***的社交twitter访问*，然后选中 **"管理**"复选框。
 
4.  选择 **“创建”**。

5.  部署策略后，从共享访问策略列表中选择策略。

6.  查找标记为"**连接"字符串主键的**框，然后选择连接字符串旁边的复制按钮。
 
7.  将连接字符串粘贴到文本编辑器中。 进行一些小编辑后，在下一节中需要此连接字符串。

   连接字符串如下所示：
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   请注意，连接字符串包含多个键值对，用分号分隔：`Endpoint`、`SharedAccessKeyName`、`SharedAccessKey` 和 `EntityPath`。  

   > [!NOTE]
   > 为安全起见，已删除示例中的部分连接字符串。

## <a name="configure-and-start-the-twitter-client-application"></a>配置并启动 Twitter 客户端应用程序

客户端应用程序直接从 Twitter 获取推文事件。 为了做到这一点，需要为其赋予调用 Twitter 流式处理 API 的权限。 若要配置该权限，可在 Twitter 中创建一个应用程序，该应用会生成唯一凭据（例如 OAuth 标记）。 然后，可配置客户端应用程序，以便在其执行 API 调用时使用这些凭据。 

### <a name="create-a-twitter-application"></a>创建 Twitter 应用程序
如果还没有可用于本操作指南的 Twitter 应用程序，则可以创建一个。 必须已具有 Twitter 帐户。

> [!NOTE]
> Twitter 中创建应用程序以及获取密钥、机密和令牌的确切过程可能会改变。 如果这些说明与你在 Twitter 网站上看到的内容不符，请参阅 Twitter 开发人员文档。

1. 从 Web 浏览器转到[Twitter 开发人员](https://developer.twitter.com/en/apps)，创建开发人员帐户，然后选择"**创建应用**"。 可能会看到一条消息，指出你需要申请 Twitter 开发人员帐户。 请放心，在您的申请获得批准后，您应该会看到一封确认电子邮件。 批准开发人员帐户可能需要几天时间。

   ![Twitter 应用程序详细信息](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter 应用程序详细信息")

2. 在“创建应用程序”**** 页中提供新应用的详细信息，然后选择“创建 Twitter 应用程序”****。

   ![Twitter 应用程序详细信息](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter 应用程序详细信息")

3. 在应用程序页中选择“密钥和令牌”**** 选项卡，复制“使用者 API 密钥”**** 和“使用者 API 密钥”**** 的值。 此外，在“访问令牌和访问令牌机密”**** 下选择“创建”**** 以生成访问令牌。 复制“访问令牌”**** 和****“访问令牌机密”的值。

   保存 Twitter 应用程序的检索值。 以后需要这些值。

> [!NOTE]
> 输入 Twitter 应用程序的密钥和机密即可访问 Twitter 帐户。 与 Twitter 密码一样，请将此信息视为敏感信息对待。 例如，不要在你提供给其他人的应用程序中嵌入此信息。 

### <a name="configure-the-client-application"></a>配置客户端应用程序

我们创建了一个客户端应用程序，该应用程序使用 [Twitter 流式处理 API](https://dev.twitter.com/streaming/overview) 连接到 Twitter 数据，来收集有关特定主题集的推文事件。

运行应用程序之前，需要提供某些信息，例如 Twitter 密钥和事件中心连接字符串。

1. 请确保您已下载了在先决条件中列出的[TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)应用程序。

2. 使用文本编辑器打开*App.config*文件。 对`<appSettings>`元素进行以下更改：

   * 设置为`oauth_consumer_key`Twitter 使用者密钥（API 密钥）。 
   * 设置为`oauth_consumer_secret`Twitter 消费者密钥（API 密钥）。
   * 设置为`oauth_token`Twitter 访问令牌。
   * 设置为`oauth_token_secret`Twitter 访问令牌密钥。
   * 将 `EventHubNameConnectionString` 设置为连接字符串。
   * 设置为`EventHubName`事件中心名称（即实体路径的值）。

3. 打开命令行并导航到 TwitterClientCore 应用所在的目录。 使用 命令`dotnet build`生成项目。 然后使用 命令`dotnet run`运行应用。 应用会将推文发送到事件中心。

## <a name="create-a-stream-analytics-job"></a>创建流分析作业

既然推文事件正在从 Twitter 实时流式传输，那就可以设置一个流分析作业来实时分析这些事件。

1. 在 Azure 门户中，导航到资源组并选择 **" 添加**"。 然后搜索**流分析作业**并选择 **"创建**"。

2. 将作业命名为 `socialtwitter-sa-job`，然后指定订阅、资源组和位置。

    为获得最佳性能，最好将作业和事件中心放置在同一区域，这样就不需要在不同区域之间传输数据。

3. 选择 **“创建”**。 然后在部署完成后导航到作业。

## <a name="specify-the-job-input"></a>指定作业输入

1. 在"流分析"作业中，从**作业拓扑**下的左侧菜单中选择 **"输入**"。

2. **+选择&nbsp;"添加流输入** > **事件中心**"。 填写 **"新建输入**"表单，并填写以下信息：

   |**设置**  |**建议的值**  |**说明**  |
   |---------|---------|---------|
   |输入别名| *推特流* | 输入输入的别名。 |
   |订阅  | 用户的订阅\<\> |  选择要使用的 Azure 订阅。 |
   |事件中心命名空间 | *asa-twitter-事件hub* |
   |事件中心名称 | *社交推特-eh* | 选择 *"使用现有*"。 然后选择您创建的事件中心。|
   |事件压缩类型| GZip | 数据压缩类型。|

   保留剩余的默认值，然后选择 **"保存**"。

## <a name="specify-the-job-query"></a>指定作业查询

流分析支持简单的声明性查询模型，该模型用于描述转换。 若要了解有关语言的详细信息，请参阅 [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。 此指南可帮助您通过 Twitter 数据创作和测试多个查询。

若要比较各个主题的提及次数，可使用[翻转窗口](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics)每五秒按主题获取提及次数。

1. 在作业**概述**中，选择"编辑"框右上角附近的**查询**。 Azure 列出了为作业配置的输入和输出，并允许您创建查询以在输入流发送到输出时转换输入流。

2. 将查询编辑器中的查询更改为以下内容：

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. 消息中的事件数据应显示在查询下方的 **"输入预览"** 窗口中。 确保**视图**设置为**JSON**。 如果看不到任何数据，请确保数据生成器向事件中心发送事件，并且已选择**GZip**作为输入的压缩类型。

4. 选择 **"测试"查询**，并在查询下方的 **"测试结果"** 窗口中注意到结果。

5. 将代码编辑器中的查询更改为以下内容，然后选择 **"测试"查询**：

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. 此查询返回包含关键字*Azure*的所有推文。

## <a name="create-an-output-sink"></a>创建输出接收器

现已定义事件流、用于引入事件的事件中心输入，以及用于通过流执行转换的查询。 最后一步是为作业定义输出接收器。  

本操作指南会将聚合的推文事件从作业查询写入 Azure Blob 存储。  也可以将结果推送到 Azure SQL 数据库、Azure 表存储、事件中心或 Power BI，具体取决于应用程序需求。

## <a name="specify-the-job-output"></a>指定作业输出

1. 在左侧导航菜单的 **"作业拓扑"** 部分下，选择 **"输出**"。 

2. 在 **"输出"** 页中，单击**+&nbsp;"添加**和**Blob 存储/数据存储第 2 代**：

   * “输出别名”****：使用名称 `TwitterStream-Output`。 
   * **导入选项**：**选择从订阅中选择存储**。
   * **存储帐户**。 选择存储帐户。
   * **容器**。 选择 **"创建新"** 并`socialtwitter`输入 。
   
4. 选择“保存”。****   

## <a name="start-the-job"></a>启动作业

指定作业输入、查询和输出。 已准备好启动流分析作业。

1. 确保 TwitterClientCore 应用程序正在运行。 

2. 在作业概述中，选择 **"开始**"。

3. 在 **"开始作业"** 页上，**对于作业输出开始时间**，选择 **"现在"，** 然后选择"**开始**"。

## <a name="get-support"></a>获取支持
有关进一步帮助，请尝试我们的[Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [使用 Azure 流分析开始](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
