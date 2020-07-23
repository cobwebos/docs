---
title: 使用 Azure 资源管理器模板为 Azure IoT 中心配置消息路由
description: 使用 Azure 资源管理器模板为 Azure IoT 中心配置消息路由
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "74084390"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>教程：使用 Azure 资源管理器模板配置 IoT 中心消息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>消息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>下载模板和参数文件

在本教程的第二部分，我们将下载并运行一个 Visual Studio 应用程序，以将消息发送到 IoT 中心。 该下载内容的某个文件夹中包含 Azure 资源管理器模板和参数文件，以及 Azure CLI 和 PowerShell 脚本。

现在请继续下载 [Azure IoT C# 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 解压缩 master.zip 文件。 资源管理器模板和参数文件位于 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ 中，其文件名分别为 **template_iothub.json** 和 **template_iothub_parameters.json**。

## <a name="create-your-resources"></a>创建资源

我们将使用 Azure 资源管理器 (RM) 模板来创建所有资源。 每次可以运行几行 Azure CLI 和 PowerShell 脚本。 RM 模板是通过一个步骤部署的。 本文通过单独的部分来介绍每种方法。 然后，介绍如何部署模板，以及创建虚拟设备用于测试。 部署模板后，可以在门户中查看消息路由配置。

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 为了简化资源的命名，将在设置的这些资源名称后面追加一个基于当前日期/时间随机生成的字母数字值。 

查看模板时，将会看到为这些资源设置的变量的位置。这些变量采用传入的参数，并将 *randomValue* 连接到该参数。 

以下部分将解释所用的参数。

### <a name="parameters"></a>参数

其中的大部分参数都具有默认值。 以 **_in** 结尾的参数将与 *randomValue* 相连接，使参数名称全局唯一。 

**randomValue**：此值是基于部署模板时的日期/时间生成的。 此字段不包含在参数文件中，因为它是在模板本身中生成的。

**subscriptionId**：此字段用于标识要在其中部署模板的订阅。 此字段不包含在参数文件中，因为它是由系统设置的。

**IoTHubName_in**：此字段是基本 IoT 中心名称，与 randomValue 相连接，使名称全局唯一。

**location**：此字段是要部署到的 Azure 区域，例如“westus”。

**consumer_group**：此字段是为通过路由终结点传入的消息设置的使用者组。 它用于筛选 Azure 流分析中的结果。 例如，如果我们想要获取整个流中的所有内容，或者数据通过设置为 **Contoso** 的 consumer_group 传入，则我们可将 Azure 流分析流（和 Power BI 报表）设置为仅显示这些条目。 本教程的第 2 部分将使用此字段。

**sku_name**：此字段是 IoT 中心的规模。 此值必须为 S1 或更高；免费层不适用于本教程，因为它不允许多个终结点。

**sku_units**：此字段类似于 **sku_name**，表示可以使用的 IoT 中心单位数。

**d2c_partitions**：此字段表示用于事件流的分区数。

**storageAccountName_in**：此字段是要创建的存储帐户的名称。 消息将路由到存储帐户中的容器。 此字段与 randomValue 相连接，使名称全局唯一。

**storageContainerName**：此字段是路由到存储帐户的消息的存储容器。

**storage_endpoint**：此字段是消息路由使用的存储帐户终结点的名称。

**service_bus_namespace_in**：此字段是要创建的服务总线命名空间的名称。 此值与 randomValue 相连接，使名称全局唯一。

**service_bus_queue_in**：此字段是用于路由消息的服务总线队列的名称。 此值与 randomValue 相连接，使名称全局唯一。

**AuthRules_sb_queue**：此字段是服务总线队列的授权规则，用于检索队列的连接字符串。

### <a name="variables"></a>变量

这些值将在模板中使用，往往派生自参数。

**queueAuthorizationRuleResourceId**：此字段是服务总线队列的授权规则的 ResourceId。 而 ResourceId 用于检索队列的连接字符串。

**iotHubName**：此字段是连接 randomValue 后的 IoT 中心名称。 

**storageAccountName**：此字段是连接 randomValue 后的存储帐户名称。 

**service_bus_namespace**：此字段是连接 randomValue 后的命名空间。

**service_bus_queue**：此字段是连接 randomValue 后的服务总线队列名称。

**sbVersion**：要使用的服务总线 API 版本。 在本例中为“2017-04-01”。

### <a name="resources-storage-account-and-container"></a>资源：存储帐户和容器

创建的第一个资源是存储帐户，以及消息要路由到的容器。 容器是存储帐户下的资源。 容器具有存储帐户的 `dependsOn` 子句，因此，必须先创建存储帐户，再创建容器。

下面是此节的外观：

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>资源：服务总线命名空间和队列

创建的第二个资源是服务总线命名空间，以及消息要路由到的服务总线队列。 SKU 设置为“标准”。 从变量中检索 API 版本。 此资源设置为在部署此节时激活服务总线命名空间 (status:Active)。 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

此节创建服务总线队列。 此脚本部分包含 `dependsOn` 子句，用于确保先创建命名空间，再创建队列。

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>资源：IoT 中心和消息路由

创建存储帐户和服务总线队列后，可以创建要将消息路由到的 IoT 中心。 RM 模板使用 `dependsOn` 子句，因此，它不会在创建服务总线资源和存储帐户之前尝试创建中心。 

下面是 IoT 中心节的第一个部分。 此模板部分设置依赖项，以属性开头。

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

下一节是 Iot 中心的消息路由配置节。 首先是终结点节。 此模板部分设置服务总线队列和存储帐户的路由终结点，包括连接字符串。

若要创建队列的连接字符串，需要使用内联检索的 queueAuthorizationRulesResourcedId。 若要创建存储帐户的连接字符串，需要检索主存储密钥，然后在连接字符串的格式中使用该密钥。

也需要在终结点配置中将 Blob 格式设置为 `AVRO` 或 `JSON`。

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

下一节是用于将消息路由到终结点。 每个终结点存在一项设置，因此，服务总线队列和存储帐户容器各有一项设置。

请记住，路由到存储的消息的查询条件是 `level="storage"`，路由到服务总线队列的消息的查询条件是 `level="critical"`。

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

此 JSON 演示了 IoT 中心节的余下内容，其中包含默认信息和中心的 SKU。

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>资源：服务总线队列授权规则

服务总线队列授权规则用于检索服务总线队列的连接字符串。 它使用 `dependsOn` 子句来确保先创建服务总线命名空间和服务总线队列，然后再创建授权规则。

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>资源：使用者组

在此节中，创建 Azure 流分析在本教程第二部分使用的 IoT 中心数据的使用者组。

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>资源：Outputs

若要将值发回到部署脚本以供显示，请使用 output 节。 此模板部分返回服务总线队列的连接字符串。 不一定要返回值；包含值的目的是通过示例演示如何向调用方脚本返回结果。

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>部署 RM 模板

若要将模板部署到 Azure，请将模板和参数文件上传到 Azure Cloud Shell，然后执行某个脚本来部署模板。 打开 Azure Cloud Shell 并登录。 本示例使用 PowerShell。

若要上传文件，请在菜单栏中选择“上传/下载文件”图标，然后选择“上传”。 

![Cloud Shell 菜单栏，其中已突出显示“上传/下载文件”](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

使用弹出的文件资源管理器找到本地磁盘上的文件并将其选中，然后选择“打开”。 

上传文件后，结果对话框将显示下图所示的内容。

![Cloud Shell 菜单栏，其中已突出显示“上传/下载文件”](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

文件将上传到 Cloud Shell 实例使用的共享。 

运行脚本以执行部署。 此脚本的最后一行检索设置返回的变量 - 服务总线队列连接字符串。

该脚本设置并使用以下变量：

**$RGName** 是模板要部署到的资源组名称。 此字段是在部署模板之前创建的。

**$location** 是模板使用的 Azure 位置，例如“westus”。

**deploymentname** 是分配给部署的名称，用于检索返回变量值。

下面是 PowerShell 脚本。 请复制此 PowerShell 脚本并将其粘贴到 Cloud Shell 窗口中，然后按 Enter 运行它。

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

如果遇到脚本错误，可在本地编辑脚本，再次将其上传到 Cloud Shell，然后再次运行该脚本。 脚本成功完成运行后，请继续下一步。

## <a name="create-simulated-device"></a>创建模拟设备

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>在门户中查看消息路由

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>后续步骤

设置所有资源并配置消息路由后，请继续学习下一篇教程，了解如何处理和显示有关路由的消息的信息。

> [!div class="nextstepaction"]
> [第 2 部分 - 查看消息路由结果](tutorial-routing-view-message-routing-results.md)
