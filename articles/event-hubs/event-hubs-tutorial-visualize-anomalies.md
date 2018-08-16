---
title: 将发送到 Azure 事件中心的实时事件中的数据异常可视化 | Microsoft Docs
description: 教程 - 将发送到 Microsoft Azure 事件中心的实时事件中的数据异常可视化
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.author: shvija
ms.date: 08/08/2018
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: 04a9a3b3df44814d680f01595d70ced08a946591
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004107"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>教程：将发送到 Azure 事件中心的实时事件中的数据异常可视化

借助 Azure 事件中心，可以使用 Azure 流分析检查传入的数据和提取异常，然后在 Power BI 中将这些异常可视化。 假设有数千个设备在不断地向事件中心发送实时数据，每秒发送的事件数累积达到数百万个。 如何在这么多的数据中检查异常或错误？ 例如，如果设备正在发送信用卡交易，而你需要在 5 秒时间间隔内捕获多个国家/地区任意位置发生的多个交易，那么结果会是怎样？ 如果某人窃取了信用卡，然后在全球不同的地方同时使用这些信用卡购物，则可能需要捕获这些异常。 

本教程将模拟此示例。 我们将运行一个可以创建信用卡交易并将其发送到事件中心的应用程序。 再使用 Azure 流分析实时读取数据流，将无效交易与无效交易区分开来，然后使用 Power BI 直观识别标记为无效的交易。

本教程介绍如何执行以下操作：
> [!div class="checklist"]
> * 创建事件中心命名空间
> * 创建事件中心
> * 运行发送信用卡交易的应用
> * 配置流分析作业以处理这些交易
> * 配置 Power BI 可视化以显示结果

需要一个 Azure 订阅才能完成此教程。 如果没有订阅，请在开始之前[创建一个免费帐户][]。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

- 安装 [Visual Studio](https://www.visualstudio.com/)。 
- 需要使用一个 Power BI 帐户来分析流分析作业的输出。 可以[免费试用 Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)。

## <a name="set-up-resources"></a>设置资源

在本教程中，需要一个事件中心命名空间和一个事件中心。 可以使用 Azure CLI 或 Azure PowerShell 创建这些资源。 为所有资源使用相同的资源组和位置。 在本教程结束后，可以通过删除资源组一次性删除所有资源。

以下部分介绍如何执行上述步骤。 遵照适用于 CLI 或 PowerShell 的说明执行以下步骤：

1. 创建[资源组](../azure-resource-manager/resource-group-overview.md)。 

2. 创建事件中心命名空间。 

3. 创建事件中心。

> [!NOTE]
> 本本教程稍后需要用到每个脚本中设置的变量。 这些变量包括资源组名称 ($resourceGroup)、事件中心命名空间 (**$eventHubNamespace**) 和事件中心名称 (**$eventHubName**)。 本文稍后会使用美元符号 ($) 前缀来引用这些变量，让你知道脚本中已设置这些变量。

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>使用 Azure CLI 设置资源

复制此脚本并将其粘贴到 Cloud Shell。 在已登录的情况下，该脚本会逐行运行。

必须全局唯一的变量已使用 `$RANDOM` 串联起来。 运行脚本时，如果已设置相应的变量，则会生成一个随机数字字符串，并将其串联到固定字符串的末尾，使其保持唯一。

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>使用 Azure PowerShell 设置资源

复制此脚本并将其粘贴到 Cloud Shell。 在已登录的情况下，该脚本会逐行运行。

必须全局唯一的变量已使用 `$(Get-Random)` 串联起来。 运行脚本时，如果已设置相应的变量，则会生成一个随机数字字符串，并将其串联到固定字符串的末尾，使其保持唯一。

```azurepowershell-interactive
# Log in to Azure account.
Login-AzureRMAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzureRmEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzureRmEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzureRmEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>运行应用以生成测试事件数据

[GitHub 上的事件中心示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)包含一个可以生成测试数据的[异常检测程序应用](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/AnomalyDetector)。 该应用通过以下方式模拟信用卡的使用：将信用卡交易写入事件中心，并偶尔在多个位置写入同一张信用卡发生的多笔交易，以便将这些交易标记为异常。 若要运行此应用，请遵循以下步骤： 

1. 从 GitHub 下载 [Azure 事件中心示例](https://github.com/Azure/azure-event-hubs/archive/master.zip)，并在本地将其解压缩。

2. 转到文件夹 \azure-event-hubs-master\samples\DotNet\AnomalyDetector\，双击 AnomalyDetector.sln 在 Visual Studio 中打开解决方案。 

3. 打开 Program.cs，将 **Event Hubs connection string** 替换为运行脚本时保存的连接字符串。 

4. 将 **Event Hub name** 替换为事件中心名称。 单击 F5 运行应用程序。 该应用程序会开始向事件中心发送事件，直到发送了 1000 个事件为止。 在某些情况下，该应用需保持运行状态才能让我们检索数据。 以下说明中根据需要指明了此类情况。

## <a name="set-up-azure-stream-analytics"></a>设置 Azure 流分析

现在，可将数据流式传输到事件中心。 若要在 Power BI 可视化中使用该数据，请先设置流分析作业，以检索稍后要馈送到 Power BI 可视化中的数据。

### <a name="create-the-stream-analytics-job"></a>创建流分析作业

1. 在 Azure 门户中，单击“创建资源”。 在搜索框中键入“流分析”，并按 **Enter**。 选择“流分析作业”。 在流分析作业窗格中单击“创建”。 

2. 为作业输入以下信息：

   **作业名称**：使用 **contosoEHjob**。 此字段是作业的名称，必须全局唯一。

   **订阅**：选择自己的订阅。

   **资源组**：使用事件中心所用的同一资源组 (**ContosoResourcesEH**)。

   **位置**：使用设置脚本中所用的相同位置（“美国西部”）。

   ![显示如何创建新的 Azure 流分析作业的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    在剩余字段中使用默认值。 单击“创建”。 

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

如果不是在门户上的“流分析作业”窗格中操作，在门户中单击“资源组”，然后选择自己的资源组 (**ContosoResourcesEH**)，即可返回到自己的流分析作业。 此操作会显示组中的所有资源，然后可以选择自己的流分析作业。 

流分析作业的输入是来自事件中心的信用卡交易。

> [!NOTE]
> 以美元符号 ($) 开头的变量的值已在前面部分所述的启动脚本中设置。 在此处指定这些字段时，必须使用相同的值，即事件中心命名空间和事件中心名称。

1. 在“作业拓扑”下，单击“输入”。

2. 在“输入”窗格中，单击“添加流输入”并选择“事件中心”。 在出现的屏幕上填写以下字段：

   **输入别名**：使用 **contosoinputs**。 此字段是定义数据查询时使用的输入流的名称。

   **订阅**：选择自己的订阅。

   **事件中心命名空间**：选择事件中心命名空间 ($**eventHubNamespace**)。 

   **事件中心名称**：单击“使用现有项”并选择事件中心 ($**eventHubName**)。

   **事件中心策略名称**：选择“RootManageSharedAccessKey”。

   **事件中心使用者组**：将此字段留空以使用默认的使用者组。

   在剩余字段中使用默认值。

   ![显示如何将输入流添加到流分析作业的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. 单击“保存”。

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下，单击“输出”。 此字段是定义数据查询时使用的输出流的名称。

2. 在“输出”窗格中，单击“添加”并选择“Power BI”。 在出现的屏幕上填写以下字段：

   **输出别名**：使用 **contosooutputs**。 此字段是输出的唯一别名。 

   **数据集名称**：使用 **contosoehdataset**。 此字段是要在 Power BI 中使用的数据集的名称。 

   **表名称**：使用 **contosoehtable**。 此字段是要在 Power BI 中使用的表的名称。 

   在剩余字段中使用默认值。

   ![显示如何为流分析作业设置输出的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. 单击“授权”，并登录到 Power BI 帐户。

4. 在剩余字段中使用默认值。

5. 单击“保存”。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

此查询用于检索最终要发送到 Power BI 可视化的数据。 它使用前面在设置作业时定义的 **contosoinputs** 和 **contosooutputs**。 此查询将检索被视为欺诈的信用卡交易，即，在五秒间隔内在不同的位置发生了多笔交易的同一张信用卡的交易。

1. 在“作业拓扑”下，单击“查询”。

2. 将查询替换为以下内容： 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. 单击“保存”。

### <a name="test-the-query-for-the-stream-analytics-job"></a>测试流分析作业的查询 

1. 运行异常检测程序应用，在设置并运行测试的同时将数据发送到事件中心。 

2. 在“查询”窗格中，单击 **contosoinputs** 输入旁边的点，然后选择“来自输入的示例数据”。

3. 指定需要 3 分钟的数据，然后单击“确定”。 请等到出现数据已采样的通知。

4. 单击“测试”，确保获取结果。 结果将显示在底部窗格中“结果”部分的查询右下方。

5. 关闭“查询”窗格。

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中，依次单击“启动”、“立即”、“启动”。 成功启动作业后，作业状态将从“已停止”更改为“正在运行”。

## <a name="set-up-the-power-bi-visualizations"></a>设置 Power BI 可视化效果

1. 运行异常检测程序应用，在设置 Power BI 可视化效果的同时将数据发送到事件中心。 可能需要多次运行该应用，因为它每次运行只会生成 1000 笔交易。

2. 登录到 [Power BI](https://powerbi.microsoft.com/) 帐户。

3. 转到“我的工作区”。

4. 单击“数据集”。

   应会看到在为流分析作业创建输出时指定的数据集 (**contosoehdataset**)。 数据集首次显示可能需要花费 5-10 分钟。

5. 依次单击“仪表板”、“创建”，然后选择“仪表板”。

   ![“仪表板”和“创建”按钮的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. 指定仪表板的名称，并单击“创建”。 使用“信用卡异常”。

   ![指定仪表板名称的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. 在“仪表板”页上单击“添加磁贴”，在“实时数据”部分选择“自定义流数据”，然后单击“下一步”。

   ![指定磁贴源的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. 选择数据集 (**contosoehdataset**) 并单击“下一步”。

   ![指定数据集的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. 选择“卡”作为可视化效果类型。 在“字段”下单击“添加值”，并选择“fraudulentuses”。

   ![指定可视化效果类型和字段的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   单击“下一步”。

10. 将标题设置为“欺诈性使用”，将副标题设置为“过去几分钟的总计”。 单击“应用”。 随即会将该磁贴保存到仪表板。

    ![指定仪表板磁贴标题和副标题的屏幕截图。](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

11. 添加另一种可视化效果。 再次重复前几个步骤：

   * 单击“添加磁贴”。
   * 选择“自定义流数据”。 
   * 单击“下一步”。
   * 选择数据集并单击“下一步”。 

12. 在“可视化效果类型”下面，选择“折线图”。

13. 在“轴”下面，单击“添加值”并选择“windowend”。 

14. 在“值”下面，单击“添加值”并选择“fraudulentuses”。

15. 在“要显示的时间窗口”下面，选择“过去 5 分钟”。 单击“下一步”。

16. 指定“显示一段时间内的欺诈性使用”作为标题，将磁贴的副标题保留空白，然后单击“应用”。 随后会返回仪表板。

17. 再次运行异常检测程序应用，将一些数据发送到事件中心。 在应用分析数据时，“欺诈性使用”磁贴会发生变化，折线图会显示数据。 

    ![显示 Power BI 结果的屏幕截图](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>清理资源

如果想要删除已创建的所有资源，请删除 Power BI 可视化效果数据，然后删除资源组。 删除资源组会删除其包含的所有资源。 在本例中，会删除事件中心、事件中心命名空间、流分析作业和资源组本身。 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>清理 Power BI 可视化效果中的资源

登录到 Power BI 帐户。 转到“我的工作区”。 在仪表板名称所在的行中，单击垃圾桶图标。 接下来，转到“数据集”并单击垃圾桶图标以删除相应的数据集 (**contosoehdataset**)。

### <a name="clean-up-resources-using-azure-cli"></a>使用 Azure CLI 清理资源

若要删除资源组，请使用 [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清理资源

若要删除资源组，请使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：
> [!div class="checklist"]
> * 创建事件中心命名空间
> * 创建事件中心
> * 运行可模拟事件并将其发送到事件中心的应用
> * 配置流分析作业以处理发送到中心的事件
> * 配置 Power BI 可视化以显示结果

请继续学习下一篇文章，以详细了解 Azure 事件中心。

> [!div class="nextstepaction"]
> [在 .NET Standard 中开始将消息发送到 Azure 事件中心](event-hubs-dotnet-standard-getstarted-send.md)

[创建一个免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
