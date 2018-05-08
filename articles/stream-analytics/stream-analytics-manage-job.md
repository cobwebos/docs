---
title: 教程：使用 Azure 门户创建和管理流分析作业 | Microsoft Docs
description: 本教程以端到端方式演示了如何使用 Azure 流分析来分析电话呼叫流中的欺诈性呼叫。
services: stream-analytics
author: SnehaGunda
ms.author: sngun
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 524b15747a275c76fec6c529e4f00d0da1b41420
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>通过创建流分析作业来分析电话呼叫数据并在 Power BI 仪表板中将结果可视化

本教程介绍了如何使用 Azure 流分析来分析由客户端应用程序生成的示例性电话呼叫。 由客户端应用程序生成的电话呼叫数据包含一些欺诈性呼叫，我们将定义一个流分析作业来筛选此类呼叫。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 生成示例性的电话呼叫数据并将其发送到 Azure 事件中心  
> * 创建流分析作业   
> * 配置作业输入和输出  
> * 定义用于筛选欺诈性呼叫的查询  
> * 测试和启动作业  
> * 在 Power BI 中可视化结果 

## <a name="prerequisites"></a>先决条件

在开始之前，请确保具有以下各项：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。  
* 登录到 [Azure 门户](https://portal.azure.com/)。  
* 可以从 Microsoft 下载中心下载电话呼叫事件生成器应用 [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)，也可以从 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) 获取源代码。  

## <a name="create-an-azure-event-hub"></a>创建 Azure 事件中心 

你应该将数据发送到 Azure，然后流分析才能分析欺诈性呼叫数据流。 在本教程中，需使用 [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)将数据发送到 Azure。 在本教程中，请先创建事件中心，然后让事件生成器应用将呼叫数据发送至该事件中心。 请运行以下步骤来创建事件中心：

1. 登录到 Azure 门户。  
2. 选择“创建资源” > “物联网” > “事件中心”。  

   ![查找事件中心](media/stream-analytics-manage-job/find-eh.png)
3. 使用以下值填写“创建命名空间”窗格：  

   |**设置**  |建议的值 |**说明**  |
   |---------|---------|---------|
   |名称     | myEventHubNS        |  用于标识事件中心命名空间的唯一名称。       |
   |订阅     |   用户的订阅\<\>      |   选择要在其中创建事件中心的 Azure 订阅。      |
   |资源组     |   MyASADemoRG      |  选择“新建”，然后输入帐户的新资源组名称。       |
   |Location     |   美国西部 2      |    一个位置，可在其中部署事件中心命名空间。     |

4. 对其余设置使用默认选项，然后选择“创建”。  

   ![创建事件中心命名空间](media/stream-analytics-manage-job/create-ehns.png)

5. 当命名空间完成部署以后，转到“所有资源”，在 Azure 资源的列表中找到“myEventHubNS”，然后选择将其打开。  
6. 接下来选择“+事件中心” > “名称”，将事件中心命名为“MyEventHub”。 可使用其他名称。 对其余设置使用默认选项，然后选择“创建”，等待部署成功完成。

   ![创建事件中心](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>授予对事件中心的访问权限，并获取连接字符串

在应用程序可以将数据发送到 Azure 事件中心之前，事件中心必须具有允许适当访问的策略。 访问策略生成包含授权信息的连接字符串。

1. 导航到在上一步创建的**事件中心**（即“MyEventHub”），从事件中心窗格中选择“共享访问策略”，然后选择“+添加”。  
2. 将策略名称设置为“Mypolicy”，选择“管理”，然后选择“创建”。  

   ![创建事件中心共享访问策略](media/stream-analytics-manage-job/create-ehpolicy.png)

3. 在策略已经部署后，选择打开该策略，找到“连接字符串 - 主键”，然后选择连接字符串旁边的“复制”。  
4. 将连接字符串粘贴到文本编辑器中。 需要在下一部分使用此连接字符串。  

   连接字符串如下所示：

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   请注意，连接字符串包含多个用分号隔开的键值对：Endpoint、SharedAccessKeyName、SharedAccessKey 和 EntityPath。  

5. 删除连接字符串的 EntityPath 对（务必记得删除其前面的分号）。

## <a name="start-the-event-generator-application"></a>启动事件生成器应用程序

在启动 TelcoGenerator 应用之前，应该对其进行配置，以便将数据发送到此前创建的 Azure 事件中心。

1. 提取 [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) 文件的内容。  
2. 在所选文本编辑器中打开 `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` 文件（该文件夹中有多个 .config 文件，因此请确保打开正确的文件。）  

3. 使用以下详细信息更新配置文件中的 <appSettings> 元素：

   * 将 EventHubName 键的值设置为连接字符串中 EntityPath 的值。  
   * 将 Microsoft.ServiceBus.ConnectionString 键的值设置为没有 EntityPath 值的连接字符串（在上一部分的步骤 5 中获得的值）。

4. 保存文件。  
5. 接下来打开一个命令窗口，转到在其中解压缩了 TelcoGenerator 应用程序的文件夹，然后输入以下命令：

   ```
   telcodatagen.exe 1000 .2 2
   ```

   此命令采用以下参数：
   * **每小时的呼叫数据记录数**。  
   * **欺诈概率 (%)** - 应用模拟欺诈呼叫的频率。 值 .2 表示大约有 20% 的通话记录似乎是欺诈性的。  
   * **持续时间（小时）**- 应用应运行的小时数。 还可以通过在命令行终止此过程 (Ctrl+C) 来随时停止该应用。

   几秒钟后，当应用将电话通话记录发送到事件中心时，应用将开始在屏幕上显示通话记录。 这些电话呼叫数据包含以下字段：

   |**记录**  |**定义**  |
   |---------|---------|
   |CallrecTime    |  呼叫开始时间的时间戳。       |
   |SwitchNum     |  用于连接呼叫的电话交换机。 在此示例中，交换机是表示来源国家/地区（美国、中国、英国、德国或澳大利亚）的字符串。       |
   |CallingNum     |  呼叫方的电话号码。       |
   |CallingIMSI     |  国际移动用户标识 (IMSI)。 它是呼叫方的唯一标识符。       |
   |CalledNum     |   呼叫接收人的电话号码。      |
   |CalledIMSI     |  国际移动用户标识 (IMSI)。 它是呼叫接收人的唯一标识符。       |

## <a name="create-a-stream-analytics-job"></a>创建流分析作业 

有了呼叫事件流以后，即可创建流分析作业，以便从事件中心读取数据。

1. 若要创建流分析作业，请导航到 Azure 门户  

2. 选择“创建资源” > “物联网” > “流分析作业”。  

3. 使用以下值填写“新建流分析作业”窗格：  

   |**设置**  |建议的值  |**说明**  |
   |---------|---------|---------|
   |作业名称     |  ASATutorial       |   用于标识事件中心命名空间的唯一名称。      |
   |订阅    |  用户的订阅\<\>   |   选择要在其中创建作业的 Azure 订阅。       |
   |资源组   |   MyASADemoRG      |   选择“使用现有”，然后输入帐户的新资源组名称。      |
   |Location   |    美国西部 2     |      一个位置，可在其中部署作业。 为获得最佳性能，建议将作业和事件中心放置在同一区域，这样在不同区域之间传输数据时就不需要付费。      |
   |宿主环境    | 云        |     流分析作业可以部署到云或边缘设备。 可以通过“Cloud”部署到 Azure Cloud，通过“Edge”部署到 IoT Edge 设备。    |
   |流式处理单位     |    1       |      流单元表示执行作业所需的计算资源。 默认情况下，此值设置为 1。 若要了解如何缩放流单元，请参阅[了解和调整流单元](stream-analytics-streaming-unit-consumption.md)一文。      |

   ![创建作业](media/stream-analytics-manage-job/create-a-job.png)   

4. 对其余设置使用默认选项，然后选择“创建”，等待部署成功完成。

## <a name="configure-job-input"></a>配置作业输入

下一步是为用于读取数据的作业定义输入源。 在本教程中，将使用在上一部分创建的事件中心作为输入。 请通过运行以下步骤来配置作业输入：

1. 从 Azure 门户打开“所有资源”窗格，找到 ASATutorial 流分析作业。  

2. 在“流分析作业”窗格的“作业拓扑”部分，选择“输入”选项。  

3. 选择“+添加流输入”（引用输入是指静态查找数据，本教程中不需使用该数据），接着选择“事件中心”，然后使用以下值填写窗格：  

   |**设置**  |建议的值  |**说明**  |
   |---------|---------|---------|
   |输入别名     |  CallStream       |  提供用于标识输入的友好名称。 输入别名只能包含字母数字字符、连字符和下划线，而且长度必须介于 3 到 63 个字符之间。       |
   |订阅    |   用户的订阅\<\>      |   选择在其中创建了事件中心的 Azure 订阅。 事件中心可以位于流分析作业所在的订阅中，也可以位于另一订阅中。       |
   |事件中心命名空间    |  MyEventHubNS       |  选择在上一部分创建的事件中心命名空间。 当前订阅中所有可用的事件中心命名空间均列在下拉列表中。       |
   |事件中心名称    |   MyEventHub      |  选择在上一部分创建的事件中心。 当前订阅中所有可用的事件中心均列在下拉列表中。       |
   |事件中心策略名称   |  Mypolicy       |  选择在上一部分创建的事件中心共享访问策略。 当前订阅中所有可用的事件中心策略均列在下拉列表中。       |

   ![配置输入](media/stream-analytics-manage-job/configure-input.png) 

4. 对其余设置使用默认选项，然后选择“保存”，等待部署成功完成。

## <a name="configure-job-output"></a>配置作业输出 

最后一步是定义作业的输出接收器，以便在其中写入转换后的数据。 在本教程中，需将结果输出到 Power BI，然后将数据可视化。 请通过运行以下步骤来配置作业输出：

1. 从 Azure 门户打开“所有资源”窗格，然后打开 ASATutorial 流分析作业。  

2. 在“流分析作业”窗格的“作业拓扑”部分，选择“输出”选项。  

3. 选择“+添加” > “Power BI”，使用以下详细信息填充窗体（可以提供友好名称来标识“输出别名”、“数据集名称”和“表名”，如表中所示），然后选择“授权”：  

   |**设置**  |建议的值  |
   |---------|---------|---------|
   |输出别名  |  MyPBIoutput  |
   |数据集名称  |   ASAdataset  | 
   |表名称 |  ASATable  | 

   ![配置输出](media/stream-analytics-manage-job/configure-output.png)  

4. 选择“授权”以后，系统会打开一个弹出窗口，并要求你提供通过 Power BI 帐户进行身份验证所需的凭据。 授权成功以后，请单击“保存”以保存设置。 

## <a name="define-a-query-to-analyze-input-data"></a>定义用于分析输入数据的查询

设置用于读取传入数据流的流分析作业以后，下一步是创建一个可分析实时数据的转换。 请使用[流分析查询语言](https://msdn.microsoft.com/library/dn834998.aspx)来定义转换查询。 在本教程中，请定义一个可以在电话数据中检测欺诈性呼叫的查询。 

就此示例来说，我们将源自同一用户但来自不同位置且两次呼叫之间的间隔为五秒钟的呼叫视为欺诈性呼叫。 例如，同一用户不能合法地同时从美国和澳大利亚发起呼叫。 若要定义流分析作业的转换查询，请运行以下步骤：

1. 从 Azure 门户打开“所有资源”窗格，然后打开此前创建的 **ASATutorial** 流分析作业。  

2. 在“流分析作业”窗格的“作业拓扑”部分，选择“查询”选项。 弹出窗口会列出为作业配置的输入和输出，并允许通过创建查询来转换输入流。  

3. 接下来，请在编辑器中将现有查询替换为以下数据，此查询针对 5 秒时间间隔的调用数据执行自联接：

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   若要检查欺诈性呼叫，应根据 `CallRecTime` 值来自联接流数据。 然后，可以查找 `CallingIMSI` 值（始发号码）相同，但 `SwitchNum` 值（来源国家/地区）不同的呼叫记录。 当对流数据使用 JOIN 操作时，该联接必须对可以及时分隔匹配行的程度施加一定限制。 由于流数据是无限的，因此请使用 [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) 函数在联接的 ON 子句中指定关系的时间限制。 

   除 DATEDIFF 函数以外，此查询就像正常的 SQL 联接一样。 此查询中使用的 DATEDIFF 函数是特定于流分析的，必须显示在 `ON...BETWEEN` 子句中。  

4. **保存**查询。  

   ![定义查询](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>测试查询

可以在查询编辑器中测试查询，但需要提供测试查询所需的示例数据。 本演练将从进入事件中心的电话呼叫流中提取示例数据。 请运行以下步骤来测试查询：

1. 请确保 TelcoGenerator 应用正在运行，并且正生成电话呼叫记录。  

2. 在“查询”窗格中，选择 CallStream 输入旁边的点，然后选择“来自输入的示例数据”。 此时会打开一个窗格，可在其中指定要从输入流中读取的示例数据量。  

   ![示例输入数据](media/stream-analytics-manage-job/sample-input-data.png)  

3. 将“分钟”设置为 3，然后选择“确定”。 系统会从输入流中进行 3 分钟的数据采样，并会在示例数据准备就绪时发出通知。 可以在通知栏中查看采样的状态。 

   查询窗口打开时，示例数据会临时存储并可供使用。 如果关闭查询窗口，示例数据将被丢弃，必须创建一组新的示例数据。 或者，也可以从 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/telco.json) 获取包含示例数据的 .json 文件，然后上传该 .json 文件，将其用作 CallStream 输入的示例数据。  

4. 选择“测试”，对查询进行测试，此时会看到如以下屏幕截图所示的输出结果：  

   ![测试输出](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>启动作业并可视化输出

1. 若要启动作业，请导航到作业的“概览”窗格，然后选择“启动”。  

2. 选择“现在”作为作业输出启动时间，然后选择“启动”。 作业会在数分钟内启动。可以在通知栏中查看状态。  

3. 作业成功以后，请导航到 [Powerbi.com](https://powerbi.com/)，然后使用工作或学校帐户登录。 如果流分析作业查询输出了结果，则说明数据集已创建。 导航到“数据集”选项卡，可以看到名为“ASAdataset”的数据集。  

4. 在工作区中选择“+创建”。 创建新的仪表板，并将其命名为“欺诈性呼叫”。 需向此仪表板添加两个磁贴，一个磁贴用于查看给定实例的欺诈性呼叫计数，另一个磁贴用于进行折线图形式的可视化。  

5. 在窗口顶部，依次选择“添加磁贴”、“自定义流数据”、“下一步”、“ASAdataset”，然后选择“卡片”作为“可视化效果类型”，选择“现场”作为“fraudulentcalls”。 选择“下一步”，为磁贴输入一个名称，然后选择“应用”。  

   ![创建磁贴](media/stream-analytics-manage-job/create-tiles.png)

6. 使用以下选项再次执行步骤 4：
   * 转到“可视化效果类型”后，选择“折线图”。  
   * 添加轴，然后选择“windowend”。  
   * 添加值，然后选择“fraudulentcalls”。  
   * 对于“要显示的时间窗口”，请选择最近 10 分钟。  

7. 添加两个磁贴以后，仪表板如以下屏幕截图所示。 你会注意到，如果事件中心发送器应用程序和流分析应用程序正在运行，则当新数据到达时，PowerBI 仪表板会定期进行更新。  

   ![Power BI 结果](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>将 PowerBI 仪表板嵌入到 Web 应用程序中

在本教程的此部分，请使用 PowerBI 团队创建的示例性 [ASP.NET](http://asp.net/) Web 应用程序来嵌入仪表板。 有关如何嵌入仪表板的详细信息，请参阅[使用 Power BI 嵌入](https://docs.microsoft.com/power-bi/developer/embedding)一文。

在本教程中，我们将按照用户拥有数据应用程序的情况执行相关步骤。 若要设置应用程序，请访问 [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) Github 存储库，然后按照“用户拥有数据”部分的说明操作（请使用 **integrate-dashboard-web-app** 子部分的重定向 URL 和主页 URL）。 由于我们使用的是“仪表板”示例，因此请使用 [GitHub 存储库](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User Owns Data/integrate-dashboard-web-app)中的 integrate-dashboard-web-app 示例代码。
在浏览器中运行应用程序以后，请执行以下步骤，将此前创建的仪表板嵌入网页中：

1. 选择“登录到 Power BI”，以便授予应用程序访问 PowerBI 帐户中的仪表板的权限。  

2. 选择“获取仪表板”按钮，此时会在表中显示帐户的仪表板。 找到此前创建的仪表板的名称 (powerbi-embedded-dashboard)，然后复制相应的 **EmbedUrl**。  

3. 最后，将 **EmbedUrl** 粘贴到相应的文本字段中，然后选择“嵌入仪表板”。 现在可以查看嵌入 Web 应用程序中的同一仪表板了。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建简单的流分析作业、如何分析传入数据，以及如何在 Power BI 仪表板中展示结果。 若要详细了解流分析作业，请继续阅读下一教程：

> [!div class="nextstepaction"]
> [在流分析作业中运行 Azure Functions](stream-analytics-with-azure-functions.md)
