---
title: 如何克隆 Azure IoT 中心
description: 如何克隆 Azure IoT 中心
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429150"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>如何将 Azure IoT 中心克隆到另一个区域

本文探讨了克隆 IoT 中心的方法，并提供了开始之前需要回答的一些问题。 下面是可能需要克隆 IoT 中心的几个原因：
 
* 你要将公司从一个区域移到另一个区域（如从欧洲到北美（反之亦然），并且你希望你的资源和数据地理位置接近于你的新位置，因此你需要移动中心。

* 正在为开发和生产环境设置集线器。

* 你要执行多集线器高可用性的自定义实现。 有关详细信息，请参阅[IoT 中心高可用性和灾难恢复的如何实现跨区域 HA 部分](iot-hub-ha-dr.md#achieve-cross-region-ha)。

* 要增加为中心配置的[分区](iot-hub-scaling.md#partitions)数。 这是首次创建你的中心时设置的，无法更改。 你可以使用本文中的信息来克隆中心，并在创建克隆后增加分区数。

若要克隆集线器，你需要具有对原始集线器的管理访问权限的订阅。 你可以将新的中心放在与原始集线器相同的订阅中，或者甚至在新的订阅中。 你不能使用相同的名称，因为中心名称必须是全局唯一的。

> [!NOTE]
> 目前，没有任何功能可用于自动克隆 IoT 中心。 它主要是一个手动过程，因此非常容易出错。 克隆集线器的复杂性与中心的复杂程度直接成比例。 例如，克隆没有消息路由的 IoT 中心非常简单。 如果只是将消息路由添加为一个复杂性，则克隆中心就会成为至少一个数量级。 如果还移动用于路由终结点的资源，则 magniture 的另一顺序就是另一种。 

## <a name="things-to-consider"></a>注意事项

在克隆 IoT 中心之前，需要考虑几个事项。

* 请确保原始位置中所有可用的功能在新位置中也可用。 某些服务处于预览阶段，并非所有功能都可在任何位置使用。

* 请不要在创建和验证克隆版本之前删除原始资源。 一旦删除了某个集线器，就不会被永久删除，并且无法恢复它来检查设置或数据，以确保中心复制正确。

* 许多资源需要全局唯一的名称，因此必须为克隆的版本使用不同的名称。 还应为克隆中心所属的资源组使用不同的名称。 

* 不迁移原始 IoT 中心的数据。 这包括遥测消息、云到设备（C2D）命令以及与作业相关的信息（如计划和历史记录）。 也不会迁移指标和日志记录结果。 

* 对于路由到 Azure 存储的数据或消息，可以将数据保留在原始存储帐户中，将该数据传输到新区域中的新存储帐户，或者将旧数据保留在新位置，并在新的位置创建新的存储帐户。 有关如何在 Blob 存储中移动数据的详细信息，请参阅[AzCopy 入门](../storage/common/storage-use-azcopy-v10.md)。

* 无法迁移事件中心和服务总线主题和队列的数据。 这是时间点数据，处理消息后不会存储这些数据。

* 需要计划停机时间进行迁移。 将设备克隆到新集线器需要一定的时间。 如果你使用的是导入/导出方法，基准测试表明，移动500000设备可能需要大约两个小时，而移动一百万台设备需要四个小时。 

* 可以将设备复制到新中心，而无需关闭或更改设备。 

    * 如果设备最初是使用 DPS 预配的，则重新预配它们会更新每个设备中存储的连接信息。 
    
    * 否则，你必须使用导入/导出方法来移动设备，然后需要修改设备以使用新的中心。 例如，你可以将设备设置为使用来自克隆的所需属性中的 IoT 中心主机名。 设备将采用该 IoT 中心主机名，从旧集线器断开连接设备，并将其重新连接到新的集线器。
    
* 你需要更新你使用的任何证书，以便可以将它们用于新资源。 此外，你可能已在某个位置的 DNS 表中定义了中心，你将需要更新 DNS 信息。

## <a name="methodology"></a>方法

这是我们建议将 IoT 中心从一个区域移到另一个区域的一般方法。 对于消息路由，这假设资源不会移动到新区域。 有关详细信息，请参阅[有关消息路由的部分](#how-to-handle-message-routing)。

   1. 将中心及其设置导出到资源管理器模板。 
   
   1. 对模板进行必要的更改，如更新所有出现的名称和克隆的中心的位置。 对于用于消息路由终结点的模板中的任何资源，请为该资源更新模板中的密钥。
   
   1. 将模板导入到新位置中的新资源组。 这会创建克隆。

   1. 根据需要进行调试。 
   
   1. 添加未导出到模板的任何内容。 
   
       例如，使用者组不会导出到模板。 需要手动将使用者组添加到模板，或者在创建了中心后使用[Azure 门户](https://portal.azure.com)。 [使用 Azure 资源管理器模板来配置 IoT 中心消息路由](tutorial-routing-config-message-routing-rm-template.md)一文中的模板中添加了一个使用者组的示例。
       
   1. 将设备从原始中心复制到克隆。 这将在[管理注册到 IoT 中心的设备](#managing-the-devices-registered-to-the-iot-hub)部分中介绍。

## <a name="how-to-handle-message-routing"></a>如何处理消息路由

如果你的中心使用[自定义路由](iot-hub-devguide-messages-read-custom.md)，则导出中心的模板将包含路由配置，但不包括资源本身。 你必须选择是将路由资源移到新位置还是将其保留原样，并按原样继续使用它们。 

例如，假设你在美国西部有一个将邮件路由到存储帐户的中心（也在美国西部），并且你想要将中心移动到美国东部。 你可以移动中心，并让它仍将消息路由到美国西部的存储帐户，也可以移动中心并移动存储帐户。 将消息路由到不同区域中的终结点资源可能会导致性能下降。

如果还不移动用于路由终结点的资源，则可以轻松地移动使用消息路由的中心。 

如果中心使用消息路由，则有两个选择。 

1. 将用于路由终结点的资源移动到新位置。

    * 你必须在[Azure 门户](https://portal.azure.com)中或者通过使用资源管理器模板，自行创建新的资源。 

    * 在新位置中创建资源时，必须重命名所有资源，因为这些资源具有全局唯一名称。 
     
    * 在创建新的中心之前，必须更新新的中心模板中的资源名称和资源密钥。 创建新的中心时，应存在资源。

1. 请勿移动用于路由终结点的资源。 "就地" 使用它们。

   * 在编辑模板的步骤中，您需要检索每个路由资源的密钥并将其放入模板中，然后再创建新的中心。 

   * 集线器仍引用原始路由资源，并按配置将消息路由到这些资源。

   * 由于中心和路由终结点资源不在同一位置，因此会降低性能。

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>准备将中心迁移到另一个区域

本部分提供有关迁移中心的特定说明。

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>查找原始中心并将其导出到资源模板。

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 转到 "**资源组**"，然后选择包含要移动的中心的资源组。 你还可以前往**资源**并找到中心。 选择中心。

1. 从中心的属性和设置列表中选择 "**导出模板**"。 

   ![显示用于导出 IoT 中心模板的命令的屏幕截图。](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. 选择 "**下载**" 以下载模板。 将该文件保存到你可以再次查找的位置。 

   ![显示用于下载 IoT 中心模板的命令的屏幕截图。](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>查看模板 

1. 中转到 "下载" 文件夹（或导出模板时使用的任何文件夹）并找到 zip 文件。 打开 zip 文件，找到名为 `template.json`的文件。 选择它，然后按 Ctrl + C 复制模板。 中转到不在 zip 文件中的其他文件夹，然后粘贴文件（Ctrl + V）。 现在可以对其进行编辑。
 
    以下示例适用于没有路由配置的泛型中心。 它是在区域**westus**中名为**ContosoTestHub29358**的 S1 层中心（具有1个单元）。 下面是已导出的模板。

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
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
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>编辑模板 

必须先进行一些更改，然后才能使用该模板在新区域中创建新的中心。 使用[VS Code](https://code.visualstudio.com)或文本编辑器编辑模板。

#### <a name="edit-the-hub-name-and-location"></a>编辑中心名称和位置

1. 删除顶部的 parameters 节，只需使用中心名称，因为我们不会有多个参数。 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. 更改该名称以使用实际（新）名称，而不是从参数（在上一步中删除）中检索它。 

    对于新中心，请使用原始集线器的名称加上字符串*克隆*来构成新名称。 首先清理集线器名称和位置。
    
    旧版本：

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    新版本： 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    接下来，你将发现**path**的值包含旧的中心名称。 更改为使用新的更改。 下面是**eventHubEndpoints**中的路径值： "**事件**" 和 " **OperationsMonitoringEvents**"。

    完成后，"事件中心终结点" 部分应如下所示：

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>更新未移动的路由资源的密钥

导出已配置路由的集线器的资源管理器模板时，会看到导出的模板中未提供这些资源的密钥，它们的位置由星号表示。 在导入新的中心模板并创建中心**之前**，必须通过转到门户中的这些资源并检索密钥来填充它们。 

1. 检索任意路由资源所需的密钥，并将其放入模板。 可以从[Azure 门户](https://portal.azure.com)中的资源检索密钥。 

   例如，如果要将消息路由到存储容器，请在门户中找到存储帐户。 在 "设置" 部分下，选择 "**访问密钥**"，然后复制其中一个密钥。 第一次导出模板时，关键如下：

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. 检索存储帐户的帐户密钥后，请将其放在子句的模板中 `AccountKey=****` 星号的位置。 

1. 对于服务总线队列，获取与 SharedAccessKeyName 匹配的共享访问密钥。 下面是 json 中的密钥和 `SharedAccessKeyName`：

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. 对于服务总线主题和事件中心连接也是如此。

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>在新位置创建新的路由资源

本部分仅适用于移动中心用于路由终结点的资源。

如果要移动路由资源，则必须在新位置手动设置资源。 您可以使用[Azure 门户](https://portal.azure.com)创建路由资源，也可以通过导出消息路由使用的每个资源的资源管理器模板，对其进行编辑，并导入它们。 设置资源后，可以导入中心的模板（包括路由配置）。

1. 创建路由使用的每个资源。 您可以使用[Azure 门户](https://portal.azure.com)手动执行此操作，也可以使用资源管理器模板创建资源。 如果要使用模板，请执行以下步骤：

    1. 对于路由使用的每个资源，将其导出到资源管理器的模板。
    
    1. 更新资源的名称和位置。 

    1. 更新资源之间的任何交叉引用。 例如，如果为新的存储帐户创建模板，则需要更新该模板中的存储帐户名称以及引用该模板的其他任何模板。 在大多数情况下，中心模板中的路由部分是唯一引用资源的其他模板。 

    1. 导入每个模板，每个模板部署每个资源。

    一旦路由使用的资源已设置并正在运行，就可以继续。

1. 在 IoT 中心的模板中，将每个路由资源的名称更改为新名称，并在需要时更新位置。 

现在，你有了一个模板，该模板将创建一个与旧集线器大致完全相同的新中心，具体取决于你决定如何处理路由。

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>移动--通过加载模板在新区域中创建新的中心

使用模板在新位置创建新的中心。 如果要移动的路由资源，应在新位置设置资源，并更新模板中的引用以使其匹配。 如果不移动路由资源，它们应该位于带有更新密钥的模板中。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“创建资源”。 

1. 在搜索框中，放入 "模板部署"，然后选择 Enter。

1. 选择 "**模板部署" （使用自定义模板进行部署）** 。 这会将你带到模板部署的屏幕上。 选择“创建”。 你会看到如下屏幕：

   ![显示用于构建你自己的模板的命令的屏幕截图](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. 选择 **"在编辑器中生成自己的模板**"，这使你可以从文件上传模板。 

1. 选择 "**加载文件**"。 

   ![显示用于上载模板文件的命令的屏幕截图](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. 浏览你编辑的新模板并将其选中，然后选择 "**打开**"。 它在编辑窗口中加载模板。 选择“保存”。 

   ![显示加载模板的屏幕截图](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. 在以下字段中进行填充。

   **订阅**：选择要使用的订阅。

   **资源组**：在新位置创建新的资源组。 如果已设置新的设置，可以选择它而不是创建一个新的。

   **位置**：如果选择了现有的资源组，则会填写此资源组，以便与资源组的位置匹配。 如果创建了新的资源组，则会是其位置。

   "**我同意" 复选框**：这基本上表明你同意为正在创建的资源付费。

1. 选择“购买”按钮。

门户现在会验证模板并部署克隆的中心。 如果你具有路由配置数据，则该数据将包含在新的中心，但会指向以前位置中的资源。

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>管理注册到 IoT 中心的设备

既然已启动并运行克隆，接下来需要将所有设备从原始中心复制到克隆。 

可以通过多种方式来实现此目的。 你最初使用的是[设备预配服务（DPS）](/azure/iot-dps/about-iot-dps)来预配设备，或者你未使用。 否则，这并不是很困难。 否则，这可能非常复杂。 

如果未使用 DPS 来预配设备，则可以跳过下一部分，开始[使用导入/导出将设备移到新中心](#using-import-export-to-move-the-devices-to-the-new-hub)。

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>使用 DPS 重新设置新集线器中的设备

若要使用 DPS 将设备移动到新位置，请参阅[如何重新设置设备](../iot-dps/how-to-reprovision.md)。 完成后，可以查看[Azure 门户](https://portal.azure.com)中的设备，并验证它们是否在新位置。

使用[Azure 门户](https://portal.azure.com)中转到新中心。 选择中心，然后选择 " **IoT 设备**"。 你会看到重新预配到克隆的中心的设备。 你还可以查看克隆的中心的属性。 

如果已实现路由，请测试并确保将消息正确路由到资源。

### <a name="committing-the-changes-after-using-dps"></a>使用 DPS 后提交更改

此更改已由 DPS 服务提交。

### <a name="rolling-back-the-changes-after-using-dps"></a>使用 DPS 后回滚更改。 

如果要回滚更改，请将设备从新的中心重新预配到旧的中心。

你现在已经完成了中心及其设备的迁移。 你可以跳到[清除](#clean-up)。

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>使用导入/导出将设备移到新集线器

应用程序以 .NET Core 为目标，因此你可以在 Windows 或 Linux 上运行它。 您可以下载示例，检索连接字符串，设置要运行的位的标志，并运行它。 无需打开代码即可执行此操作。

### <a name="downloading-the-sample"></a>下载示例

1. 使用本页中C#的 iot 示例：[用于的C#Azure iot 示例](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)。 下载 zip 文件并将其解压缩到计算机上。 

1. 相关代码在中。/iot-hub/Samples/service/ImportExportDevicesSample。 若要运行应用程序，无需查看或编辑代码。

1. 若要运行应用程序，请指定三个连接字符串和五个选项。 您可以在中将此数据作为命令行参数传递，也可以使用环境变量，或使用这两者的组合。 我们将以命令行参数的形式传递选项，并将连接字符串作为环境变量传递。 

   原因在于，连接字符串很长且难看，但可能不会更改，但你可能想要更改这些选项，并多次运行该应用程序。 若要更改环境变量的值，必须关闭命令窗口和 Visual Studio 或 VS Code，无论使用哪种。 

### <a name="options"></a>选项

下面是运行应用程序时指定的五个选项。 我们将在命令行上将其放在一分钟内。

*   **addDevices** （参数1）-如果要添加为你生成的虚拟设备，请将此项设置为 true。 这些将添加到源中心。 同时，设置**numToAdd** （参数2）以指定要添加多少设备。 可向中心注册的设备的最大数目为1000000。此选项的目的是用于测试--可以生成特定数量的设备，然后将其复制到另一个中心。

*   **copyDevices** （参数3）--将此项设置为 true，以将设备从一个中心复制到另一个中心。 

*   **deleteSourceDevices** （参数4）--将此项设置为 true 以删除注册到源集线器的所有设备。 建议等待等待，直到在你确定所有设备都已传输完毕后才运行。 删除设备后，将无法恢复。

*   **deleteDestDevices** （参数5）-将此项设置为 true 以删除注册到目标中心（克隆）的所有设备。 如果要多次复制设备，则可能需要执行此操作。 

基本命令将*dotnet 运行*，这会告诉 .net 生成本地 .csproj 文件，然后运行该文件。 在运行命令行参数之前，请先将其添加到末尾。 

你的命令行将类似于以下示例：

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>使用连接字符串的环境变量

1. 若要运行该示例，需要将连接字符串连接到新旧的 IoT 中心，并连接到可用于临时工作文件的存储帐户。 我们会在环境变量中存储这些值。

1. 若要获取连接字符串值，请登录到[Azure 门户](https://portal.azure.com)。 

1. 将连接字符串放在可以检索它们的某个位置，例如记事本。 如果复制以下项，则可以将连接字符串直接粘贴到它们所在的位置。 不要在等号周围添加空格，否则将更改变量名称。 此外，连接字符串两侧不需要双引号。 如果将存储帐户连接字符串括在引号中，则它将不起作用。

   对于 Windows，这是设置环境变量的方式：

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   对于 Linux，这是定义环境变量的方式：

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. 对于 IoT 中心连接字符串，请在门户中中转到每个中心。 可以在**资源**中搜索中心。 如果知道资源组，则可以选择 "**资源**组"，选择资源组，然后从该资源组中的资产列表中选择该中心。 

1. 从中心的 "设置" 中选择 "**共享访问策略**"，然后选择 " **iothubowner** " 并复制其中一个连接字符串。 对目标中心执行相同的操作。 将它们添加到相应的 SET 命令。

1. 对于存储帐户连接字符串，请在 "**资源**" 中或在其**资源组**下查找存储帐户，然后将其打开。 
   
1. 在 "设置" 部分下，选择 "**访问密钥**"，然后复制其中一个连接字符串。 将连接字符串放在文本文件中，获取适当的 SET 命令。 

现在，使用 SET 命令在文件中设置了环境变量，并且知道了命令行参数。 让我们运行该示例。

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>运行示例应用程序并使用命令行参数

1. 打开命令提示符窗口。 选择 "Windows"，然后键入 `command prompt` 以获取命令提示符窗口。

1. 复制设置环境变量的命令，并将其粘贴到命令提示符窗口，然后选择 Enter。 完成后，在 "命令提示符" 窗口中键入 `SET` 以查看环境变量及其值。 一旦将这些文件复制到命令提示符窗口中，就不必再次复制它们，除非您打开新的命令提示符窗口。

1. 在命令提示符窗口中，将目录更改到/ImportExportDevicesSample （其中 ImportExportDevicesSample 文件存在）。 然后键入以下命令，并包含命令行参数。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Dotnet 命令生成并运行应用程序。 由于你在运行应用程序时将传入选项，因此你可以在每次运行应用程序时更改这些选项的值。 例如，你可能想要运行一次，并创建新设备，然后再次运行该设备，并将这些设备复制到新集线器上，等等。 你还可以在同一运行中执行所有步骤，不过，在你确定完成克隆的操作之前，我们建议不要删除任何设备。 下面是创建1000设备的示例，然后将其复制到另一个中心。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    验证设备是否已成功复制后，可以从源集线器删除设备，如下所示：

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>使用 Visual Studio 运行示例应用程序

1. 如果要在 Visual Studio 中运行应用程序，请将当前目录更改为 IoTHubServiceSamples 文件所在的文件夹。 然后在命令提示符窗口中运行以下命令，在 Visual Studio 中打开解决方案。 您必须在设置环境变量的同一个命令窗口中执行此操作，因此这些变量是已知的。

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. 右键单击 "项目*ImportExportDevicesSample* "，然后选择 "**设为启动项目**"。    
    
1. 在 ImportExportDevicesSample 文件夹中，为五个选项设置 Program.cs 顶部的变量。

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. 选择 F5 以运行应用程序。 完成运行后，可以查看结果。

### <a name="view-the-results"></a>查看结果 

你可以查看[Azure 门户](https://portal.azure.com)中的设备，并验证它们是否在新位置。

1. 使用[Azure 门户](https://portal.azure.com)中转到新中心。 选择中心，然后选择 " **IoT 设备**"。 你会看到刚从旧集线器复制到克隆中心的设备。 你还可以查看克隆的中心的属性。 

1. 通过转到[Azure 门户](https://portal.azure.com)中的 Azure 存储帐户，并在 `devicefiles` 容器中查找 `ImportErrors.log`，检查导入/导出错误。 如果此文件为空（大小为0），则没有任何错误。 如果尝试多次导入同一设备，它会在第二次拒绝设备，并将错误消息添加到日志文件。

### <a name="committing-the-changes"></a>提交更改 

此时，你已将中心复制到新位置，并将设备迁移到了新克隆。 现在，你需要进行更改，以便设备与克隆的集线器一起工作。

若要提交更改，请执行以下步骤： 

* 更新每个设备以更改 IoT 中心主机名，使 IoT 中心主机名指向新的中心。 应使用首次预配设备时使用的相同方法执行此操作。

* 更改引用旧集线器的所有应用程序，使之指向新的中心。

* 完成后，新的中心应启动并运行。 旧集线器应该没有活动设备，并且处于断开连接状态。 

### <a name="rolling-back-the-changes"></a>正在回滚更改

如果决定回滚这些更改，请执行以下步骤：

* 更新每个设备以更改 IoT 中心主机名，使其指向旧集线器的 IoT 中心主机名。 应使用首次预配设备时使用的相同方法执行此操作。

* 更改引用新中心所需的所有应用程序，使之指向旧的中心。 例如，如果你使用的是 Azure 分析，则可能需要重新配置[Azure 流分析输入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。

* 删除新的中心。 

* 如果你有路由资源，则旧集线器上的配置仍会指向正确的路由配置，并且应在重新启动集线器后使用这些资源。

### <a name="checking-the-results"></a>检查结果 

若要检查结果，请更改 IoT 解决方案，使其指向新位置的中心，并运行它。 换而言之，请对使用以前的集线器执行的新中心执行相同的操作，并确保它们正常工作。 

如果已实现路由，请测试并确保将消息正确路由到资源。

## <a name="clean-up"></a>清理

请不要清除，直到确实确定新的中心已启动并运行，并且设备工作正常。 如果使用该功能，还请务必测试路由。 准备就绪后，请执行以下步骤来清理旧资源：

* 如果尚未这样做，请删除旧的中心。 这会从中心删除所有活动的设备。

* 如果你已将移动的资源路由到新位置，则可以删除旧的路由资源。

## <a name="next-steps"></a>后续步骤

已将 IoT 中心克隆到新区域中的新中心，并完成设备。 有关对 IoT 中心内的标识注册表执行批量操作的详细信息，请参阅[批量导入和导出 Iot 中心设备标识](iot-hub-bulk-identity-mgmt.md)。

有关用于中心的 IoT 中心和开发的详细信息，请参阅以下文章。

* [IoT 中心开发人员指南](iot-hub-devguide.md)

* [IoT 中心路由教程](tutorial-routing.md)

* [IoT 中心设备管理概述](iot-hub-device-management-overview.md)

* 若要部署示例应用程序，请参阅[.Net Core 应用程序部署](https://docs.microsoft.com/dotnet/core/deploying/index)。
