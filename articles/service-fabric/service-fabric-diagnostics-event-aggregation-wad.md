---
title: Azure Service Fabric 事件聚合与 Microsoft Azure 诊断 | Microsoft Docs
description: 了解使用 WAD 监视和诊断 Azure Service Fabric 群集的聚合和收集事件。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: 65e5e45300e66cd8c3acc44a91335de45a919eb5
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>使用 Microsoft Azure 诊断的事件聚合和集合
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

当你运行 Azure Service Fabric 群集时，最好是从一个中心位置的所有节点中收集日志。 将日志放在中心位置可帮助分析和排查群集中的问题，或该群集中运行的应用程序与服务的问题。

上传和收集日志的方式之一是使用可将日志上传到 Azure 存储、也能选择发送日志到 Azure Application Insights 或 Azure 事件中心的 Microsoft Azure 诊断 (WAD) 扩展。 也可以使用外部进程读取存储中的事件，并将它们放在分析平台产品（例如 [Log Analytics](../log-analytics/log-analytics-service-fabric.md) 或其他日志分析解决方案）中。

## <a name="prerequisites"></a>先决条件
以下工具可用于执行本文档中的某些操作：

* [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)（与 Azure 云服务相关，但包含有用的信息和示例）
* [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure 资源管理器客户端](https://github.com/projectkudu/ARMClient)
* [Azure 资源管理器模板](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>日志和事件源

### <a name="service-fabric-platform-events"></a>Service Fabric 平台事件
如[本文](service-fabric-diagnostics-event-generation-infra.md)所述，可使用一些现成的日志通道设置 Service Fabric，下列通道能轻松配置 WAD，发送监视和诊断数据到存储表或其他位置：
  * 操作事件：Service Fabric 平台执行的更高水平操作。 示例包括创建应用程序和服务、节点状态更改和升级信息。 这些会以 Windows 事件跟踪 (ETW) 日志的形式发出
  * [Reliable Actors 编程模型事件](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services 编程模型事件](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>应用程序事件
 从你的应用程序和服务代码发出，使用 Visual Studio 模板提供的 EventSource 帮助器类写出的事件。 有关如何从应用程序写入 EventSource 日志的详细信息，请参阅[在本地计算机开发设置中监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

## <a name="deploy-the-diagnostics-extension"></a>部署诊断扩展
收集日志的第一个步骤是将诊断扩展部署在 Service Fabric 群集的每个 VM 上。 诊断扩展将收集每个 VM 上的日志，并将它们上传到指定的存储帐户。 根据使用的是 Azure 门户还是 Azure 资源管理器，步骤稍有不同。 另外，步骤还会根据扩展是在创建群集时部署的，还是针对现有群集部署的，而有所不同。 让我们看看每个方案的步骤。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>在通过 Azure 门户创建群集过程中部署诊断扩展
若要在创建群集过程中将诊断扩展部署到群集中的 VM，需使用下图所示的“诊断设置”面板 - 请确保诊断设置为“打开”（默认设置）。 创建群集后，无法使用门户更改这些设置。

![门户中有关创建群集的 Azure 诊断设置](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

使用门户创建群集时，强烈建议先下载模板，**然后再单击“确定”**创建群集。 有关详细信息，请参阅[使用 Azure 资源管理器模板设置 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)。 以后，需要通过模板进行更改，因为无法使用门户进行某些更改。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>使用 Azure 资源管理器在创建群集过程中部署诊断扩展
若要使用 Resource Manager 创建群集，需要在创建群集之前，将诊断配置 JSON 添加到整个 Resource Manager 模板。 我们将在 Resource Manager 模板示例中提供包含五个 VM 的群集 Resource Manager 模板，并在演示 Resource Manager 模板示例的过程中添加诊断配置。 可以在 Azure 示例库中的以下位置找到该示例：[包含诊断 Resource Manager 模板示例的五节点群集](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/)。

若要查看 Resource Manager 模板中的诊断设置，请打开 azuredeploy.json 文件并搜索 **IaaSDiagnostics**。 若要使用此模板创建群集，请在上面的链接中选择“**部署到 Azure**”按钮。

或者，也可以下载 Resource Manager 示例，进行更改，然后在 Azure PowerShell 窗口中输入 `New-AzureRmResourceGroupDeployment` 命令，使用修改后的模板创建群集。 有关要在命令中传入哪些参数，请参阅以下代码。 有关如何使用 PowerShell 部署资源组的详细信息，请参阅[使用 Azure 资源管理器模板部署资源组](../azure-resource-manager/resource-group-template-deploy.md)一文。

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>将诊断扩展部署到现有群集
如果现有的群集上未部署诊断或者你要修改现有配置，可以添加或更新配置。 修改用于创建现有群集的 Resource Manager 模板，或者如前所述从门户下载该模板。 执行以下任务可修改 template.json 文件。

通过将存储资源添加到 resources 节将其添加到模板。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 接下来，将该资源添加到存储帐户定义后面的 `supportLogStorageAccountName` 与 `vmNodeType0Name` 之间的 parameters 节中。 将占位符文本 *storage account name goes here* 替换为存储帐户的名称。

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
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
然后，通过在 extensions 数组中添进行下代码来更新 template.json 的 `VirtualMachineProfile` 节。 请务必根据插入位置，在开头或末尾添加逗点。

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
> 如果要将容器部署到群集，可通过将此代码添加到“WadCfg > DiagnosticMonitorConfiguration”节，启用 WAD 来选取 docker 统计信息。
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

## <a name="log-collection-configurations"></a>日志收集配置
其他通道的日志也可供收集，下面是你可以在 Azure 中运行的群集的模板中进行的一些最常见配置。

* 操作通道 - 基本：默认情况下启用，由 Service Fabric 和群集执行的高级操作，包括发生的节点事件、所部署新应用程序的事件或升级回退事件等。有关事件的列表，请参阅[操作通道事件](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-diagnostics-event-generation-operational)。
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* 操作通道 - 详细：这包括运行状况报告和负载均衡决策，加上基本操作通道中的所有内容。 这些事件由系统或代码使用 [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) 或 [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx) 等运行状况或加载报告 API 生成。 要查看 Visual Studio 的诊断事件查看器中的这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000008”添加到 ETW 提供程序列表中。

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* 数据和消息通道 - 基本：消息（当前仅限 ReverseProxy）和数据路径中生成的关键日志和事件，以及详细操作通道日志。 这些事件是处理失败的请求和 ReverseProxy 中的其他关键问题以及已处理的请求。 **这是我们针对全面日志记录的建议**。 若要在 Visual Studio 的诊断事件查看器中查看这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000010”添加到 ETW 提供程序列表。

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* 数据和消息通道 - 详细：包含群集和详细操作通道中的数据和消息提供的所有非关键日志。 有关对所有反向代理事件的详细故障排除，请参阅[反向代理诊断指南](service-fabric-reverse-proxy-diagnostics.md)。  若要在 Visual Studio 的诊断事件查看器中查看这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000020”添加到 ETW 提供程序列表。

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>此通道包含非常大量的事件，从详细通道启用事件收集会导致快速生成大量跟踪并可能会消耗存储容量。 请只有在绝对必要的情况下才启用此项。


若要启用“基本数据和消息通道”（我们针对全面日志记录的建议），模板的 `WadCfg` 中的 `EtwManifestProviderConfiguration` 将如下所示：

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
                "scheduledTransferKeywordFilter": "4611686018427387928",
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

在使用 `New-AzureRmResourceGroupDeployment` PowerShell 命令应用配置更新之前，请更新 template.json 文件中的 `EtwEventSourceProviderConfiguration` 节，添加新 EventSource 通道的条目。 事件源的名称定义为 Visual Studio 生成的 ServiceEventSource.cs 文件中的代码的一部分。

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

若要收集性能计数器或事件日志，请参考[使用 Azure 资源管理器模板创建具有监视和诊断功能的 Windows 虚拟机](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中提供的示例修改资源管理器模板。 然后重新发布 Resource Manager 模板。

## <a name="collect-performance-counters"></a>收集性能计数器

若要从群集中收集性能指标，请将性能计数器添加到群集中的 Resource Manager 模板“WadCfg > DiagnosticMonitorConfiguration”。 有关修改 `WadCfg` 以收集特定性能计数器的步骤，请参阅[通过 WAD 监控性能](service-fabric-diagnostics-perf-wad.md)。 对于我们建议收集的性能计数器列表，请参阅 [Service Fabric 性能计数器](service-fabric-diagnostics-event-generation-perf.md)。
  
如果使用 Application Insights 接收器（如下面部分所述）并想要这些指标显示在 Application Insights 中，请确保将接收器名称添加到“sinks”部分，如上所示。 这将自动向你的 Application Insights 资源发送单独配置的性能计数器。


## <a name="send-logs-to-application-insights"></a>将日志发送到 Application Insights

将监视和诊断数据发送到 Application Insights (AI) 可作为 WAD 配置的一部分。 如果决定使用 AI 进行事件分析和可视化，请阅读[使用 Application Insights 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-appinsights.md)，将 AI 接收器设置为“WadCfg”的一部分。

## <a name="next-steps"></a>后续步骤

正确配置 Azure 诊断后，将看到来自 ETW 和 EventSource 日志的存储表中的数据。 如果选择使用 Log Analytics、Kibana 或其他不在资源管理器模板中直接配置的任何数据分析和可视化平台，请确保设置所选平台以读入这些存储表中的数据。 对于 Log Analytics 这样做相对简单，相关介绍在[事件和日志分析](service-fabric-diagnostics-event-analysis-oms.md)中。 Application Insights 则有点特殊，由于它可以被配置为诊断扩展配置中的一部分，所以如果选择使用 AI 请参考[相应的文章](service-fabric-diagnostics-event-analysis-appinsights.md)。

>[!NOTE]
>目前没有任何方法可以筛选或清理已发送到表的事件。 如果未实施某个过程从表中删除事件，该表会不断增大。 目前，在[监视器示例](https://github.com/Azure-Samples/service-fabric-watchdog-service)中有一个运行数据整理服务的示例，建议为自己编写一个，除非有需要存储超过 30 或 90 天日志的的理由。

* [了解如何使用诊断扩展收集性能计数器或日志](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 Application Insights 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [使用 Log Analytics 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-oms.md)
