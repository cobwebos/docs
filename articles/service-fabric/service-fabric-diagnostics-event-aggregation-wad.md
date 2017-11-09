---
title: "Azure Service Fabric 事件聚合与 Microsoft Azure 诊断 | Microsoft Docs"
description: "了解使用 WAD 监视和诊断 Azure Service Fabric 群集的聚合和收集事件。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 5773361fdec4cb8ee54fa2856f6aa969d5dac4e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>使用 Microsoft Azure 诊断的事件聚合和集合
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

当你运行 Azure Service Fabric 群集时，最好是从一个中心位置的所有节点中收集日志。 将日志放在中心位置可帮助分析和排查群集中的问题，或该群集中运行的应用程序与服务的问题。

上传和收集日志的方式之一是使用可将日志上传到 Azure 存储、也能选择发送日志到 Azure Application Insights 或 Azure 事件中心的 Microsoft Azure 诊断 (WAD) 扩展。 也可以使用外部进程读取存储中的事件，并将它们放在分析平台产品中，例如 [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) 或其他日志分析解决方案中。

## <a name="prerequisites"></a>先决条件
以下工具可用于执行本文档中的某些操作：

* [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)（与 Azure 云服务相关，但包含有用的信息和示例）
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager 客户端](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager 模板](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>日志和事件源

### <a name="service-fabric-platform-events"></a>Service Fabric 平台事件
如[本文](service-fabric-diagnostics-event-generation-infra.md)所述，可使用一些现成的日志通道设置 Service Fabric，下列通道能轻松配置 WAD，发送监视和诊断数据到存储表或其他位置：
  * 操作事件：Service Fabric 平台执行的更高水平操作。 示例包括创建应用程序和服务、节点状态更改和升级信息。 这些会以 Windows 事件跟踪 (ETW) 日志的形式发出
  * [Reliable Actors 编程模型事件](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services 编程模型事件](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>应用程序事件
 从你的应用程序和服务代码发出，使用 Visual Studio 模板提供的 EventSource 帮助器类写出的事件。 有关如何从应用程序写入 EventSource 日志的详细信息，请参阅[在本地计算机开发设置中监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

## <a name="deploy-the-diagnostics-extension"></a>部署诊断扩展
收集日志的第一个步骤是将诊断扩展部署在 Service Fabric 群集的每个 VM 上。 诊断扩展将收集每个 VM 上的日志，并将它们上传到指定的存储帐户。 根据使用的是 Azure 门户还是 Azure Resource Manager，步骤稍有不同。 另外，步骤还会根据扩展是在创建群集时部署的，还是针对现有群集部署的，而有所不同。 让我们看看每个方案的步骤。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>在通过 Azure 门户创建群集过程中部署诊断扩展
若要在创建群集过程中将诊断扩展部署到群集中的 VM，需使用下图所示的“诊断设置”面板 - 请确保诊断设置为“打开”（默认设置）。 创建群集后，无法使用门户更改这些设置。

![门户中有关创建群集的 Azure 诊断设置](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

使用门户创建群集时，强烈建议先下载模板，**然后再单击“确定”**创建群集。 有关详细信息，请参阅[使用 Azure Resource Manager 模板设置 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)。 以后，需要通过模板进行更改，因为无法使用门户进行某些更改。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 在创建群集过程中部署诊断扩展
若要使用 Resource Manager 创建群集，需要在创建群集之前，将诊断配置 JSON 添加到整个 Resource Manager 模板。 我们将在 Resource Manager 模板示例中提供包含五个 VM 的群集 Resource Manager 模板，并在演示 Resource Manager 模板示例的过程中添加诊断配置。 可以在 Azure 示例库中的以下位置找到该示例：[包含诊断 Resource Manager 模板示例的五节点群集](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)。

若要查看 Resource Manager 模板中的诊断设置，请打开 azuredeploy.json 文件并搜索 **IaaSDiagnostics**。 若要使用此模板创建群集，请在上面的链接中选择“**部署到 Azure**”按钮。

或者，也可以下载 Resource Manager 示例，进行更改，然后在 Azure PowerShell 窗口中输入 `New-AzureRmResourceGroupDeployment` 命令，使用修改后的模板创建群集。 有关要在命令中传入哪些参数，请参阅以下代码。 有关如何使用 PowerShell 部署资源组的详细信息，请参阅[使用 Azure Resource Manager 模板部署资源组](../azure-resource-manager/resource-group-template-deploy.md)一文。

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

## <a name="collect-health-and-load-events"></a>收集运行状况并加载事件

从 5.4 版本的 Service Fabric 开始，可收集运行状况和加载指标事件。 这些事件反映了由系统或你的代码使用 [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) 或 [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx) 等运行状况或加载报告 API 生成的事件。 这允许随时间聚合和查看系统运行状况，以及基于运行状况或加载事件进行警报。 要查看 Visual Studio 的诊断事件查看器中的这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000008”添加到 ETW 提供程序列表中。

若要收集这些事件，请修改资源管理器模板，以包含

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>收集反向代理事件

从 5.7 版本的 Service Fabric 开始，可收集[反向代理](service-fabric-reverseproxy.md)事件。
反向代理会将事件发到两个通道，其中一个包含反映请求处理故障的错误事件，而另一个包含关于在反向代理处理的所有请求的详细事件。 

1. 收集错误事件：若要查看 Visual Studio 的诊断事件查看器中的这些事件，请将“Microsoft-ServiceFabric:4:0x4000000000000010”添加到 ETW 提供程序列表中。
若要收集 Azure 群集中的这些事件，请修改资源管理器模板，以包含

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

2. 收集所有请求处理事件：在 Visual Studio 的诊断事件查看器中，将 ETW 提供程序列表中的 Microsoft ServiceFabric 条目更新为“Microsoft-ServiceFabric:4:0x4000000000000020”。
对于 Azure Service Fabric 群集，请修改资源管理器模板，以包含

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```
> 建议谨慎启用收集此通道中的事件，因为这将收集通过反向代理的所有流量，且可快速消耗存储容量。

对于 Azure Service Fabric 群集，所有节点中的事件均在 SystemEventTable 中进行收集并聚合。
有关详细的反向代理事件疑难解答，请参阅[反向代理诊断指南](service-fabric-reverse-proxy-diagnostics.md)。

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

若要收集性能计数器或事件日志，请参考[使用 Azure Resource Manager 模板创建具有监视和诊断功能的 Windows 虚拟机](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中提供的示例修改 Resource Manager 模板。 然后重新发布 Resource Manager 模板。

## <a name="collect-performance-counters"></a>收集性能计数器

若要从群集中收集性能指标，请将性能计数器添加到群集中的 Resource Manager 模板“WadCfg > DiagnosticMonitorConfiguration”。 对于我们建议收集的性能计数器，请参阅 [Service Fabric 性能计数器](service-fabric-diagnostics-event-generation-perf.md)。

例如，我们将在此处设置一个性能计数器，每隔 15 秒采样（可更改并遵循“PT \<时间>\<单位>”格式，例如，PT3M 每隔 3 分钟采样），每分钟传输到适当的存储表中。

  ```json
  "PerformanceCounters": {
      "scheduledTransferPeriod": "PT1M",
      "PerformanceCounterConfiguration": [
          {
              "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
              "sampleRate": "PT15S",
              "unit": "Percent",
              "annotation": [
              ],
              "sinks": ""
          }
      ]
  }
  ```
  
如果使用 Application Insights 接收器（如下面部分所述）并想要这些指标显示在 Application Insights 中，请确保将接收器名称添加到“sinks”部分，如上所示。 此外，请考虑创建一个单独的表来接受性能计数器的数据，使其不会被已启用的来自他日志通道的数据挤出。


## <a name="send-logs-to-application-insights"></a>将日志发送到 Application Insights

将监视和诊断数据发送到 Application Insights (AI) 可作为 WAD 配置的一部分。 如果决定使用 AI 进行事件分析和可视化，请阅读[使用 Application Insights 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-appinsights.md)，将 AI 接收器设置为“WadCfg”的一部分。

## <a name="next-steps"></a>后续步骤

正确配置 Azure 诊断后，将看到来自 ETW 和 EventSource 日志的存储表中的数据。 如果选择使用 OMS、Kibana 或其他不在 Resource Manager 模板中直接配置的任何数据分析和可视化平台，请确保设置所选平台以读入这些存储表中的数据。 对于 OMS 这样做相对简单，相关介绍在[使用 OMS 的事件和日志分析](service-fabric-diagnostics-event-analysis-oms.md)中。 Application Insights 则有点特殊，由于它可以被配置为诊断扩展配置中的一部分，所以如果选择使用 AI 请参考[相应的文章](service-fabric-diagnostics-event-analysis-appinsights.md)。

>[!NOTE]
>目前没有任何方法可以筛选或清理已发送到表的事件。 如果未实施某个过程从表中删除事件，该表会不断增大。 目前，在[监视器示例](https://github.com/Azure-Samples/service-fabric-watchdog-service)中有一个运行数据整理服务的示例，建议为自己编写一个，除非有需要存储超过 30 或 90 天日志的的理由。

* [了解如何使用诊断扩展收集性能计数器或日志](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 Application Insights 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [使用 OMS 进行事件分析和可视化](service-fabric-diagnostics-event-analysis-oms.md)