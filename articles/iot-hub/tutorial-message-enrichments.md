---
title: 教程-使用 Azure IoT 中心消息到
description: 演示如何使用消息正进行 Azure IoT 中心的消息教程
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259069"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>教程：使用 Azure IoT 中心消息正 （预览版）

*消息正*是一种向 IoT 中心能力*戳*之前将消息发送到指定终结点的其他信息的消息。 使用消息正的一个原因是包含可用于简化下游处理的数据。 例如，丰富设备的遥测消息数与设备孪生标记可以减少对客户进行 API 调用获取此信息的设备孪生的负载。 有关详细信息，请参阅[概述消息正](iot-hub-message-enrichments-overview.md)。

在本教程中，你使用 Azure CLI 设置资源，包括指向两个不同的存储容器-的两个终结点**丰富**并**原始**。 然后，使用[Azure 门户](https://portal.azure.com)若要配置消息到要应用于发送到具有终结点的消息仅**丰富**存储容器。 您将消息发送到 IoT 中心路由到这两个存储容器。 仅发送到的终结点的消息**丰富**将增加存储容器。

下面是为完成本教程将执行的任务：

**使用 IoT 中心消息到**
> [!div class="checklist"]
> * 使用 Azure CLI 创建资源--IoT 中心、 具有两个终结点的存储帐户和路由配置。
> * 使用 Azure 门户配置消息正。
> * 运行模拟 IoT 设备向中心发送消息的应用。
> * 查看结果并验证消息到按预期工作。

## <a name="prerequisites"></a>必备组件

* 必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 安装 [Visual Studio](https://www.visualstudio.com/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>检索示例代码

下载[IoT 设备模拟](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)并将其解压缩。 此存储库中，包括的一个将用于将消息发送到 IoT 中心有多个应用程序。

此下载还包含用于创建用来测试消息正的资源的脚本。 脚本位于 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli。 现在，可以看一下脚本，并使用它。 此外可以直接从本文复制该脚本。

当准备好开始测试，将使用此下载中的设备模拟应用程序将消息发送到 IoT 中心。

## <a name="set-up-and-configure-resources"></a>设置和配置资源

除了创建所需的资源，Azure CLI 脚本还配置在两个路由到终结点是单独的存储容器。 配置路由的详细信息，请参阅[路由教程](tutorial-routing.md)。 资源设置后，使用[Azure 门户](https://portal.azure.com)配置消息正对于每个终结点，然后再继续到测试步骤。

> [!NOTE]
> 所有消息都路由到两个终结点，但将丰富仅转到与配置的消息到终结点的消息。
>

可以使用以下脚本，也可以下载存储库的 /resources 文件夹中打开该脚本。 下面是该脚本将执行的步骤：

* 创建 IoT 中心。
* 创建存储帐户。
* 在存储帐户-一个丰富的消息，一个用于不丰富的消息中创建两个容器。
* 设置路由的两个不同的存储帐户。
    * 创建每个存储帐户容器的终结点。
    * 创建到每个存储帐户容器终结点的路由。

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 若要使更轻松地运行该脚本，这些资源名称后面附加了名为的随机字母数字值*randomValue*。 randomValue 在脚本的顶部生成一次，并根据需要追加到整个脚本中的资源名称。 如果不想要使用随机后缀，可将其设置为空字符串或特定值。

如果尚未这样做，打开[的 Bash Cloud Shell 窗口](https://shell.azure.com)。 在已解压缩的存储库中打开该脚本，请使用 Ctrl-A 选择所有它，然后按 CTRL-C 将其复制。 或者，可以复制以下 CLI 脚本或直接在 cloud shell 中将其打开。 将脚本粘贴在 Azure cloud shell 窗口中，在命令行上右键单击并选择**粘贴**。 该脚本一次运行一个语句。 该脚本停止运行后，选择**Enter**以确保它在运行最后一个命令。 下面的代码块显示了使用，并且使用注释解释了它的作用的脚本。

以下是由脚本创建的资源。 **丰富**意味着资源具有正的消息。 **原始**意味着资源不丰富的消息。

| 名称 | 值 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 容器名称 | 源语言  |
| 容器名称 | 丰富  |
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

此时，资源已启动的所有设置和配置路由。 您可以在门户中查看的消息路由配置和设置消息路由到消息正**丰富**存储容器。

### <a name="view-routing-and-configure-the-message-enrichments"></a>查看路由和配置消息到

1. 转到 IoT 中心，通过选择**资源组**，然后选择本教程中设置的资源组 (**ContosoResources_MsgEn**)。 在列表中查找 IoT 中心，并选择它。 选择*消息路由** 的 Iot 中心。

   ![选择消息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   消息路由的窗格会显示三个选项卡-**路由**，**自定义终结点**，并**丰富消息**。 您可以浏览前两个选项卡中，若要查看配置设置的脚本。 使用第三个选项卡添加到消息。 让我们来丰富消息路由到名为的存储容器的终结点**丰富**。 填写名称和值，并选择终结点，然后**ContosoStorageEndpointEnriched**从下拉列表中。 下面是设置将 IoT 中心名称添加到消息充实示例：

   ![添加第一个扩充](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 将这些值添加到 ContosoStorageEndpointEnriched 终结点的列表。

   | 名称 | 值 | 终结点 （下拉列表中） |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | 设备位置 | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 如果你的设备不具有孪生，在此处放置中的值将标记为消息到中的值的字符串。 若要查看设备孪生信息，请转到你在门户中的中心，然后选择**IoT 设备**，选择你的设备，并选择**设备孪生**页的顶部。
   >
   > 可以编辑要添加的标记 （如位置） 并将其设置为特定值，如果想要的孪生信息。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](iot-hub-devguide-device-twins.md)

3. 完成后，您的窗格应类似于此图像：

   ![与所有正添加的表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 选择**应用**以保存所做的更改。

## <a name="send-messages-to-the-iot-hub"></a>将消息发送到 IoT 中心

现在，消息到终结点，运行模拟设备应用程序将消息发送到 IoT 中心进行配置。 中心已完成以下的规则设置：

* 将消息路由到存储终结点 ContosoStorageEndpointOriginal 不来丰富并将其存储的存储容器中`original`。

* 将丰富和存储容器中存储消息路由到存储终结点 ContosoStorageEndpointEnriched `enriched`。

模拟设备应用程序是一个已解压缩的下载中的应用程序。 应用程序将消息发送为每个不同的消息中的路由方法[路由教程](tutorial-routing.md); 这包括 Azure 存储。

双击解决方案文件 (IoT_SimulatedDevice.sln) 以在 Visual Studio 中打开代码，然后打开 Program.cs。 替换`{your hub name}`与 IoT 中心名称。 IoT 中心主机名的格式 **{中心名称}.azure-devices.net**。 对于本教程中，中心主机名是**ContosoTestHubMsgEn.azure devices.net**。 接下来，将替换`{device key}`具有设备密钥保存在运行该脚本创建的资源时前面。

如果没有设备密钥，可以从门户进行检索。 登录后，请转到**资源组**，选择资源组中，然后选择 IoT 中心。 下查找**IoT 设备**测试设备，然后选择你的设备。 选择复制图标旁边**主键**将其复制到剪贴板。

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>运行和测试

运行控制台应用程序。 稍等几分钟。 正在发送的消息的应用程序的控制台屏幕上显示。

此应用每隔一秒发送一条新的设备到云消息到 IoT 中心。 此消息包含一个 JSON 序列化对象，其中具有设备 ID、温度、湿度和消息级别（级别默认为 `normal`）。 它随机分配的级别`critical`或`storage`，从而导致出现消息要路由到存储帐户或默认终结点。 发送到的消息**丰富**将丰富的存储帐户中的容器。

在发送多个存储消息后，查看数据。

1. 选择**资源组**，然后查找你的资源组 (ContosoResourcesMsgEn) 并选择它。

2. 选择你的存储帐户 (contosostorage)。 然后选择**存储资源管理器 （预览版）** 从所选内容左窗格中。

   ![选择存储资源管理器](./media/tutorial-message-enrichments/select-storage-explorer.png)

   选择**BLOB 容器**若要查看可用的两个容器。

   ![请参阅存储帐户中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

在容器中的消息调用**丰富**具有在消息中包含消息正。 在容器中的消息调用**原始**将具有不到与原始消息。 向下钻取到某个容器之前获取到底部并打开最新的消息文件，然后执行要验证没有正添加到该容器中的消息的其他容器相同的。

当您查看已增加的消息时，应会看到"我将 IoT 中心"中心名称以及位置和客户 ID，如下：

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

和下面是 unenriched 的消息。 ""我 IoT 中心"，"设备位置"和"customerID"根本不显示在这里，因为此终结点有不到。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清理资源

如果你想要删除所有已在本教程中创建的资源，删除资源组。 此操作会一并删除组中包含的所有资源。 在此示例中，它会删除 IoT 中心、存储帐户和资源组本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 清理资源

若要删除资源组，请使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 在本教程的开头，`$resourceGroup` 已设置为 **ContosoResources**。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，你配置并测试过添加消息正向 IoT 中心的消息使用以下步骤：

**使用 IoT 中心消息到**
> [!div class="checklist"]
> * 使用 Azure CLI 创建资源--IoT 中心、 具有两个 enendpoints 的存储帐户和路由配置。
> * 使用 Azure 门户配置消息正。
> * 运行的应用程序可模拟 IoT 设备发送消息到中心。
> * 查看结果并验证消息到按预期工作。

有关消息到详细信息，请参阅[概述消息正](iot-hub-message-enrichments-overview.md)。

有关消息路由的详细信息，请参阅以下文章：

* [使用 IoT 中心消息路由到不同的终结点发送设备到云的消息](iot-hub-devguide-messages-d2c.md)

* [教程：路由的 IoT 中心](tutorial-routing.md)