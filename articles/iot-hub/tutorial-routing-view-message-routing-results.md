---
title: 查看 Azure IoT 中心消息路由结果 (.NET) | Microsoft Docs
description: 查看 Azure IoT 中心消息路由结果
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 1417ecdaf6a85f491e1accfb9564e27d15e13445
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045827"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>教程：第 2 部分 - 查看路由的消息

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>消息路由规则

下面是消息路由规则；这些规则是在本教程的第 1 部分设置的，本文（第二部分）将演示其工作方式。

|值 |结果|
|------|------|
|级别=“storage” |写入到 Azure 存储。|
|级别=“critical” |写入服务总线队列。 逻辑应用从队列检索消息并使用 Office 365 通过电子邮件发送该消息。|
|default |使用 Power BI 显示此数据。|

现在，创建要将消息路由到的资源，运行某个应用以将消息发送到中心，然后查看路由的运作方式。

## <a name="create-a-logic-app"></a>创建逻辑应用  

服务总线队列将用于检索指定为关键的消息。 设置一个逻辑应用，用于监视服务总线队列，并在消息添加到队列时发送电子邮件。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“+ 创建资源”。 在搜索框中输入“逻辑应用”，并单击 Enter。 在显示的搜索结果中选择“逻辑应用”，然后选择“创建”进入“创建逻辑应用”窗格。 填充字段。

   **名称**：此字段是逻辑应用的名称。 本教程使用 ContosoLogicApp。

   **订阅**：选择 Azure 订阅。

   **资源组**：选择“使用现有项”并选择你的资源组。 本教程使用 ContosoResources。

   **位置**：使用你的位置。 本教程使用“美国西部”。

   **Log Analytics**：应关闭此开关。

   ![“创建逻辑应用”屏幕](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   选择“创建”。

2. 现在转到该逻辑应用。 转到“逻辑应用”的最简单方法是选择“资源组”，选择你的资源组（本教程使用 **ContosoResources**），然后从资源列表中选择“逻辑应用”。 随即将显示该逻辑应用设计器页面（可能需要向右滚动才可查看完整页面）。 在“逻辑应用设计器”页上向下滚动，直到出现带有“空白逻辑应用 +”字样的磁贴；选择该磁贴。 默认选项卡是“为你提供”。 如果此窗格为空，请选择“全部”以查看所有可用的连接器和触发器。

3. 从连接器列表中选择“服务总线”。

   ![连接器列表](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. 此时会显示触发器的列表。 选择“在队列中收到消息时(自动完成)/服务总线”。

   ![服务总线的触发器列表](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. 在下一屏幕上，填写“连接名称”。 本教程使用 ContosoConnection。

   ![为服务总线队列设置连接](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   选择“服务总线”命名空间。 本教程使用 ContosoSBNamespace。 选择该命名空间时，门户会查询该“服务总线”命名空间以检索密钥。 依次选择“RootManageSharedAccessKey”、“创建”。

   ![完成设置连接](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. 在下一步屏幕上，从下拉列表选择队列名称（本教程使用 contososbqueue）。 其余字段可使用默认值。

   ![队列选项](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. 现在，设置一个在队列接收到消息时发送电子邮件的操作。 在逻辑应用设计器中选择“+ 新建步骤”以添加步骤，然后选择“全部”以查看所有可用选项。 在“选择操作”窗格中，找到并选择“Office 365 Outlook”。 在触发器屏幕上，选择“发送电子邮件/Office 365 Outlook”。  

   ![Office365 选项](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. 登录到 Office 365 帐户并设置连接。 如果此操作超时，只需重试即可。 为电子邮件收件人指定电子邮件地址。 同时指定主题，并在正文键入想要让收件人看到的消息。 测试时，可填入自己的电子邮件地址作为收件人。

   选择“添加动态内容”，以显示消息中可包含的内容。 选择“内容”- 将包含电子邮件中的消息。

   ![逻辑应用的电子邮件选项](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. 选择“保存”。 然后关闭逻辑应用设计器。

## <a name="set-up-azure-stream-analytics"></a>设置 Azure 流分析

若要在 Power BI 可视化中查看数据，首先需要设置流分析作业来检索数据。 请记住，只会将“级别”为“常规”的消息发送到默认终结点，并由针对 Power BI 可视化的流分析作业检索。

### <a name="create-the-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “物联网” > “流分析作业”。

2. 为作业输入以下信息。

   **作业名称**：作业的名称。 该名称必须全局唯一。 本教程使用 contosoJob。

   **订阅**：本教程使用的 Azure 订阅。

   **资源组**：使用 IoT 中心所用的同一资源组。 本教程使用 ContosoResources。

   **位置**：使用设置脚本中所用的同一位置。 本教程使用“美国西部”。

   ![创建流分析作业](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. 选择“创建”来创建作业。 若要返回到该作业，请选择“资源组”。 本教程使用 ContosoResources。 选择资源组，然后在资源列表中选择流分析作业。

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

4. 在“作业拓扑”下选择“输入”。

5. 在“输入”窗格中，依次选择“添加流输入”、“IoT 中心”。 在出现的屏幕上，填写以下字段：

   **输入别名**：本教程使用 contosoinputs。

   **从订阅选择 IoT 中心**：选中此单选按钮选项。

   **订阅**：选择本教程使用的 Azure 订阅。

   **IoT 中心**：选择 IoT 中心。 本教程使用 ContosoTestHub。

   **终结点**：选择“消息传送”。 （如果选择操作监视，将获得有关 IoT 中心的遥测数据，而不是正在发送的数据。） 

   **共享访问策略名称**：选择“iothubowner”。 门户将填充共享访问策略密钥。

   **使用者组**：选择在本教程的步骤 1 中设置的使用者组。 本教程使用 contosoconsumers。
   
   其余字段接受默认值。 

   ![设置流分析作业的输入](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. 选择“保存”。

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下选择“输出”。

2. 在“输出”窗格中，依次选择“添加”、“Power BI”。 在出现的屏幕上，填写以下字段：

   **输出别名**：输出的唯一别名。 本教程使用 contosooutputs。 

   **数据集名称**：要在 Power BI 中使用的数据集的名称。 本教程使用 contosodataset。 

   **表名称**：要在 Power BI 中使用的表的名称。 本教程使用 contosotable。

   在剩余字段中使用默认值。

3. 选择“授权”并登录到 Power BI 帐户。 （这可能需要多次重试。）

   ![设置流分析作业的输出](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. 选择“保存”。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

1. 在“作业拓扑”下选择“查询”。

2. 将 `[YourInputAlias]` 替换为作业的输入别名。 本教程使用 contosoinputs。

3. 将 `[YourOutputAlias]` 替换为作业的输出别名。 本教程使用 contosooutputs。

   ![设置流分析作业的查询](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. 选择“保存”。

5. 关闭“查询”窗格。 随后将返回到资源组中资源的视图。 选择流分析作业。 本教程中将其称为 **contosoJob**。

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中，单击“启动” > “立即” > “启动”。 成功启动作业后，作业状态将从“已停止”更改为“正在运行”。

设置 Power BI 报表需要数据，因此将先创建设备并运行设备模拟应用程序再设置 Power BI。

## <a name="run-simulated-device-app"></a>运行模拟设备应用

在本教程的第 1 部分，我们已设置一个使用 IoT 设备进行模拟的设备。 假设在本教程的第 1 部分尚未下载相应的应用和资源，则在本部分，我们将下载 .NET 控制台应用，用于模拟设备向 IoT 中心发送设备到云的消息。

此应用程序针对每个不同的消息路由方法发送消息。 下载内容还带有一个文件夹，其中包含完整的 Azure 资源管理器模板和参数文件，以及 Azure CLI 和 PowerShell 脚本。

如果在本教程的步骤 1 中未从存储库下载这些文件，现在请继续从 [IoT 设备模拟](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)下载。 选择此链接可下载包含多个应用程序的存储库；要查找的解决方案是 iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln。 

双击解决方案文件 (IoT_SimulatedDevice.sln) 以在 Visual Studio 中打开代码，然后打开 Program.cs。 使用 IoT 中心主机名代替 `{iot hub hostname}`。 IoT 中心主机名的格式为“{iot-hub-name}.azure-devices.net”。 本教程的中心主机名为“ContosoTestHub.azure-devices.net”。 接下来，使用之前设置模拟设备时保存的设备密钥代替 `{device key}`。 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>运行和测试

运行控制台应用程序。 稍等几分钟。 可在应用程序的控制台屏幕上看到正在发送的消息。

此应用每隔一秒发送一条新的设备到云消息到 IoT 中心。 此消息包含一个 JSON 序列化对象，其中具有设备 ID、温度、湿度和消息级别（级别默认为 `normal`）。 它随机分配 `critical` 级别或 `storage` 级别，使消息路由到存储帐户或路由到服务总线队列（这会触发逻辑应用发送电子邮件）。 默认 (`normal`) 读取内容将在之后设置的 BI 报表中显示。

如果所有内容都正确设置，则此时应可以看见以下结果：

1. 开始收到关键消息相关的电子邮件。

   ![生成的电子邮件](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   此结果表示以下语句为 true。 

   * 到服务总线队列的路由运行正常。
   * 从服务总线队列检索消息的逻辑应用运行正常。
   * 连接到 Outlook 的逻辑应用连接器工作正常。 

2. 在 [Azure 门户](https://portal.azure.com)中选择“资源组”，然后选择你的资源组。 本教程使用 ContosoResources。 依次选择存储帐户、“Blob”、“容器”。 本教程使用 contosoresults。 现在应该可以看见一个文件夹，可继续深入查看目录，直到看见一个或多个文件。 打开其中某个文件；这些文件中包含路由到存储帐户的条目。 

   ![存储中的结果文件](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

此结果表示以下语句为 true。

   * 到存储帐户的路由运行正常。

现在，在应用程序仍在运行的情况下，设置 Power BI 可视化以显示来自默认路由的消息。

## <a name="set-up-the-power-bi-visualizations"></a>设置 Power BI 可视化效果

1. 登录到 [Power BI](https://powerbi.microsoft.com/) 帐户。

2. 转到“工作区”，然后选择为流分析作业创建输出时设置的工作区。 本教程使用“我的工作区”。 

3. 选择“数据集”。 如果没有任何数据集，请在几分钟后再次检查。

   此时会看到列出的数据集，该数据集是在为流分析作业创建输出时指定的。 本教程使用 contosodataset。 （数据集首次显示的过程可能需要 5-10 分钟。）

4. 在“操作”下，选择第一个图标以创建报表。

   ![Power BI 工作区，其中突出显示了“操作”和报表图标](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. 创建折线图，显示某段时间的实时温度。

   * 在报表创建页上，选择折线图图标以添加折线图。

     ![可视化效果和字段](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * 在“字段”窗格中展开一个表，该表是在为流分析作业创建输出时指定的。 本教程使用 contosotable。

   * 将 **EventEnqueuedUtcTime** 拖至“可视化效果”窗格中的“轴”。

   * 将“温度”拖至“值”。

   已创建一个折线图。 X 轴显示 UTC 时区的日期和时间。 Y 轴显示来自传感器的温度。

6. 创建另一个折线图，显示某段时间的实时湿度。 要设置第二个折线图，请执行上诉相同步骤，将“EventEnqueuedUtcTime”置于 x 轴，将“湿度”置于 y 轴。

   ![最终的 Power BI 报表，其中包含两个图表](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. 选择“保存”以保存报表。

现在应在两个图表上都能看到数据。 此结果表示以下语句为 true：

   * 到默认终结点的路由运行正常。
   * Azure 流分析作业的流式传输正常。
   * Power BI 可视化设置正确。

选择 Power BI 窗口顶部的“刷新”按钮可刷新图表，以查看最近的数据。 

## <a name="clean-up-resources"></a>清理资源 

若要删除在本教程的两个部分中创建的所有资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在这种情况下，它会删除 IoT 中心、服务总线命名空间和队列、逻辑应用、存储帐户和资源组本身。 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>清理 Power BI 可视化效果中的资源

登录到 [Power BI](https://powerbi.microsoft.com/) 帐户。 转到你的工作区。 本教程使用“我的工作区”。 若要删除 Power BI 可视化效果，请转到“数据集”并选择垃圾桶图标来删除相应的数据集。 本教程使用 contosodataset。 删除数据集时，报表也随之删除。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 清理资源

若要删除资源组，请使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 在本教程的开头，`$resourceGroup` 已设置为 **ContosoResources**。

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>使用 PowerShell 清理资源

若要删除资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令。 在本教程的开头，`$resourceGroup` 已设置为 **ContosoResources**。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

本教程的第 2 部分（即本文）已介绍如何通过执行以下任务，使用消息路由将 IoT 中心消息路由到不同的目标。  

**第 I 部分：创建资源并设置消息路由**
> [!div class="checklist"]
> * 创建资源 - IoT 中心、存储帐户、服务总线队列和模拟设备。
> * 在 IoT 中心为存储帐户和服务总线队列配置终结点和消息路由。

**第 II 部分：将消息发送到中心并查看路由的结果**
> [!div class="checklist"]
> * 创建一个逻辑应用，该应用将在消息添加到服务总线队列时触发，并发送电子邮件。
> * 下载并运行应用，该应用模拟 IoT 设备将消息发送到中心，以获得不同的路由选择。
> * 为发送至默认终结点的数据创建 Power BI 可视化。
> * 查看结果...
> * ...在服务总线队列和电子邮件中。
> * ...在存储帐户中。
> * ...在 Power BI 可视化中。

转到下一教程，了解如何管理 IoT 设备的状态。 

> [!div class="nextstepaction"]
> [通过 IoT 中心设置和使用指标和诊断](tutorial-use-metrics-and-diags.md)
