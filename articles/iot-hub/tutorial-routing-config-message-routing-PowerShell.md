---
title: 使用 Azure PowerShell 为 Azure IoT 中心配置消息路由
description: 使用 Azure PowerShell 为 Azure IoT 中心配置消息路由。 根据消息中的属性，路由到存储帐户或服务总线队列。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084436"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>教程：使用 Azure PowerShell 配置 IoT 中心消息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>下载脚本（可选）

在本教程的第二部分，我们将下载并运行一个 Visual Studio 应用程序，以将消息发送到 IoT 中心。 下载内容的某个文件夹中包含 Azure 资源管理器模板和参数文件，以及 Azure CLI 和 PowerShell 脚本。 

若要查看已完成的脚本，请下载 [Azure IoT C# 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 解压缩 master.zip 文件。 Azure CLI 脚本位于 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ 中，其文件名为 **iothub_routing_psh.ps1**。

## <a name="create-your-resources"></a>创建资源

首先使用 PowerShell 创建资源。

### <a name="use-powershell-to-create-your-base-resources"></a>使用 PowerShell 创建基本资源

复制以下脚本并将其粘贴到 Cloud Shell，然后按 Enter。 系统每次运行脚本中的一行。 该脚本的第一节将创建本教程所需的基本资源，包括存储帐户、IoT 中心、服务总线命名空间和服务总线队列。 在学习本教程的过程中，请复制每个脚本块并将其粘贴到 Cloud Shell，以运行该块。

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 为方便进行标识，这些资源名称的后面追加了名为 *randomValue* 的随机字母数字值。 randomValue 在脚本的顶部生成一次，并根据需要追加到整个脚本中的资源名称。 如果不想要使用随机后缀，可将其设置为空字符串或特定值。 

> [!IMPORTANT]
> 路由脚本也会使用初始脚本中设置的变量，因此，请在同一 Cloud Shell 会话中运行所有脚本。 如果打开新的会话来运行用于设置路由的脚本，则多个变量将会缺少值。 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>创建模拟设备

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

设置基本资源后，可以配置消息路由。

## <a name="set-up-message-routing"></a>设置消息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

若要创建路由终结点，请使用 [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint)。 若要创建终结点的消息路由，请使用 [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute)。

### <a name="route-to-a-storage-account"></a>路由到存储帐户 

首先设置存储帐户的终结点，然后创建消息路由。

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

以下是脚本使用的、必须在 Cloud Shell 会话中设置的变量：

**resourceGroup**：此字段会在两个位置出现 - 请将两者设置为你的资源组。

**名称**：此字段是要应用路由的 IoT 中心的名称。

**endpointName**：此字段是用于标识终结点的名称。 

**endpointType**：此字段是终结点的类型。 此值必须设置为 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 在本文中，请将其设置为 `azurestoragecontainer`。

**subscriptionID**：此字段设置为你的 Azure 帐户的 subscriptionID。

**storageConnectionString**：此值是从前述脚本设置的存储帐户中检索的。 路由使用此值来访问存储帐户。

**containerName**：此字段是存储帐户中要将数据写入到的容器的名称。

**Encoding**：请将此字段设置为 `AVRO` 或 `JSON`。 这会指定存储数据的格式。 默认值为 AVRO。

**routeName**：此字段是要设置的路由的名称。 

**condition**：此字段是用于筛选发送到此终结点的消息的查询。 路由到存储的消息的查询条件是 `level="storage"`。

**enabled**：此字段默认为 `true`，表示应在创建消息路由后启用该路由。

请复制此脚本，并将其粘贴到 Cloud Shell 窗口中。

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

下一步是为存储帐户创建路由终结点。 还可以指定用于存储结果的容器。 创建存储帐户时已创建该容器。

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

接下来，为存储终结点创建消息路由。 消息路由根据查询规范指定要将消息发送到何处。

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>路由到服务总线队列

现在为服务总线队列设置路由。 若要检索服务总线队列的连接字符串，必须创建一个定义了正确权限的授权规则。 以下脚本为服务总线队列创建名为 `sbauthrule` 的授权规则，并将权限设置为 `Listen Manage Send`。 设置此授权规则后，可以使用它来检索队列的连接字符串。

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

现在，使用该授权规则来检索服务总线队列密钥。 稍后将在脚本中使用此授权规则来检索连接字符串。

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

现在，为服务总线队列设置路由终结点和消息路由。 以下是脚本使用的、必须在 Cloud Shell 会话中设置的变量：

**endpointName**：此字段是用于标识终结点的名称。 

**endpointType**：此字段是终结点的类型。 此值必须设置为 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 在本文中，请将其设置为 `servicebusqueue`。

**routeName**：此字段是要设置的路由的名称。 

**condition**：此字段是用于筛选发送到此终结点的消息的查询。 路由到服务总线队列的消息的查询条件是 `level="critical"`。

下面是服务总线队列的消息路由的 Azure PowerShell。

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>在门户中查看消息路由

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>后续步骤

设置资源并配置消息路由后，请继续学习下一篇教程，了解如何将消息发送到 IoT 中心，并查看其如何路由到不同的目标。 

> [!div class="nextstepaction"]
> [第 2 部分 - 查看消息路由结果](tutorial-routing-view-message-routing-results.md)