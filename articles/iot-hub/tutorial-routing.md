---
title: 使用 Azure CLI 和 Azure 门户为 Azure IoT 中心配置消息路由 | Microsoft Docs
description: 使用 Azure CLI 和 Azure 门户为 Azure IoT 中心配置消息路由
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 2f382c31c6bfb6ab71afd495c4c3f702715633c0
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661882"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>教程：使用 Azure CLI 和 Azure 门户配置 IoT 中心消息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>使用 Azure CLI 创建基本资源

本教程使用 Azure CLI 创建基本资源，然后使用 [Azure 门户](https://portal.azure.com)来演示如何配置消息路由和设置虚拟设备用于测试。

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 为方便进行标识，这些资源名称的后面追加了名为 *randomValue* 的随机字母数字值。 randomValue 在脚本的顶部生成一次，并根据需要追加到整个脚本中的资源名称。 如果不想要使用随机后缀，可将其设置为空字符串或特定值。

复制以下脚本并将其粘贴到 Cloud Shell，然后按 Enter。 系统每次运行脚本中的一行。 此脚本将创建本教程所需的基本资源，包括存储帐户、IoT 中心、服务总线命名空间和服务总线队列。

有关调试的说明：此脚本使用续接符号（反斜杠 `\`），使脚本更方便阅读。 如果在运行脚本时遇到问题，请确保任何反斜杠后面没有空格。

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

设置基本资源后，可以在 [Azure 门户](https://portal.azure.com)中配置消息路由。

## <a name="set-up-message-routing"></a>设置消息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>路由到存储帐户

现在为存储帐户设置路由。 你转到“消息路由”窗格，然后添加路由。 添加路由时，请为路由定义新的终结点。 设置此路由后，**level** 属性设置为 **storage** 的消息将自动写入存储帐户。 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. 在 [Azure 门户](https://portal.azure.com)中选择“资源组”，然后选择你的资源组。 本教程使用 ContosoResources。

2. 在资源列表下选择“IoT 中心”。 本教程使用 ContosoTestHub。

3. 选择“消息路由”。 在“消息路由”窗格中，选择“+添加”。 在“添加路由”窗格中，选择“终结点”字段旁边的“+添加”以显示支持的终结点，如下图所示：

   ![开始添加路由的终结点](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. 选择“Blob 存储”。 此时会显示“添加存储终结点”窗格。

   ![添加终结点](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. 为终结点输入名称。 本教程使用 **ContosoStorageEndpoint**。

6. 选择“选取容器”。 将转到存储帐户列表。 选择在准备步骤中设置的存储账户。 本教程使用 **contosostorage**。 它显示该存储帐户中的容器列表。 **选择**在准备步骤中设置的容器。 本教程使用 contosoresults。 返回到“添加存储终结点”窗格并查看所做的选择。

7. 将编码设置为 AVRO 或 JSON。 在本教程中，其余字段使用默认值。 如果所选的区域不支持 JSON 编码，则此字段将会灰显。

   > [!NOTE]
   > 可以使用 **Blob 文件名格式**设置 Blob 名称的格式。 默认为 `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`。 格式必须包含 {iothub}、{partition}、{YYYY}、{MM}、{DD}、{HH} 和 {mm}，顺序不限。
   >
   > 例如，使用默认 Blob 文件名格式时，如果中心名称为 ContosoTestHub，日期/时间为 2018 年 10 月 30 日上午 10:56，则 Blob 名称将类似于：`ContosoTestHub/0/2018/10/30/10/56`。
   > 
   > Blob 以 Avro 格式写入。
   >

8. 选择“创建”以创建存储终结点，并将其添加到路由。 随即返回到“添加路由”窗格。

9. 现在完成余下的路由查询信息。 此查询指定将消息发送到刚刚添加为终结点的存储容器的条件。 填充屏幕上的字段。

   **名称**：为路由查询输入名称。 本教程使用 **ContosoStorageRoute**。

   **终结点**：选择刚刚设置的终结点。

   **数据源**：从下拉列表选择“设备遥测消息”。

   **启用路由**：确保此字段设置为 `enabled`。
   
   **路由查询**：输入 `level="storage"` 作为查询字符串。

   ![为存储帐户创建路由查询](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   选择“保存”。 完成后，返回到“消息路由”窗格，可在其中看到存储的新路由查询。 关闭“路由”窗格，将返回到资源组页。

### <a name="route-to-a-service-bus-queue"></a>路由到服务总线队列

现在为服务总线队列设置路由。 你转到“消息路由”窗格，然后添加路由。 添加路由时，请为路由定义新的终结点。 设置此路由后，**level** 属性设置为 **critical** 的消息将写入服务总线队列，这会触发某个逻辑应用，然后发送包含相关信息的电子邮件。

1. 在“资源组”页上选择你的 IoT 中心，然后选择“消息路由”。

2. 在“消息路由”窗格中，选择“+添加”。

3. 在“添加路由”窗格中，选择“终结点”字段旁边的“+添加”。 选择“服务总线队列”。 随即看到“添加服务总线终结点”窗格。

   ![添加服务总线终结点](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. 填写字段：

   **终结点名称**：为终结点输入名称。 本教程使用 **ContosoSBQueueEndpoint**。
   
   **服务总线命名空间**：使用下拉列表选择在准备步骤中设置的服务总线命名空间。 本教程使用 ContosoSBNamespace。

   **服务总线队列**：使用下拉列表选择服务总线队列。 本教程使用 contososbqueue。

5. 选择“创建”以添加服务总线队列终结点。 随即返回到“添加路由”窗格。

6. 现在完成余下的路由查询信息。 此查询指定将消息发送到刚刚添加为终结点的服务总线队列的条件。 填充屏幕上的字段。 

   **名称**：为路由查询输入名称。 本教程使用 **ContosoSBQueueRoute**。 

   **终结点**：选择刚刚设置的终结点。

   **数据源**：从下拉列表选择“设备遥测消息”。

   **路由查询**：输入 `level="critical"` 作为查询字符串。 

   ![为服务总线队列创建路由查询](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. 选择“保存”。 返回到“路由”窗格时，可看到这两个新的路由，如此处所示。

   ![刚刚设置的路由](./media/tutorial-routing/message-routing-show-both-routes.png)

8. 可以通过选择“自定义终结点”选项卡来查看设置的自定义终结点。

   ![刚刚设置的自定义终结点](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. 关闭“消息路由”窗格，返回到“资源组”窗格。

## <a name="create-a-simulated-device"></a>创建模拟设备

[!INCLUDE [iot-hub-include-create- imulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>后续步骤

设置资源并配置消息路由后，请继续学习下一篇教程，了解如何将消息发送到 IoT 中心，并查看其如何路由到不同的目标。 

> [!div class="nextstepaction"]
> [第 2 部分 - 查看消息路由结果](tutorial-routing-view-message-routing-results.md)
