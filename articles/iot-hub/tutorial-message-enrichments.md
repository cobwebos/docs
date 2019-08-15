---
title: 教程 - 使用 Azure IoT 中心消息扩充
description: 介绍如何使用 Azure IoT 中心消息扩充的教程
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 2c115bf0ad21e905e998692fbbc175f5aa52b86d
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014246"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>教程：使用 Azure IoT 中心消息扩充（预览版）

“消息扩充”是 IoT 中心在将消息发送到指定的终结点之前，使用附加的信息为消息添加戳记的功能。 使用消息扩充的原因之一是包含可用于简化下游处理的数据。 例如，使用设备孪生标记扩充设备遥测消息可以减少客户对此信息发出设备孪生 API 调用所造成的负载。 有关详细信息，请参阅[消息扩充概述](iot-hub-message-enrichments-overview.md)。

在本教程中，你将使用 Azure CLI 来设置资源，其中包括两个终结点，它们指向两个不同的存储容器 -- **enriched** 和 **original**。 然后，使用 [Azure 门户](https://portal.azure.com)配置仅对发送到包含 **enriched** 存储容器的终结点的消息应用消息扩充。 消息将发送到 IoT 中心，然后路由到这两个存储容器。 只会扩充发送到 **enriched** 存储容器的终结点的消息。

下面是完成本教程所要执行的任务：

**使用 IoT 中心消息扩充**
> [!div class="checklist"]
> * 使用 Azure CLI 创建资源 -- IoT 中心、包含两个终结点的存储帐户，以及路由配置。
> * 使用 Azure 门户配置消息扩充。
> * 运行一个应用，用于模拟向中心发送消息的 IoT 设备。
> * 查看结果，并验证消息扩充是否按预期方式运行。

## <a name="prerequisites"></a>先决条件

* 必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 安装 [Visual Studio](https://www.visualstudio.com/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>检索示例代码

下载 [IoT 设备模拟](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)并将其解压缩。 此存储库包含多个应用程序，其中包括用于向 IoT 中心发送消息的应用程序。

此下载内容还包含用于创建资源以测试消息扩充的脚本。 该脚本位于 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli 中。 现在，可以查看并使用该脚本。 也可以直接在本文中复制该脚本。

准备好开始测试时，将使用此下载内容中的设备模拟应用程序向 IoT 中心发送消息。

## <a name="set-up-and-configure-resources"></a>设置并配置资源

除了创建所需的资源以外，Azure CLI 脚本还会配置终结点（独立的存储容器）的两个路由。 有关配置路由的详细信息，请参阅[路由教程](tutorial-routing.md)。 设置资源后，使用 [Azure 门户](https://portal.azure.com)配置每个终结点的消息扩充，然后继续执行测试步骤。

> [!NOTE]
> 所有消息将路由到这两个终结点，但只会扩充发送到已配置了消息扩充的终结点的消息。
>

可以使用以下脚本，或者打开已下载的存储库的 /resources 文件夹中的脚本。 下面是该脚本执行的步骤：

* 创建 IoT 中心。
* 创建存储帐户。
* 在存储帐户中创建两个容器 - 一个容器用于已扩充的消息，另一个容器用于未扩充的消息。
* 为两个不同的存储帐户设置路由。
    * 为每个存储帐户容器创建一个终结点。
    * 创建每个存储帐户容器终结点的路由。

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 为方便运行脚本，这些资源名称的后面追加了名为 *randomValue* 的随机字母数字值。 randomValue 在脚本的顶部生成一次，并根据需要追加到整个脚本中的资源名称。 如果不想要使用随机后缀，可将其设置为空字符串或特定值。

如果尚未执行此操作, 请打开[Bash 的 Cloud Shell 窗口。](https://shell.azure.com) 打开已解压缩的存储库中的脚本，使用 Ctrl-A 选择其整个内容，然后按 Ctrl-C 复制这些内容。 也可以复制以下 CLI 脚本，或者直接在 Cloud Shell 中将其打开。 右键单击命令行并选择“粘贴”，在 Azure Cloud Shell 窗口中粘贴该脚本。 该脚本每次运行一条语句。 脚本停止运行后，按 **Enter** 确保运行最后一条命令。 以下代码块显示了使用的脚本，并提供了注释用于解释脚本的作用。

下面是脚本创建的资源。 **Enriched** 表示该资源用于扩充的消息。 **Original** 表示该资源用于未扩充的消息。

| 名称 | ReplTest1 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 容器名称 | original  |
| 容器名称 | enriched  |
| IoT 设备名称 | Contoso-Test-Device |
| IoT 中心名称 | ContosoTestHubMsgEn |
| 存储帐户名称 | contosostorage |
| 终结点名称 1 | ContosoStorageEndpointOriginal |
| 终结点名称 2 | ContosoStorageEndpointEnriched|
| 路由名称 1 | ContosoStorageRouteOriginal |
| 路由名称 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

现已设置所有资源并已配置路由。 可在门户中查看消息路由配置，并为发往 **enriched** 存储容器的消息设置消息扩充。

### <a name="view-routing-and-configure-the-message-enrichments"></a>查看路由并配置消息扩充

1. 选择“资源组”转到 IoT 中心，然后选择为本教程设置的资源组 (**ContosoResources_MsgEn**)。 在列表找到“IoT 中心”并将其选中。 选择 IoT 中心的“消息路由”*。

   ![选择消息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   “消息路由”窗格包含三个选项卡 -“路由”、“自定义终结点”和“扩充消息”。 可以浏览前两个选项卡，以查看脚本设置的配置。 使用第三个选项卡添加消息扩充。 让我们扩充要发往名为 **enriched** 的存储容器的终结点的消息。 填写名称和值，然后从下拉列表中选择终结点 **ContosoStorageEndpointEnriched**。 以下示例设置一个将 IoT 中心名称添加到消息的扩充：

   ![添加第一个扩充](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 将这些值添加到 ContosoStorageEndpointEnriched 终结点的列表。

   | 姓名 | ReplTest1 | 终结点（下拉列表） |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Msds-devicelocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 如果你的设备没有孪生，则此处输入的值将会根据消息扩充中的值戳记为字符串。 若要查看设备孪生信息，请在门户中转到你的中心，选择“IoT 设备”，选择你的设备，然后在页面顶部选择“设备孪生”。
   >
   > 可以编辑孪生信息以添加标记（例如位置），并将其设置为所需的特定值。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](iot-hub-devguide-device-twins.md)

3. 完成后，窗格应如下图所示：

   ![包含所有已添加扩充的表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 选择“应用”保存更改。

## <a name="send-messages-to-the-iot-hub"></a>将消息发送到 IoT 中心

为终结点配置消息扩充后，请运行模拟设备应用程序，以将消息发送到 IoT 中心。 为中心设置了可实现以下目的的规则：

* 路由到存储终结点 ContosoStorageEndpointOriginal 的消息不会扩充，将会存储在存储容器 `original` 中。

* 路由到存储终结点 ContosoStorageEndpointEnriched 的消息将会扩充，并存储在存储容器 `enriched` 中。

模拟设备应用程序是已解压缩的下载内容中的应用程序之一。 该应用程序针对[路由教程](tutorial-routing.md)中每个不同的消息路由方法（包括 Azure 存储）发送消息。

双击解决方案文件 (IoT_SimulatedDevice.sln) 以在 Visual Studio 中打开代码，然后打开 Program.cs。 请将 `{your hub name}` 替换为 IoT 中心名称。 IoT 中心主机名的格式为 **{中心名称}.azure-devices.net**。 本教程中的中心主机名为 **ContosoTestHubMsgEn.azure-devices.net**。 接下来，请将 `{device key}` 替换为前面在运行脚本创建资源时所保存的设备密钥。

如果没有设备密钥，可以从门户中检索。 登录后，转到“资源组”，选择你的资源组，然后选择你的 IoT 中心。 在“IoT 设备”下找到你的测试设备，然后选择该设备。 选择“主密钥”旁边的复制图标，将主密钥复制到剪贴板。

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>运行和测试

运行控制台应用程序。 稍等几分钟。 发送的消息将显示在应用程序的控制台屏幕上。

此应用每隔一秒发送一条新的设备到云消息到 IoT 中心。 此消息包含一个 JSON 序列化对象，其中具有设备 ID、温度、湿度和消息级别（级别默认为 `normal`）。 它会随机分配 `critical` 或 `storage` 级别，导致消息路由到存储帐户或默认终结点。 发送到存储帐户中 **enriched** 容器的消息将会扩充。

发送多条存储消息后，查看数据。

1. 选择“资源组”，然后找到你的资源组 (ContosoResourcesMsgEn) 并将其选中。

2. 选择你的存储帐户 (contosostorage)。 然后在左侧的选择窗格中选择“存储资源管理器(预览版)”。

   ![选择存储资源管理器](./media/tutorial-message-enrichments/select-storage-explorer.png)

   选择“BLOB 容器”查看可用的两个容器。

   ![查看存储帐户中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

名为 **enriched** 的容器中的消息包含扩充内容。 名为 **original** 的容器中的消息包含原始内容，而不包含扩充内容。 在其中一个容器中向下钻取到底部，打开最近的消息文件，然后，针对另一个容器执行相同的操作，以确认未将任何扩充内容添加到该容器中的消息。

查看已扩充的消息时，应会看到带有中心名称、位置和客户 ID 的“我的 IoT 中心”，如下所示：

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

下面是一条未扩充的消息。 "我的 IoT 中心"、"msds-devicelocation" 和 "customerID" 不显示在此处, 因为此终结点没有根据。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清理资源

若要删除在本教程中创建的所有资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在此示例中，它会删除 IoT 中心、存储帐户和资源组本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 清理资源

若要删除资源组，请使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 在本教程的开头，`$resourceGroup` 已设置为 **ContosoResources**。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用以下步骤配置并测试了为 IoT 中心消息添加消息扩充：

**使用 IoT 中心消息扩充**
> [!div class="checklist"]
> * 使用 Azure CLI 创建资源 -- IoT 中心、包含两个终结点的存储帐户，以及路由配置。
> * 使用 Azure 门户配置消息扩充。
> * 运行一个应用，用于模拟向中心发送消息的 IoT 设备。
> * 查看结果，并验证消息扩充是否按预期方式运行。

有关消息扩充的详细信息，请参阅[消息扩充概述](iot-hub-message-enrichments-overview.md)。

有关消息路由的详细信息，请参阅以下文章：

* [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](iot-hub-devguide-messages-d2c.md)

* [教程：IoT 中心路由](tutorial-routing.md)