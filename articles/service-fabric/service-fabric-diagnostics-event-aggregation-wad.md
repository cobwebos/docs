---
title: 使用 Windows Azure 诊断聚合事件
description: 了解如何使用 WAD 聚合和收集事件，以便对 Azure Service Fabric 群集进行监视和诊断。
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: bcb9ca9e73c0898dc778202eca036a5ae92bebf8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076141"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>使用 Windows Azure 诊断聚合和集合事件
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

当你运行 Azure Service Fabric 群集时，最好是从一个中心位置的所有节点中收集日志。 将日志放在中心位置可帮助分析和排查群集中的问题，或该群集中运行的应用程序与服务的问题。

上传和收集日志的方式之一是使用可将日志上传到 Azure 存储、也能选择发送日志到 Azure Application Insights 或 Azure 事件中心的 Windows Azure 诊断 (WAD) 扩展。 你还可以使用外部进程读取存储中的事件，并将它们放在分析平台产品（如 [Azure Monitor 日志](./service-fabric-diagnostics-oms-setup.md) 或其他日志分析解决方案）中。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件
本文中使用了以下工具：

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Azure Resource Manager 模板](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric 平台事件
Service Fabric 提供了一些[现成的日志记录通道](service-fabric-diagnostics-event-generation-infra.md)，该扩展预配置了其中的以下通道来将监视和诊断数据发送到存储表或其他位置：
  * [操作事件](service-fabric-diagnostics-event-generation-operational.md)：Service Fabric 平台执行的较高级别的操作。 示例包括创建应用程序和服务、节点状态更改和升级信息。 这些会以 Windows 事件跟踪 (ETW) 日志的形式发出
  * [Reliable Actors 编程模型事件](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services 编程模型事件](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>通过门户部署诊断扩展
收集日志的第一个步骤是将诊断扩展部署在 Service Fabric 群集中的每个虚拟机规模集节点上。 诊断扩展将收集每个 VM 上的日志，并将它们上传到指定的存储帐户。 以下步骤概述了如何通过 Azure 门户和 Azure 资源管理器模板为新的和现有的群集完成此操作。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>在通过 Azure 门户创建群集过程中部署诊断扩展
创建群集时，在群集配置步骤中，展开可选设置并确保将“诊断”设置为“打开”  （默认设置）。

![门户中有关创建群集的 Azure 诊断设置](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

强烈建议在最终步骤中**单击“创建”之前**下载模板。 有关详细信息，请参阅[使用 Azure Resource Manager 模板设置 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)。 需要使用该模板来更改要从（上面列出的）哪些通道来收集数据。

![群集模板](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

现在，你正在聚合 Azure 存储中的事件，请 [设置 Azure Monitor 日志](service-fabric-diagnostics-oms-setup.md) 以获取见解并在 Azure Monitor 日志门户中进行查询

>[!NOTE]
>目前没有任何方法可以筛选或清理发送到表的事件。 如果未实现某个流程来从表中删除事件，则表会不断增大（默认上限为 50 GB）。 [本文的下文中进一步说明了](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota)如何对此进行更改。 另外，在[监视器示例](https://github.com/Azure-Samples/service-fabric-watchdog-service)中有一个运行数据整理服务的示例，建议为自己编写一个，除非有需要存储超过 30 或 90 天日志的的理由。



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>通过 Azure 资源管理器部署诊断扩展

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>创建包含诊断扩展的群集
若要使用资源管理器创建群集，需要将诊断配置 JSON 添加到整个资源管理器模板。 我们会在 Resource Manager 模板示例中提供包含五个 VM 的群集 Resource Manager 模板，并在演示 Resource Manager 模板示例的过程中添加诊断配置。 可以在 Azure 示例库中的此位置看到它。[具有诊断资源管理器的五节点群集的模板示例](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/)。

若要查看 Resource Manager 模板中的诊断设置，请打开 azuredeploy.json 文件并搜索 **IaaSDiagnostics**。 若要使用此模板创建群集，请选择在上面的链接中提供的“部署到 Azure”  按钮。

或者，也可以下载 Resource Manager 示例，进行更改，并在 Azure PowerShell 窗口中输入 `New-AzResourceGroupDeployment` 命令，使用修改后的模板创建群集。 有关要在命令中传入的参数，请参阅以下代码。 有关如何使用 PowerShell 部署资源组的详细信息，请参阅[使用 Azure Resource Manager 模板部署资源组](../azure-resource-manager/templates/deploy-powershell.md)一文。

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>向现有群集添加诊断扩展
如果存在尚未部署诊断的现有群集，可以通过群集模板来添加或更新该扩展。 修改用于创建现有群集的 Resource Manager 模板，或者如前所述从门户下载该模板。 执行以下任务来修改 template.json 文件：

通过将新存储资源添加到 resources 节将其添加到模板。

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 接下来，将该资源添加到存储帐户定义后面与 `supportLogStorageAccountName` 之间的 parameters 节中。 将占位符文本 *storage account name goes here* 替换为所需的存储帐户的名称。

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
然后，通过在 extensions 数组中添加以下代码更新 template.json 文件的 `VirtualMachineProfile` 节。 请务必根据插入位置，在开头或末尾添加逗点。

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

如上所述修改 template.json 文件后，请重新发布 Resource Manager 模板。 如果已导出模板，则运行 deploy.ps1 文件会重新发布模板。 部署后，请确保 **ProvisioningState** 为 **Succeeded**。

> [!TIP]
> 如果要将容器部署到群集，可通过将此代码添加到“WadCfg > DiagnosticMonitorConfiguration”  节，启用 WAD 来选取 docker 统计信息。
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>更新存储配额

由于由该扩展填充的表不断增长，直至达到配额，因此可能需要考虑减小配额大小。 默认值为 50 GB，可以在模板中在 `DiagnosticMonitorConfiguration` 下的 `overallQuotaInMB` 字段下进行配置。

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>日志收集配置
其他通道的日志也可供收集，下面是你可以在 Azure 中运行的群集的模板中进行的一些最常见配置。

* 操作通道 - 基本：默认情况下启用，由 Service Fabric 和群集执行的高级操作，包括出现节点事件、部署新应用程序或升级回滚等。有关事件的列表，请参阅[操作通道事件](./service-fabric-diagnostics-event-generation-operational.md)。
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* 操作通道 - 详细：这包括运行状况报告和负载均衡决策，加上基本操作通道中的所有内容。 这些事件由系统或代码使用 [ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100)) 或 [ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100)) 等运行状况或加载报告 API 生成。 要在 Visual Studio 的诊断事件查看器中查看这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000008”添加到 ETW 提供程序列表。

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* 数据和消息通道 - 基本：消息（当前仅限 ReverseProxy）和数据路径中生成的关键日志和事件，以及详细操作通道日志。 这些事件是请求处理失败和 ReverseProxy 中的其他严重问题以及已处理的请求。 **这是我们针对全面日志记录的建议**。 若要在 Visual Studio 的诊断事件查看器中查看这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000010”添加到 ETW 提供程序列表。

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* 数据和消息通道 - 详细：包含群集和详细操作通道中的数据和消息提供的所有非关键日志的详细通道。 有关对所有反向代理事件的详细故障排除，请参阅[反向代理诊断指南](service-fabric-reverse-proxy-diagnostics.md)。  若要在 Visual Studio 的诊断事件查看器中查看这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000020”添加到 ETW 提供程序列表。

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>此通道包含非常大量的事件，从详细通道启用事件收集会导致快速生成大量跟踪并可能会消耗存储容量。 请只有在绝对必要的情况下才启用此项。


若要启用“基本操作通道”（建议启用以获得干扰最少的全面日志记录），模板的 `WadCfg` 中的 `EtwManifestProviderConfiguration` 将如下所示  ：

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>从新的 EventSource 通道收集

若要将诊断更新为从新的 EventSource 通道（表示要部署的新应用程序）收集日志，请执行之前描述的相同的步骤，其中描述了现有群集的诊断设置。

在使用 `New-AzResourceGroupDeployment` PowerShell 命令应用配置更新之前，请更新 template.json 文件中的 `EtwEventSourceProviderConfiguration` 节，添加新 EventSource 通道的条目。 事件源的名称定义为 Visual Studio 生成的 ServiceEventSource.cs 文件中代码的一部分。

例如，如果事件源名为 My-Eventsource，请添加以下代码，将来自 My-Eventsource 的事件放入名为 MyDestinationTableName 的表中。

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

若要收集性能计数器或事件日志，请参考[使用 Azure 资源管理器模板创建具有监视和诊断功能的 Windows 虚拟机](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)中提供的示例修改资源管理器模板。 然后，重新发布资源管理器模板。

## <a name="collect-performance-counters"></a>收集性能计数器

若要从群集中收集性能指标，请将性能计数器添加到群集的资源管理器模板中的“WadCfg > DiagnosticMonitorConfiguration”。 有关修改 `WadCfg` 以收集特定性能计数器的步骤，请参阅[通过 WAD 监控性能](service-fabric-diagnostics-perf-wad.md)。 对于我们建议收集的性能计数器列表，请参阅 [Service Fabric 性能计数器](service-fabric-diagnostics-event-generation-perf.md)。
  
如果使用 Application Insights 接收器（如下面部分所述）并想要这些指标显示在 Application Insights 中，则确保将接收器名称添加到“sinks”部分中，如上所示。 这将自动向你的 Application Insights 资源发送单独配置的性能计数器。


## <a name="send-logs-to-application-insights"></a>将日志发送到 Application Insights

### <a name="configuring-application-insights-with-wad"></a>使用 WAD 配置 Application Insights

>[!NOTE]
>目前仅适用于 Windows 群集。

可通过两种方式将数据从 WAD 发送到 Azure Application Insights，这一过程是通过向 WAD 配置添加 Application Insights 接收器实现的，不管是通过 Azure 门户还是通过 Azure 资源管理器模板。

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>在 Azure 门户中创建群集时添加 Application Insights 检测密钥

![添加 AI 密钥](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

创建群集时，如果“已启用”诊断，则会显示一个可选字段，可在其中输入 Application Insights 检测密钥。 如果在此处粘贴 Application Insights 密钥，则系统会在用于部署群集的资源管理器模板中自动配置 Application Insights 接收器。

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>将 Application Insights 接收器添加到资源管理器模板

在 Resource Manager 模板的“WadCfg”中，通过包含以下两项更改来添加“接收器”：

1. 在声明完 `DiagnosticMonitorConfiguration` 后，直接添加接收器配置：

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. 在 `DiagnosticMonitorConfiguration` 中添加接收器，具体方法是在 `WadCfg` 的 `DiagnosticMonitorConfiguration` 中添加以下代码行（紧靠声明的 `EtwProviders` 前面）：

    ```json
    "sinks": "applicationInsights"
    ```

在上面的两个代码片段中，名称“applicationInsights”用于描述接收器。 不一定非要使用此名称；只要将接收器包含在“sinks”中，就可以将名称设置为任何字符串。

目前，群集中的日志在 Application Insights 日志查看器中显示为**跟踪**。 由于来自平台的大部分跟踪信息都是“参考”级别，因此还可以考虑将接收器配置更改为仅发送类型为“警告”或“错误”的日志。 这可通过将“通道”添加到接收器完成，如[本文](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)所示。

>[!NOTE]
>如果在门户或资源管理器模板中使用错误的 Application Insights 密钥，则必须手动更改密钥并更新/重新部署群集。

## <a name="next-steps"></a>后续步骤

正确配置 Azure 诊断后，将看到来自 ETW 和 EventSource 日志的存储表中的数据。 如果选择使用 Azure Monitor 日志、Kibana 或其他不在资源管理器模板中直接配置的任何数据分析和可视化平台，请确保设置所选平台以读入这些存储表中的数据。 对 Azure Monitor 日志执行此操作相对较为简单，并在 [事件和日志分析](service-fabric-diagnostics-event-analysis-oms.md)中进行说明。 Application Insights 则有点特殊，由于它可以被配置为诊断扩展配置中的一部分，所以如果选择使用 AI 请参考[相应的文章](service-fabric-diagnostics-event-analysis-appinsights.md)。

>[!NOTE]
>目前没有任何方法可以筛选或清理已发送到表的事件。 如果未实施某个过程从表中删除事件，该表会不断增大。 目前，在[监视器示例](https://github.com/Azure-Samples/service-fabric-watchdog-service)中有一个运行数据整理服务的示例，建议为自己编写一个，除非有需要存储超过 30 或 90 天日志的的理由。

* [了解如何使用诊断扩展收集性能计数器或日志](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [使用 Application Insights 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Monitor 日志进行事件分析和可视化](service-fabric-diagnostics-event-analysis-oms.md)
* [使用 Application Insights 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Monitor 日志进行事件分析和可视化](service-fabric-diagnostics-event-analysis-oms.md)
