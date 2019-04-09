---
title: 使用 Azure CLI 为 Azure IoT 中心配置消息路由 | Microsoft Docs
description: 使用 Azure CLI 为 Azure IoT 中心配置消息路由
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53a0645c42255d5f741d31c411ca70e1e009febc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661575"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>教程：使用 Azure CLI 配置 IoT 中心消息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>下载脚本（可选）

在本教程的第二部分，我们将下载并运行一个 Visual Studio 应用程序，以将消息发送到 IoT 中心。 下载内容的某个文件夹中包含 Azure 资源管理器模板和参数文件，以及 Azure CLI 和 PowerShell 脚本。

若要查看已完成的脚本，请下载 [Azure IoT C# 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 解压缩 master.zip 文件。 Azure CLI 脚本位于 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ 中，其文件名为 **iothub_routing_cli.azcli**。

## <a name="use-the-azure-cli-to-create-your-resources"></a>使用 Azure CLI 创建资源

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 为方便进行标识，这些资源名称的后面追加了名为 *randomValue* 的随机字母数字值。 randomValue 在脚本的顶部生成一次，并根据需要追加到整个脚本中的资源名称。 如果不想要使用随机后缀，可将其设置为空字符串或特定值。 

> [!IMPORTANT]
> 路由脚本也会使用初始脚本中设置的变量，因此，请在同一 Cloud Shell 会话中运行所有脚本。 如果打开新的会话来运行用于设置路由的脚本，则多个变量将会缺少值。
>

复制以下脚本并将其粘贴到 Cloud Shell，然后按 Enter。 系统每次运行脚本中的一行。 该脚本的第一节将创建本教程所需的基本资源，包括存储帐户、IoT 中心、服务总线命名空间和服务总线队列。 在学习本教程余下内容的过程中，请复制每个脚本块并将其粘贴到 Cloud Shell，以运行该块。

> [!TIP]
> 有关调试的提示：此脚本使用续接符号（反斜杠 `\`），使脚本更方便阅读。 如果在运行脚本时遇到问题，请确保任何反斜杠后面没有空格。
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id)

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
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

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
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

设置基本资源后，可以配置消息路由。

## <a name="set-up-message-routing"></a>设置消息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

若要创建路由终结点，请使用 [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint.md?view=azure-cli-latest#az-iot-hub-routing-endpoint-create)。 若要为终结点创建消息路由，请使用 [az iot hub route create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create)。

### <a name="route-to-a-storage-account"></a>路由到存储帐户

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

首先设置存储帐户的终结点，然后设置路由。 

需设置以下变量：

**storageConnectionString**：此值是从前述脚本设置的存储帐户中检索的。 消息路由使用此值来访问存储帐户。

  **resourceGroup**：需要在两个位置使用资源组 - 请将其设置为你的资源组。

**endpoint subscriptionID**：此字段设置为终结点的 Azure subscriptionID。 

**endpointType**：此字段是终结点的类型。 此值必须设置为 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 在本文中，请将其设置为 `azurestoragecontainer`。

**iotHubName**：此字段是执行路由的中心的名称。

**containerName**：此字段是存储帐户中要将数据写入到的容器的名称。

**encoding**：此字段为 `avro` 或 `json`。 这表示存储的数据的格式。

**routeName**：此字段是要设置的路由的名称。 

**endpointName**：此字段是用于标识终结点的名称。 

**enabled**：此字段默认为 `true`，表示创建消息路由后应启用该路由。

**condition**：此字段是用于筛选发送到此终结点的消息的查询。 路由到存储的消息的查询条件是 `level="storage"`。

请复制此脚本，并将其粘贴到 Cloud Shell 窗口以运行。

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

下一步是为存储帐户创建路由终结点。 还可以指定用于存储结果的容器。 前面在创建存储帐户时已创建该容器。

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

接下来，为存储终结点创建路由。 消息路由根据查询规范指定要将消息发送到何处。 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>路由到服务总线队列

现在为服务总线队列设置路由。 若要检索服务总线队列的连接字符串，必须创建一个定义了正确权限的授权规则。 以下脚本为服务总线队列创建名为 `sbauthrule` 的授权规则，并将权限设置为 `Listen Manage Send`。 定义此授权规则后，可以使用它来检索队列的连接字符串。

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

现在，使用授权规则来检索服务总线队列的连接字符串。

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

现在，为服务总线队列设置路由终结点和消息路由。 需设置以下变量：

**endpointName**：此字段是用于标识终结点的名称。 

**endpointType**：此字段是终结点的类型。 此值必须设置为 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 在本文中，请将其设置为 `servicebusqueue`。

**routeName**：此字段是要设置的路由的名称。 

**condition**：此字段是用于筛选发送到此终结点的消息的查询。 路由到服务总线队列的消息的查询条件是 `level="critical"`。

下面是路由终结点的 Azure CLI，以及服务总线队列的消息路由。

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>在门户中查看消息路由

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>后续步骤

设置资源并配置消息路由后，请继续学习下一篇教程，了解如何将消息发送到 IoT 中心，并查看其如何路由到不同的目标。 

> [!div class="nextstepaction"]
> [第 2 部分 - 查看消息路由结果](tutorial-routing-view-message-routing-results.md)