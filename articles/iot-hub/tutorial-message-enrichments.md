---
title: 教程-使用 Azure IoT 中心消息根据
description: 介绍如何对 Azure IoT 中心消息使用 message 根据的教程
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: 323730fff4659c87058669016b69808a880994cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453890"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments"></a>教程：使用 Azure IoT 中心消息根据

*消息根据*是 IoT 中心在将消息发送到指定终结点之前，用其他信息来*标记*消息的能力。 使用 message 根据的一个原因是包含可用于简化下游处理的数据。 例如，使用设备克隆标记浓缩设备遥测消息可降低客户的负载，使设备对此信息进行 API 调用。 有关详细信息，请参阅[message 根据概述](iot-hub-message-enrichments-overview.md)。

在本教程中，你将看到两种创建和配置为 IoT 中心测试消息根据所需资源的方法。 资源包括一个包含两个存储容器的存储帐户-一个用于保存已扩充的消息，另一个用于保存原始消息。 还包括一个 IoT 中心，用于接收这些消息并将其路由到适当的存储容器，具体取决于它们是否已丰富。 

* 第一种方法是使用 Azure CLI 创建资源并配置消息路由。 然后，使用[Azure 门户](https://portal.azure.com)手动定义根据。 

* 第二种方法是使用 Azure 资源管理器模板来创建消息路由和消息根据的资源*和*配置。 

消息路由和消息根据的配置完成后，可以使用应用程序将消息发送到 IoT 中心，然后将消息路由到两个存储容器。 仅对已扩充存储容器的终结点**发送的消息**进行了丰富。

以下是完成本教程所要执行的任务：

**使用 IoT 中心消息根据**
> [!div class="checklist"]
> * 第一种方法：手动消息根据
>   - 使用 Azure CLI 创建资源并配置消息路由。
>   - 使用[Azure 门户](https://portal.azure.com)手动配置消息根据。
> * 第二种方法：使用 RM 模板
>   - 使用 Azure 资源管理器模板创建资源、配置消息路由和消息根据。 
> * 运行模拟 IoT 设备向中心发送消息的应用。
> * 查看结果，并验证消息根据是否按预期方式工作。

## <a name="prerequisites"></a>必备组件

* 必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 安装 [Visual Studio](https://www.visualstudio.com/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>检索 IoT C#示例存储库

从 GitHub[下载C# IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)并将其解压缩。 此存储库包含多个应用程序、脚本和资源管理器模板。 要在本教程中使用的方法如下：

* 对于手动方法，有一个用于创建资源的 CLI 脚本。 此脚本位于 **/azure-iot-samples-csharp/iot-hub/tutorials/routing/simulateddevice/resources/iothub_msgenrichment_cli. azcli**中。 此脚本将创建资源并配置消息路由。 运行此操作后，你可以使用[Azure 门户](https://portal.azure.com)手动创建消息根据，然后运行 DeviceSimulation 应用以查看根据工作。

* 对于自动化方法，有一个 Azure 资源管理器模板。 该模板位于 **/azure-iot-samples-csharp/iot-hub/tutorials/routing/simulateddevice/resources/template_msgenrichments。** 此模板创建资源，配置消息路由，最后配置消息根据。 加载此模板后，您可以运行设备模拟应用程序，以查看根据的工作情况。

* 你使用的第三个应用程序是设备模拟应用，用于向 IoT 中心发送消息并测试消息根据。

## <a name="manual-set-up-and-configuration-using-azure-cli"></a>使用 Azure CLI 进行手动设置和配置

除了创建必要的资源以外，Azure CLI 脚本还会将两个路由配置为单独存储容器的终结点。 有关配置消息路由的详细信息，请参阅[路由教程](tutorial-routing.md)。 设置资源后，可以使用[Azure 门户](https://portal.azure.com)为每个终结点配置 message 根据，然后继续执行测试步骤。

> [!NOTE]
> 所有消息都路由到这两个终结点，但只会对发送到带有已配置消息根据的终结点的消息进行扩充。
>

您可以使用下面的脚本，或在下载的存储库的/resources 文件夹中打开该脚本。 脚本将执行以下步骤：

* 创建 IoT 中心。
* 创建存储帐户。
* 在存储帐户中创建两个容器-一个用于已扩充的消息，另一个用于不会被丰富的消息。
* 为两个不同的存储帐户设置路由。
    * 为每个存储帐户容器创建一个终结点。
    * 创建指向每个存储帐户容器终结点的路由。

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 为了更轻松地运行脚本，将使用名为*randomValue*的随机字母数字值追加这些资源名称。 randomValue 在脚本的顶部生成一次，并根据需要追加到整个脚本中的资源名称。 如果不想要使用随机后缀，可将其设置为空字符串或特定值。

如果尚未执行此操作，请打开[Cloud Shell 窗口](https://shell.azure.com)，并确保将其设置为 Bash。 在解压缩的存储库中打开脚本，使用 Ctrl + A 选择所有该脚本，然后按 Ctrl-c 复制它。 或者，你可以复制以下 CLI 脚本或直接在 Cloud Shell 中打开它。 右键单击命令行并选择 "**粘贴**"，将该脚本粘贴到 "Cloud Shell" 窗口中。 脚本一次运行一条语句。 脚本停止运行后，选择**Enter**以确保它运行最后一个命令。 下面的代码块显示了所使用的脚本，其中注释说明了所执行的操作。

下面是脚本创建的资源。 "**丰富**的" 是指资源适用于具有根据的消息。 "**原始**" 表示资源是指不会得到丰富的消息。

| 名称 | 值 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 容器名称 | 原配  |
| 容器名称 | 不要  |
| IoT 设备名称 | Contoso-测试-设备 |
| IoT 中心名称 | ContosoTestHubMsgEn |
| 存储帐户名称 | contosostorage |
| 终结点名称1 | ContosoStorageEndpointOriginal |
| 终结点名称2 | ContosoStorageEndpointEnriched|
| 路由名称1 | ContosoStorageRouteOriginal |
| 路由名称2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

此时，将设置所有资源并配置消息路由。 可以在门户中查看消息路由配置，并设置消息根据，以获取转到已**扩充存储容器的消息**。

### <a name="manually-configure-the-message-enrichments-using-the-azure-portal"></a>使用 Azure 门户手动配置消息根据

1. 通过选择 "**资源组**" 切换到 IoT 中心，然后选择为本教程设置的资源组（**ContosoResourcesMsgEn**）。 在列表中找到 IoT 中心并将其选中。 为 Iot 中心选择 "**消息路由**"。

   ![选择消息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   "消息路由" 窗格有三个选项卡：**路由**、**自定义终结点**和**浓缩消息**。 您可以浏览前两个选项卡，查看由脚本设置的配置。 使用第三个选项卡添加消息根据。 让我们来丰富发送到存储容器终结点的消息（**称为 "** 扩展"）。 填写 "名称" 和 "值"，然后从下拉列表中选择终结点**ContosoStorageEndpointEnriched** 。 下面是设置扩充的示例，它将 IoT 中心名称添加到消息：

   ![添加第一个扩充](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 将这些值添加到 ContosoStorageEndpointEnriched 终结点的列表。

   | 密钥 | 值 | 终结点（下拉列表） |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Msds-devicelocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |顾客 | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 如果设备没有克隆，则在此处输入的值将被标记为消息根据中的值的字符串。 若要查看设备克隆信息，请在门户中转到你的中心，然后选择 " **IoT 设备**"，选择你的设备，然后选择页面顶部的 "**设备**克隆"。
   >
   > 若要添加标记（如位置）并将其设置为特定值，则可以编辑 "输出" 信息。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](iot-hub-devguide-device-twins.md)

3. 完成后，您的窗格应如下图所示：

   ![添加了所有根据的表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 选择 "**应用**" 保存更改。 跳到[测试消息根据](#testing-message-enrichments)部分。

## <a name="use-an-rm-template-to-create-and-configure-the-resources-message-routing-and-message-enrichments"></a>使用 RM 模板来创建和配置资源、消息路由和消息根据 

1. 登录到 Azure 门户。 单击 " **+ 创建资源**"。 这会打开搜索框。 搜索**模板部署**。 在结果窗格中，选择**模板部署（使用自定义模板进行部署）** 。

   ![Azure 门户中的模板部署](./media/tutorial-message-enrichments/template-select-deployment.png)

1. 在 "模板部署" 窗格中选择 "**创建**"。 

1. 在 "自定义部署" 窗格上。 选择 **"在编辑器中生成自己的模板"** 。

1. 在 "编辑模板" 窗格上，选择 "**加载文件**"。 你会看到 Windows 资源管理器。 在 **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**中找到解压缩存储库文件中的**template_messageenrichments**文件。 

   ![从本地计算机中选择模板](./media/tutorial-message-enrichments/template-select.png)

1. 选择 "**打开**"，从本地计算机加载模板文件。 它将其加载到编辑窗格并向你显示。

   此模板设置为使用全局唯一的 IoT 中心名称和存储帐户名称，方法是将随机值添加到默认名称的末尾，以便可以使用模板，而无需对其进行任何更改。 

   下面是通过加载模板创建的资源。 "**丰富**的" 是指资源适用于具有根据的消息。 "**原始**" 表示资源是指不会得到丰富的消息。 这些值与 Azure CLI 脚本中使用的值相同。

   | 名称 | 值 |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | 容器名称 | 原配  |
   | 容器名称 | 不要  |
   | IoT 设备名称 | Contoso-测试-设备 |
   | IoT 中心名称 | ContosoTestHubMsgEn |
   | 存储帐户名称 | contosostorage |
   | 终结点名称1 | ContosoStorageEndpointOriginal |
   | 终结点名称2 | ContosoStorageEndpointEnriched|
   | 路由名称1 | ContosoStorageRouteOriginal |
   | 路由名称2 | ContosoStorageRouteEnriched |

1. 选择 "**保存**"，将显示 "自定义部署" 窗格，其中显示了模板使用的所有参数。 需要设置的唯一字段是**资源组**。 请创建一个新的组，或从下拉列表中选择一个。

   下面是 "自定义部署" 窗格的上半部分。 你可以查看在何处填充资源组。

   !["自定义部署" 窗格的上半部分](./media/tutorial-message-enrichments/template-deployment-top.png)

1. 下面是 "自定义部署" 窗格的下半部分。 您可以看到其余的参数以及条款和条件。 

   !["自定义部署" 窗格的下半部分](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. 选中表明你同意条款和条件的复选框，然后选择 "**购买**" 以继续进行模板部署。

1. 等待模板完全部署。 可以选择屏幕顶部的钟形图标来检查进度。 完成后，可以继续[测试 message 根据](#testing-message-enrichments)。

## <a name="testing-message-enrichments"></a>测试消息根据

可以通过选择 "**资源组**" 查看消息根据，并选择用于本教程的资源组。 然后从资源列表中选择 "IoT 中心"，然后单击 "**消息传送**"。 这会显示消息路由配置和已配置的根据。

现在，已为终结点配置消息根据，运行模拟设备应用程序将消息发送到 IoT 中心。 已设置中心，其设置可实现以下目的：

* 路由到存储终结点 ContosoStorageEndpointOriginal 的消息将不会被充实，并将存储在 `original`的存储容器中。

* 路由到存储终结点 ContosoStorageEndpointEnriched 的消息将被充实并存储在存储容器 `enriched`中。

模拟设备应用程序是解压缩下载中的应用程序之一。 该应用程序会为[路由教程](tutorial-routing.md)中的每个不同消息路由方法发送消息;这包括 Azure 存储。

双击解决方案文件 (IoT_SimulatedDevice.sln) 以在 Visual Studio 中打开代码，然后打开 Program.cs。 替换标记 `{your hub name}`的 IoT 中心名称。 IoT 中心主机名的格式为 **{你的中心名称}。 azure-devices.net**。 对于本教程，中心主机名为**ContosoTestHubMsgEn.azure-devices.net**。 接下来，请在运行脚本时替换之前保存的设备密钥，以创建标记 `{your device key}`的资源。

如果没有设备密钥，可以从门户中检索它。 登录后，请前往 "**资源组**"，选择资源组，并选择 IoT 中心。 在 " **IoT 设备**" 下查看测试设备，并选择设备。 选择 "**主密钥**" 旁边的复制图标，将其复制到剪贴板。

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>运行和测试

运行控制台应用程序几分钟。 正在发送的消息将显示在应用程序的控制台屏幕上。

此应用每隔一秒发送一条新的设备到云消息到 IoT 中心。 此消息包含一个 JSON 序列化对象，其中具有设备 ID、温度、湿度和消息级别（级别默认为 `normal`）。 它会随机分配一个级别的 `critical` 或 `storage`，导致消息路由到存储帐户或默认终结点。 将对发送到存储帐户中的已扩充容器的消息进行**了丰富。**

发送了几个存储消息后，查看数据。

1. 选择 "**资源组**"，然后找到资源组（ContosoResourcesMsgEn）并将其选中。

2. 选择存储帐户（contosostorage）。 然后从左侧选择窗格中选择 "**存储资源管理器（预览）** "。

   ![选择存储资源管理器](./media/tutorial-message-enrichments/select-storage-explorer.png)

   选择 " **BLOB 容器**" 以查看可使用的两个容器。

   ![查看存储帐户中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

称为 "扩充" 的容器中的消息在消息**中包含根据**消息。 容器中名为 "**原始**" 的消息将具有原始消息，而不包含根据。 向下钻取到一个容器，直到到达底部并打开最新的消息文件，然后对其他容器执行相同的操作，以验证是否没有根据添加到该容器中的消息。

查看已丰富的消息时，应会看到 "我的 IoT 中心"，其中包含中心名称以及位置和客户 ID，如下所示：

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

下面是 unenriched 消息。 "我的 IoT 中心"、"msds-devicelocation" 和 "customerID" 不显示在此处，因为这些字段是由根据添加的字段，而此终结点没有根据。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清理资源

如果要删除在本教程中创建的所有资源，请删除该资源组。 此操作会一并删除组中包含的所有资源。 在此示例中，它会删除 IoT 中心、存储帐户和资源组本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 清理资源

若要删除资源组，请使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 在本教程开始时，`$resourceGroup` 设置为**ContosoResourcesMsgEn** 。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，使用以下步骤配置并测试了向 IoT 中心消息添加消息根据：

**使用 IoT 中心消息根据**
> [!div class="checklist"]
> * 第一种方法
>   * 使用 Azure CLI 创建资源并配置消息路由。
>   * 使用[Azure 门户](https://portal.azure.com)手动配置消息根据。
> * 第二种方法
>   * 使用 Azure 资源管理器模板创建资源、配置消息路由和消息根据。 
> * 运行模拟 IoT 设备向中心发送消息的应用。
> * 查看结果，并验证消息根据是否按预期方式工作。

有关消息根据的详细信息，请参阅[message 根据概述](iot-hub-message-enrichments-overview.md)。

有关消息路由的详细信息，请参阅以下文章：

* [使用 IoT 中心消息路由将设备到云消息发送到不同的终结点](iot-hub-devguide-messages-d2c.md)

* [教程： IoT 中心路由](tutorial-routing.md)