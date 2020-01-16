---
title: 通过 Azure IoT 中心设置和使用指标和诊断日志
description: 了解如何通过 Azure IoT 中心设置和使用指标和诊断日志。 这将提供数据以进行分析，从而帮助诊断中心可能存在的问题。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: e0094add11755ecb0c303adf874abe5a4a8f5811
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746201"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>教程：通过 IoT 中心设置和使用指标和诊断日志

如果有在生产环境中运行的 IoT 中心解决方案，则需设置一些指标并启用诊断日志。 然后，在出现问题的情况下，就可以查看数据，以便诊断问题并进行更快速的修复。 本文介绍如何启用诊断日志，以及如何检查其中记录的错误。 此外还介绍如何设置一些监视指标，以及如何设置在指标达到特定阈值时就会触发的警报。 例如，可以在发送的遥测消息数超出特定限制时，或者所使用的消息数接近 IoT 中心每天允许的消息配额时，让系统给你发送电子邮件。 

以加油站为例，油泵是可以与 IoT 中心通信的 IoT 设备。 在验证信用卡之后，系统会将最终的交易写入数据存储。 在 IoT 设备停止连接到中心并停止发送消息的情况下，如果看不到正在进行的操作，则更加难以解决问题。

本教程使用 [IoT 中心路由](tutorial-routing.md)中的 Azure 示例，将消息发送到 IoT 中心。

将在本教程中执行以下任务：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 IoT 中心、模拟设备和存储帐户。  
> * 启用诊断日志。
> * 启用指标。
> * 针对这些指标设置警报。 
> * 下载并运行一个应用，该应用模拟 IoT 设备将消息发送到中心的情形。 
> * 运行该应用，直至警报开始触发。 
> * 查看指标结果并检查诊断日志。 

## <a name="prerequisites"></a>必备条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 安装 [Visual Studio](https://www.visualstudio.com/)。 

- 一个能够接收邮件的电子邮件帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>设置资源

若要完成本教程，需要 IoT 中心、存储帐户和模拟 IoT 设备。 这些资源可以通过 Azure CLI 或 Azure PowerShell 创建。 为所有资源使用相同的资源组和位置。 在本教程结束后，可以通过删除资源组一次性删除所有资源。

下面是必需步骤。

1. 创建[资源组](../azure-resource-manager/management/overview.md)。 

2. 创建 IoT 中心。

3. 使用 Standard_LRS 副本创建标准 V1 存储帐户。

4. 为发送消息到中心的模拟设备创建设备标识。 保存测试阶段的密钥。

### <a name="set-up-resources-using-azure-cli"></a>使用 Azure CLI 设置资源

复制此脚本并将其粘贴到 Cloud Shell。 在已登录的情况下，该脚本会逐行运行。 新资源在资源组 ContosoResources 中创建。

必须全局唯一的变量已使用 `$RANDOM` 串联起来。 运行脚本时，如果已设置相应的变量，则会生成一个随机数字字符串，并将其串联到固定字符串的末尾，使其保持唯一。

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>创建设备标识时，可能会出现以下错误：  找不到 IoT 中心 ContosoTestHub 的策略 iothubowner 的密钥。 若要修正此错误，请更新 Azure CLI IoT 扩展，然后再次运行脚本中的最后两个命令。 
>
>下面是用于更新扩展的命令。 请在 Cloud Shell 实例中运行该命令。
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>启用诊断日志 

创建新的 IoT 中心时，会默认禁用[诊断日志](../azure-monitor/platform/platform-logs-overview.md)。 在此部分，请为中心启用诊断日志。

1. 首先，如果还没有进入门户的中心，请单击“资源组”，然后单击资源组  Contoso-Resources。 从所显示的资源列表中选择中心。 

2. 查找 IoT 中心边栏选项卡中的“监视”部分。  单击“诊断设置”。  

   ![屏幕截图，显示 IoT 中心边栏选项卡的诊断设置部分。](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. 确保订阅和资源组正确。 在“资源类型”下取消选中“全选”，然后查找并勾选 IoT 中心”。    （这样会再次将复选标记置于“全选”旁边，忽略它即可。）  在“资源”下，选择中心名称。  屏幕应该如下图所示： 

   ![屏幕截图，显示 IoT 中心边栏选项卡的诊断设置部分。](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. 现在单击“启用诊断”。  此时会显示“诊断设置”窗格。 将诊断日志设置的名称指定为“diags-hub”。

5. 选中“存档到存储帐户”  。 

   ![屏幕截图，显示如何将诊断设置为存档到存储帐户。](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    单击“配置”，此时会看到“选择存储帐户”屏幕，选择右侧的帐户   (*contosostoragemon*)，然后单击“确定”，返回到“诊断设置”窗格。  

   ![屏幕截图，显示如何将诊断日志设置为存档到存储帐户。](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. 在“日志”下，勾选“连接”和“设备遥测”，然后将“保留期(天)”设置为每项 7 天。     “诊断设置”屏幕现在应如下图所示：

   ![屏幕截图，显示最终的诊断日志设置。](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. 单击“保存”  保存这些设置。 关闭“诊断设置”窗格。

随后在查看诊断日志时，就可以看到设备的连接和断开连接日志记录。 

## <a name="set-up-metrics"></a>设置指标 

现在设置一些将消息发送到中心时需要监视的指标。 

1. 在 IoT 中心的设置窗格中，单击“监视”部分的“指标”选项。  

2. 在屏幕顶部，单击“过去 24 小时(自动)”  。 在显示的下拉列表中，选择“过去 4 小时”作为“时间范围”，并将“时间粒度”设置为“1 分钟”（本地时间）。     单击“应用”  ，保存这些设置。 

   ![屏幕截图，显示指标的时间设置。](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. 默认情况下有一个指标条目。 让资源组保留默认值，指标命名空间也保留默认值。 在“指标”下拉列表中，选择“发送的遥测消息数”   。 将“聚合”设置为“总和”。  

   ![屏幕截图，显示如何为发送的遥测消息添加一个指标。](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. 现在单击“添加指标”，向图表添加另一个指标。  选择资源组 (**ContosoTestHub**)。 在“指标”下，选择“已使用的消息总数”。   对于“聚合”，请选择“平均”。   

   现在，屏幕会显示针对“发送的遥测消息数”的最小化指标，以及针对“已使用的消息总数”的新指标。  

   ![屏幕截图，显示如何为发送的遥测消息添加一个指标。](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   单击“固定到仪表板”  。 这样就会将它固定到 Azure 门户的仪表板上，方便你再次访问它。 如果不将它固定到仪表板，则不会保留设置。

## <a name="set-up-alerts"></a>设置警报

转到门户中的中心。 单击“资源组”，选择“ContosoResources”，然后选择 IoT 中心“ContosoTestHub”。    

IoT 中心尚未迁移到 [Azure Monitor 中的指标](/azure/azure-monitor/platform/data-collection#metrics)，你必须使用[经典警报](/azure/azure-monitor/platform/alerts-classic.overview)。

1. 在“监视”下，单击“警报”。此时会显示主警报屏幕。   

   ![屏幕截图，显示如何查找经典警报。](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. 若要从此处转到经典警报，请单击“查看经典警报”。  

    ![屏幕截图，显示经典警报屏幕。](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    填写字段： 

    **订阅**：将此字段设置为当前订阅。

    **源**：将此字段设置为“指标”。 

    **资源组**：将此字段设置为当前资源组“ContosoResources”。  

    **资源类型**：将此字段设置为“IoT 中心”。 

    **资源**：选择 IoT 中心“ContosoTestHub”。 

3. 单击“添加指标警报(经典)”，设置新警报。 

    填写字段：

    **Name**：为警报规则提供名称，例如 *telemetry-messages*。

    **说明**：提供警报说明，例如“发送了 1000 个遥测消息时的警报”。  

    **源**：将此项设置为“指标”。 

    “订阅”、“资源组”和“资源”应该设置为在“查看经典警报”屏幕上选择的值。     

    将“指标”设置为“发送的遥测消息数”。  

    ![屏幕截图，显示如何为发送的遥测消息设置经典警报。](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. 在图表后设置以下字段：

   **条件**：设置为“大于”。 

   **阈值**：设置为 1000。

   **时间段**：设置为“过去 5 分钟”。 

   **通知电子邮件收件人**：将电子邮件地址置于此处。 

   ![屏幕截图，显示警报屏幕的下半部。](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   单击“确定”，保存警报。  

5. 现在针对“已使用的消息总数”设置另一警报。  当使用的消息数接近 IoT 中心的配额时，如果需要系统发送警报来通知你该中心很快就会开始拒绝消息，则可使用此指标。

   在“查看经典警报”屏幕上，单击“添加指标警报(经典)”，然后在“添加规则”窗格上填充以下字段。   

   **Name**：为警报规则提供名称，例如 *number-of-messages-used*。

   **说明**：提供警报说明，例如“接近配额时的警报”。 

   **源**：将此字段设置为“指标”。 

    “订阅”、“资源组”和“资源”应该设置为在“查看经典警报”屏幕上选择的值。     

    将“指标”设置为“已使用的消息总数”。  

6. 在图表下填充以下字段：

   **条件**：设置为“大于”。 

   **阈值**：设置为 1000。

   **时间段**：将此字段设置为“过去 5 分钟”。  

   **通知电子邮件收件人**：将电子邮件地址置于此处。 

   单击“确定”  ，保存规则。 

5. 现在会在经典警报窗格中看到两个警报： 

   ![屏幕截图，显示包含新警报规则的经典警报屏幕。](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. 关闭警报窗格。 
    
    使用这些设置时，如果发送的消息数大于 400 且已使用的消息总数超出 NUMBER，则会出现警报。

## <a name="run-simulated-device-app"></a>运行模拟设备应用

之前的脚本设置部分中，已设置了一个使用 IoT 设备进行模拟的设备。 本部分将下载一个 .NET 控制台应用，用于模拟向 IoT 中心发送设备到云消息的设备。  

下载 [IoT 设备模拟](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)的解决方案。 可通过此链接下载包含多个应用程序的存储库；要查找的解决方案位于 iot-hub/Tutorials/Routing/ 中。

双击解决方案文件 (SimulatedDevice.sln)，在 Visual Studio 中打开代码，然后打开 Program.cs。 使用 IoT 中心主机名代替 `{iot hub hostname}`。 IoT 中心主机名的格式为“{iot-hub-name}.azure-devices.net”  。 本教程的中心主机名为“ContosoTestHub.azure-devices.net”  。 接下来，使用之前设置模拟设备时保存的设备密钥代替 `{device key}`。 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>运行和测试 

在 Program.cs 中将 `Task.Delay` 从 1000 更改为 10，这样就会将发送消息的间隔时间从 1 秒更改为 0.01 秒。 缩短此延迟会增加发送的消息数。

```csharp
await Task.Delay(10);
```

运行控制台应用程序。 稍等几分钟（10-15 分钟）。 可在应用程序的控制台屏幕上看到消息从模拟设备发送到中心。

### <a name="see-the-metrics-in-the-portal"></a>在门户中查看指标

从“仪表板”打开指标。 在时间粒度为“1 分钟”的情况下，将时间值更改为“过去 30 分钟”。   此时会显示发送的遥测消息数以及在图表上使用的总消息数，最新的数字位于图表底部。

   ![屏幕截图，显示指标。](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>查看警报

返回到警报。 单击“资源组”，选择“ContosoResources”，然后选择中心“ContosoTestHub”。    在为中心显示的属性页中，选择“警报”，然后选择“查看经典警报”。   

当发送的消息数超出限制时，你就会开始收到电子邮件警报。 若要查看是否有任何活动警报，请转到中心，然后选择“警报”。  此时会显示处于活动状态的警报，以及是否存在任何警告。 

   ![屏幕截图，显示警报已触发。](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

单击遥测消息的警报。 此时会显示指标结果以及包含结果的图表。 另外，发送给你的用于警告警报已触发的电子邮件如下图所示：

   ![电子邮件的屏幕截图，显示警报已触发。](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>查看诊断日志

设置诊断日志，以便将其导出到 Blob 存储。 转到资源组，选择存储帐户 *contosostoragemon*。 选择“Blob”，然后打开容器 *insights-logs-connections*。 向下钻取，直至抵达当前日期，选择最新的文件。 

   ![屏幕截图，显示如何向下钻取到存储容器来查看诊断日志。](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

单击“下载”  ，将其下载后打开。 此时会看到日志，描述设备在将消息发送到中心时进行的连接和断开连接操作。 下面是一个示例：

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>清理资源 

若要删除在本教程中创建的所有资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在此示例中，它会删除 IoT 中心、存储帐户和资源组本身。 如果已将指标固定到仪表板，则需手动删除这些指标，方法是：单击每个指标右上角的三个点，然后选择“删除”。 

若要删除资源组，请使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何执行以下任务，以便使用指标和诊断日志：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 IoT 中心、模拟设备和存储帐户。  
> * 启用诊断日志。 
> * 启用指标。
> * 针对这些指标设置警报。 
> * 下载并运行一个应用，该应用模拟 IoT 设备将消息发送到中心的情形。 
> * 运行该应用，直至警报开始触发。 
> * 查看指标结果并检查诊断日志。 

转到下一教程，了解如何管理 IoT 设备的状态。 

> [!div class="nextstepaction"]
> [从后端服务配置设备](tutorial-device-twins.md)