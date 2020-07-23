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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75429150"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>如何将 Azure IoT 中心克隆到另一个区域

本文探讨克隆 IoT 中心的方法，并提供在开始之前需要回答的一些问题。 下面是克隆 IoT 中心的几个可能原因：
 
* 你要将公司从一个区域移到另一个区域（如从欧洲到北美（反之亦然），并且你希望你的资源和数据地理位置接近于你的新位置，因此你需要移动中心。

* 要为开发环境和生产环境设置中心。

* 要以自定义方式实现多中心高可用性。 有关详细信息，请参阅[“IoT 中心高可用性和灾难恢复”中的“如何实现跨区域高可用性”部分](iot-hub-ha-dr.md#achieve-cross-region-ha)。

* 需要增加为中心配置的[分区](iot-hub-scaling.md#partitions)数。 此数目是首次创建中心时设置的，以后无法更改。 可以使用本文所述的信息来克隆中心，并在创建克隆时增加分区数。

若要克隆中心，需要一个对原始中心拥有管理访问权限的订阅。 可将新的中心放到新的资源组和区域、原始中心所在的同一订阅甚至新的订阅中。 不能使用相同的名称，因为中心名称必须全局唯一。

> [!NOTE]
> 目前，没有可用于自动克隆 IoT 中心的功能。 克隆主要是一个手动过程，因此非常容易出错。 克隆中心的复杂性与中心的复杂性直接相关。 例如，克隆不带消息路由的 IoT 中心就非常简单。 如果添加消息路由，这看上去只是在一个方面提高了复杂性，但克隆中心的复杂性至少会提高一个数量级。 如果同时还要移动用于路由终结点的资源，则复杂性又会提高一个数量级。 

## <a name="things-to-consider"></a>注意事项

在克隆 IoT 中心之前，需要考虑几个要点。

* 确保原始位置中所有可用的功能在新位置中也可用。 某些服务以预览版提供，因此并非所有功能都可以在任何位置使用。

* 在创建和验证已克隆的版本之前不要删除原始资源。 一旦删除某个中心，就会将其永久删除，并且没有任何办法可以恢复，因此也就无法检查设置或数据来确保复制正确中心。

* 许多资源需要全局唯一的名称，因此必须对克隆的版本使用不同的名称。 此外，应该对克隆的中心所属的资源组使用不同的名称。 

* 原始 IoT 中心的数据不会迁移。 这包括遥测消息、云到设备 (C2D) 的命令，以及作业相关的信息，例如计划和历史记录。 指标和日志记录结果也不会迁移。 

* 对于路由到 Azure 存储的数据或消息，可将数据保留在原始存储帐户中，将该数据传输到新区域中的新存储帐户，或者将旧数据保留在原位，并在新位置为新数据创建新存储帐户。 有关在 Blob 存储中移动数据的详细信息，请参阅 [AzCopy 入门](../storage/common/storage-use-azcopy-v10.md)。

* 无法迁移事件中心以及服务总线主题和队列的数据。 这些数据属于时间点数据，在处理消息后不会存储。

* 需要针对迁移造成的停机时间做好安排。 将设备克隆到新中心需要花费一定的时间。 如果使用导入/导出方法，基准测试表明，移动 500,000 个设备可能需要大约两个小时，而移动 100 万个设备大约需要四个小时。 

* 无需关闭或更改设备，即可将设备复制到新中心。 

    * 如果设备最初是使用 DPS 预配的，则重新预配它们会更新每个设备中存储的连接信息。 
    
    * 否则，必须使用导入/导出方法来移动设备，然后必须修改设备才能使用新中心。 例如，可将设备设置为使用孪生所需属性中的 IoT 中心主机名。 设备将采用该 IoT 中心主机名，从旧中心断开设备的连接，然后将设备重新连接到新中心。
    
* 需要更新所用的任何证书，以便可将其用于新资源。 此外，你可能已在 DNS 表中的某个位置定义了中心 — 在这种情况下，需要更新这些 DNS 信息。

## <a name="methodology"></a>方法

下面是我们建议的将 IoT 中心从一个区域移到另一个区域的常规方法。 对于消息路由，此方法假设不会将资源移到新区域。 有关详细信息，请参阅[有关消息路由的部分](#how-to-handle-message-routing)。

   1. 将中心及其设置导出到资源管理器模板。 
   
   1. 对模板进行所需的更改，例如，更新克隆的中心的所有现有名称和位置。 对于模板中用于消息路由终结点的任何资源，请在模板中更新该资源的密钥。
   
   1. 将模板导入到位于新位置的新资源组。 这会创建克隆。

   1. 请根据需要进行调试。 
   
   1. 添加未导出到模板中的任何内容。 
   
       例如，使用者组就不会导出到模板中。 需要手动将使用者组添加到模板中，或者在创建中心后使用 [Azure 门户](https://portal.azure.com)来添加。 [使用 Azure 资源管理器模板配置 IoT 中心消息路由](tutorial-routing-config-message-routing-rm-template.md)一文提供了将一个使用者组添加到模板的示例。
       
   1. 将原始中心内的设备复制到克隆中。 [管理已注册到 IoT 中心的设备](#managing-the-devices-registered-to-the-iot-hub)部分介绍了此操作。

## <a name="how-to-handle-message-routing"></a>如何处理消息路由

如果中心使用[自定义路由](iot-hub-devguide-messages-read-custom.md)，则导出中心模板的过程涉及路由配置，但不涉及资源本身。 必须选择是要将路由资源移到新位置，还是将其保留在原地，并“按原样”继续使用它们。 

例如，假设你在美国西部的一个中心会将消息路由到同样位于美国西部的存储帐户，而你希望将中心移到美国东部。 可以移动该中心，并让它继续将消息路由到美国西部的存储帐户，或者，可以同时移动该中心和存储帐户。 将消息路由到不同区域中的终结点资源可能会导致性能轻微下降。

如果不同时移动用于路由终结点的资源，则移动使用消息路由的中心就相当简单。 

如果中心使用消息路由，你可以采用两种做法。 

1. 将用于路由终结点的资源移到新位置。

    * 必须在 [Azure 门户](https://portal.azure.com)中或通过资源管理器模板自行手动创建新资源。 

    * 在新位置创建资源时，必须重命名所有资源，因为它们具有全局唯一的名称。 
     
    * 在创建新中心之前，必须在新中心的模板中更新资源名称和资源密钥。 创建新中心时，这些资源应该存在。

1. 不要移动用于路由终结点的资源。 请“在原地”使用这些资源。

   * 在编辑模板的步骤中，需要检索每个路由资源的密钥并将其放入模板，然后再创建新中心。 

   * 中心仍引用原始路由资源，并按配置将消息路由到这些资源。

   * 由于中心和路由终结点资源不在同一位置，因此性能会略微下降。

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>准备将中心迁移到另一个区域

本部分提供有关迁移中心的具体说明。

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>找到原始中心并将其导出到资源模板。

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 转到“资源组”，选择包含所要移动的中心的资源组。  也可以转到“资源”并找到该中心。  选择该中心。

1. 从中心的属性和设置列表中选择“导出模板”。  

   ![显示用于导出 IoT 中心模板的命令的屏幕截图。](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. 选择“下载”以下载模板。  将文件保存到可以再次找到的某个位置。 

   ![显示用于下载 IoT 中心模板的命令的屏幕截图。](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>查看模板 

1. 转到“下载”文件夹（或导出模板时使用的任何文件夹），并找到下载的 zip 文件。 打开该 zip 文件，找到名为 `template.json` 的文件。 选择该文件，然后按 Ctrl+C 复制模板。 转到不在 zip 文件中的另一文件夹，然后粘贴该文件 (Ctrl+V)。 现在可对其进行编辑。
 
    以下示例适用于没有路由配置的一般中心。 它是 **westus** 区域中名为 **ContosoTestHub29358** 的一个 S1 层中心（具有 1 个单元）。 下面是导出的模板。

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

必须先进行一些更改，然后才能使用该模板在新区域中创建新中心。 使用 [VS Code](https://code.visualstudio.com) 或文本编辑器来编辑该模板。

#### <a name="edit-the-hub-name-and-location"></a>编辑中心名称和位置

1. 删除顶部的 parameters 节 -- 只使用中心名称可以大大简化操作，因为我们不会使用多个参数。 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. 更改名称以使用实际（新）名称，而无需从参数（在上一步骤中已删除参数）中检索该名称。 

    对于新中心，请使用原始中心的名称加上字符串 *clone* 来构成新的名称。 首先清理中心名称和位置。
    
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

    接下来，你会发现 **path** 的值包含旧中心的名称。 请将这些值更改为使用新名称。 下面是 **eventHubEndpoints** 下的名为 **events** 和 **OperationsMonitoringEvents** 的路径值。

    完成后，事件中心终结点节应如下所示：

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

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>更新不移动的路由资源的密钥

导出已配置路由的中心的资源管理器模板时，将会看到，导出的模板中并未提供这些资源的密钥 -- 这些密钥所在的位置由星号代替。 在导入新中心的模板并创建该中心**之前**，必须在门户中转到这些资源并检索密钥，然后填充密钥。 

1. 检索所有路由资源所需的密钥，并将其放到模板中。 可以从 [Azure 门户](https://portal.azure.com)中的资源检索密钥。 

   例如，如果要将消息路由到某个存储容器，请在门户中找到相应的存储帐户。 在“设置”部分下选择“访问密钥”，然后复制其中的一个密钥。  首次导出模板时，密钥如下所示：

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. 检索存储帐户的帐户密钥后，请将其输入到模板上的子句 `AccountKey=****` 中，并取代星号。 

1. 对于服务总线队列，获取与 SharedAccessKeyName 匹配的共享访问密钥。 下面是 JSON 中的密钥和 `SharedAccessKeyName`：

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. 上述操作对于服务总线主题和事件中心连接也适用。

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>在新位置创建新的路由资源

仅当移动中心对路由终结点使用的资源时，本部分的内容才适用。

若要移动路由资源，必须在新位置手动设置资源。 可以使用 [Azure 门户](https://portal.azure.com)创建路由资源，或者导出消息路由使用的每个资源的资源管理器模板，对其进行编辑，然后导入此模板。 设置资源后，可以导入中心的模板（包括路由配置）。

1. 创建路由使用的每个资源。 可以使用 [Azure 门户](https://portal.azure.com)手动执行此操作，也可以使用资源管理器模板创建资源。 若要使用模板，请执行以下步骤：

    1. 对于路由使用的每个资源，请将其导出到资源管理器模板。
    
    1. 更新资源的名称和位置。 

    1. 更新资源之间的任何交叉引用。 例如，如果为新存储帐户创建模板，则需要更新该模板中的存储帐户名称，以及引用该名称的其他任何模板。 在大多数情况下，只有中心模板中的 routing 节才是引用资源的模板。 

    1. 导入每个模板，以部署每个资源。

    设置并运行路由使用的资源后，可以继续操作。

1. 在 IoT 中心的模板中，将每个路由资源的名称更改为新名称，并根据需要更新位置。 

现已设置好一个模板，它可以根据你确定的路由处理方式，创建一个在外观上几乎与旧中心完全一致的新中心。

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>移动 -- 通过加载模板在新区域中创建新中心

使用模板在新位置创建新中心。 若要移动路由资源，应在新位置设置资源，并更新模板中的引用，以使其匹配。 如果不移动路由资源，应将其包含在模板中并对其使用更新的密钥。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“创建资源”。  

1. 在搜索框中输入“模板部署”，然后按 Enter。

1. 选择“模板部署(使用自定义模板进行部署)”。  随后会转到“模板部署”屏幕。 选择“创建”  。 将看到以下屏幕：

   ![显示用于生成自己的模板的命令的屏幕截图](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. 选择“在编辑器中生成自己的模板”，以便可以从文件上传模板。  

1. 选择“加载文件”。  

   ![显示用于上传模板文件的命令的屏幕截图](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. 浏览到已编辑的新模板并将其选中，然后选择“打开”。  随即会在编辑窗口中加载该模板。 选择“保存”  。 

   ![显示加载模板的屏幕截图](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. 在以下字段中进行填充。

   **订阅**：选择要使用的订阅。

   **资源组**：在新位置创建新资源组。 如果已经设置了新的资源组，可以选择它，而无需新建。

   **位置**：如果选择了现有的资源组，则系统会自动填充与该资源组匹配的位置。 如果创建了新资源组，请填充其位置。

   **“我同意”复选框**：简单而言，选中此框表示你同意为创建的资源付费。

1. 选择“购买”按钮。 

门户现在会验证该模板并部署克隆的中心。 如果有路由配置数据，该数据将包含到新中心，但会指向位于先前位置的资源。

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>管理已注册到 IoT 中心的设备

启动并运行克隆后，接下来需要将原始中心内的所有设备复制到该克隆。 

可通过多种方法来实现此目的。 具体方法取决于最初是否使用[设备预配服务 (DPS)](/azure/iot-dps/about-iot-dps) 预配了设备。 如果是，则操作并不困难。 如果不是，操作可能十分复杂。 

如果未使用 DPS 预配设备，可以跳过下一部分，并开始[使用导入/导出功能将设备移到新中心](#using-import-export-to-move-the-devices-to-the-new-hub)。

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>使用 DPS 在新中心重新预配设备

若要使用 DPS 将设备移到新位置，请参阅[如何重新预配设备](../iot-dps/how-to-reprovision.md)。 完成后，可以在 [Azure 门户](https://portal.azure.com)中查看设备，并确认它们是否出现在新位置。

使用 [Azure 门户](https://portal.azure.com)转到新中心。 选择该中心，然后选择“IoT 设备”。  将会看到已重新预配到克隆的中心的设备。 还可以查看克隆的中心的属性。 

如果已实施路由，请测试并确保将消息正确路由到资源。

### <a name="committing-the-changes-after-using-dps"></a>使用 DPS 后提交更改

此项更改已由 DPS 服务提交。

### <a name="rolling-back-the-changes-after-using-dps"></a>使用 DPS 后回滚更改。 

若要回滚更改，请将设备从新中心重新预配到旧中心。

现已完成迁移中心及其设备的过程。 接下来可以跳到[清理](#clean-up)部分。

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>使用导入/导出功能将设备移到新中心

应用程序面向 .NET Core，因此可以在 Windows 或 Linux 上运行它。 可以下载示例，检索连接字符串，为要运行的位设置标志，然后运行应用程序。 无需打开代码即可完成这些操作。

### <a name="downloading-the-sample"></a>下载示例

1. 使用以下页面中的 IoT C# 示例：[适用于 C# 的 Azure IoT 示例](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)。 下载 zip 文件并将其解压缩到计算机。 

1. 相关代码位于 ./iot-hub/Samples/service/ImportExportDevicesSample 中。 无需查看或编辑代码即可运行应用程序。

1. 若要运行应用程序，请指定三个连接字符串和五个选项。 可以作为命令行参数传入此数据，也可以使用环境变量，或结合使用这两种方法。 我们将以命令行参数的形式传递选项，并以环境变量的形式传递连接字符串。 

   原因在于，连接字符串很长很杂乱，且不太可能会更改，但你可能想要更改这些选项并多次运行应用程序。 若要更改环境变量的值，必须关闭命令窗口以及所用的 Visual Studio 或 VS Code。 

### <a name="options"></a>选项

下面是运行应用程序时需要指定的五个选项。 稍后我们将在命令行中插入这些选项。

*   **addDevices**（参数 1）-- 若要添加系统生成的虚拟设备，请将此选项设置为 true。 这些设置将添加到源中心。 另外，请设置 **numToAdd**（参数 2）以指定要添加的设备数。 可注册到一个中心的最大设备数目为 100 万个。此选项旨在用于测试 -- 可以生成特定数目的设备，然后将其复制到另一个中心。

*   **copyDevices**（参数 3）-- 将此选项设置为 true 会将设备从一个中心复制到另一个中心。 

*   **deleteSourceDevices**（参数 4）-- 将此选项设置为 true 会删除已注册到源中心的所有设备。 建议等到你已确定所有设备均已转移，再运行此选项。 一旦删除设备，就再也不能将其恢复。

*   **deleteDestDevices**（参数 5）-- 将此选项设置为 true 会删除已注册到目标中心（克隆）的所有设备。 若要多次复制设备，可能需要执行此操作。 

基本命令是 *dotnet run* -- 告知 .NET 生成本地 csproj 文件，然后运行该文件。 在运行此命令之前，请将命令行参数添加到末尾。 

命令行类似于以下示例：

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>对连接字符串使用环境变量

1. 若要运行该示例，需要获取新旧 IoT 中心的连接字符串，以及可用于存储临时工作文件的存储帐户的连接字符串。 我们将在环境变量中存储这些连接字符串的值。

1. 若要获取连接字符串值，请登录到 [Azure 门户](https://portal.azure.com)。 

1. 将连接字符串放到可方便检索它们的某个位置，例如记事本。 如果复制以下代码，则可将连接字符串直接粘贴到占位符所在的位置。 请不要在等号两边添加空格，否则会改变变量名称。 此外，不需要用双引号括住连接字符串。 如果用引号括住存储帐户连接字符串，该连接字符串会失去作用。

   对于 Windows，可按如下示设置环境变量：

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   对于 Linux，可按如下示定义环境变量：

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. 对于 IoT 中心连接字符串，请在门户中转到每个中心。 可以在中心的“资源”中搜索。  如果你知道某个资源组，可以转到“资源组”，选择该资源组，然后从该资源组的资产列表中选择中心。  

1. 在中心的“设置”中选择“共享访问策略”，然后选择“iothubowner”并复制其中的一个连接字符串。   对目标中心执行相同的操作。 将连接字符串添加到相应的 SET 命令。

1. 对于存储帐户连接字符串，请在“资源”中或其“资源组”下找到并打开该存储帐户。   
   
1. 在“设置”部分下，选择“访问密钥”并复制其中的一个连接字符串。  将连接字符串放到包含相应 SET 命令的文本文件中。 

现在，你已在包含 SET 命令的文件中设置了环境变量，并知道命令行参数有哪些。 让我们运行该示例。

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>运行示例应用程序并使用命令行参数

1. 打开命令提示符窗口。 选择“Windows”，然后键入 `command prompt` 打开命令提示符窗口。

1. 逐个复制用于设置环境变量的命令，将其粘贴到命令提示符窗口，然后按 Enter。 完成后，在命令提示符窗口中键入 `SET` 以查看环境变量及其值。 将这些命令复制到命令提示符窗口后，除非打开新的命令提示符窗口，否则不再需要复制。

1. 在命令提示符窗口中切换目录，直到进入 ./ImportExportDevicesSample（ImportExportDevicesSample.csproj 文件所在的目录）。 然后键入以下命令（包括命令行参数）。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    dotnet 命令将生成并运行应用程序。 由于在运行应用程序时要传入选项，因此每次运行应用程序时都可以更改这些选项的值。 例如，你可能想要运行该应用程序一次并创建新设备，然后再次运行该应用程序，并将这些设备复制到新中心，等等。 也可以在同一次运行中执行所有这些步骤，不过，我们建议只在你确定已完成克隆之后，才删除任何设备。 下面是创建 1000 个设备，然后将其复制到另一个中心的示例。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    验证设备已成功复制之后，可以从源中心删除设备，如下所示：

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>使用 Visual Studio 运行示例应用程序

1. 若要在 Visual Studio 中运行应用程序，请将当前目录切换到 IoTHubServiceSamples.sln 文件所在的文件夹。 然后在命令提示符窗口中运行以下命令，在 Visual Studio 中打开解决方案。 必须在设置环境变量的同一个命令窗口中执行此操作，因此这些变量是已知的。

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. 右键单击项目“ImportExportDevicesSample”并选择“设为启动项目”。      
    
1. 在 ImportExportDevicesSample 文件夹中 Program.cs 的顶部，设置五个选项的变量。

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

1. 按 F5 运行应用程序。 运行完应用程序后，可以查看结果。

### <a name="view-the-results"></a>查看结果 

可以在 [Azure 门户](https://portal.azure.com)中查看设备，并确认它们是否出现在新位置。

1. 使用 [Azure 门户](https://portal.azure.com)转到新中心。 选择该中心，然后选择“IoT 设备”。  将会看到刚刚从旧中心复制到克隆的中心的设备。 还可以查看克隆的中心的属性。 

1. 检查导入/导出错误：在 [Azure 门户](https://portal.azure.com)中转到 Azure 存储帐户，然后查看 `ImportErrors.log` 的 `devicefiles` 容器。 如果此文件为空（大小为 0），则表示未发生任何错误。 如果尝试多次导入同一设备，第二次导入时会拒绝该设备，并将一条错误消息添加到日志文件中。

### <a name="committing-the-changes"></a>提交更改 

此时，你已将中心复制到新位置，并已将设备迁移到新克隆。 现在需要进行更改，使设备能够与克隆的中心配合工作。

若要提交更改，需要执行以下步骤： 

* 更新每个设备以更改 IoT 中心主机名，将 IoT 中心主机名指向新中心。 应使用首次预配设备时所用的相同方法执行此操作。

* 更改引用旧中心的所有应用程序，使其指向新中心。

* 完成后，新中心应会启动并运行。 旧中心应该没有活动的设备并处于断开连接状态。 

### <a name="rolling-back-the-changes"></a>回滚更改

如果你决定回滚更改，请执行以下步骤：

* 更新每个设备以更改 IoT 中心主机名，将 IoT 中心主机名指向旧中心。 应使用首次预配设备时所用的相同方法执行此操作。

* 更改引用新中心的所有应用程序，使其指向旧中心。 例如，如果使用 Azure Analytics，则可能需要重新配置 [Azure 流分析输入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。

* 删除新中心。 

* 如果你有路由资源，则旧中心上的配置仍应指向正确的路由配置，并且在旧中心重启后，它应该仍可使用这些资源。

### <a name="checking-the-results"></a>检查结果 

若要检查结果，请将 IoT 解决方案更改为指向位于新位置的中心，然后运行它。 换而言之，请对新中心执行以前对旧中心所执行的相同操作，并确保它们正常工作。 

如果已实施路由，请测试并确保将消息正确路由到资源。

## <a name="clean-up"></a>清理

在真正确定新中心已启动并运行，并且设备正常工作之前，请不要清理资源。 另外，如果使用路由，请务必对此功能进行测试。 准备就绪后，执行以下步骤来清理旧资源：

* 删除旧中心（如果尚未这样做）。 这会从该中心删除所有活动的设备。

* 如果已将路由资源移到新位置，可以删除旧路由资源。

## <a name="next-steps"></a>后续步骤

现已将 IoT 中心连同设备一起克隆到了新区域中的新中心。 有关对 IoT 中心内的标识注册表执行批量操作的详细信息，请参阅[批量导入和导出 IoT 中心设备标识](iot-hub-bulk-identity-mgmt.md)。

有关 IoT 中心和中心开发的详细信息，请参阅以下文章。

* [IoT 中心开发人员指南](iot-hub-devguide.md)

* [IoT 中心路由教程](tutorial-routing.md)

* [IoT 中心设备管理概述](iot-hub-device-management-overview.md)

* 若要部署示例应用程序，请参阅 [.NET Core 应用程序部署](https://docs.microsoft.com/dotnet/core/deploying/index)。
