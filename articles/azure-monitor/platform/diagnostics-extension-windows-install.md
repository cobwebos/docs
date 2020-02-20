---
title: 安装和配置 Windows Azure 诊断扩展（WAD）
description: 了解如何在 Azure 存储帐户中收集 Azure 诊断数据，以便你可以使用多种可用工具之一查看该数据。
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 5b3cc4cbaa663b7932609e85c544378a7cca69ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472682"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>安装和配置 Windows Azure 诊断扩展（WAD）
Azure 诊断扩展是 Azure Monitor 中的一个代理，它收集来宾操作系统中的监视数据以及 Azure 虚拟机和其他计算资源的工作负荷。 本文详细介绍了如何安装和配置 Windows 诊断扩展，以及如何将数据存储在和 Azure 存储帐户中的说明。

诊断扩展在 Azure 中实现为[虚拟机扩展](/virtual-machines/extensions/overview)，因此，它支持使用资源管理器模板、POWERSHELL 和 CLI 的相同安装选项。 有关安装和维护虚拟机扩展的详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](/virtual-machines/extensions/features-windows)。

## <a name="install-with-azure-portal"></a>安装 Azure 门户
可以在 Azure 门户中的单个虚拟机上安装和配置诊断扩展，此操作提供了一个接口，而不是直接使用该配置。 启用诊断扩展后，它会自动使用最常见性能计数器和事件的默认配置。 你可以根据特定要求修改此默认配置。

> [!NOTE]
> 存在无法使用 Azure 门户配置的诊断扩展设置，包括将数据发送到 Azure 事件中心。 对于这些设置，必须使用其他配置方法之一。

1. 在 Azure 门户中打开虚拟机的菜单。
2. 单击 "VM" 菜单的 "**监视**" 部分中的 "**诊断设置**"。
3. 如果尚未启用诊断扩展，请单击 "**启用来宾级监视**"。
4. 将为 VM 创建一个新的 Azure 存储帐户，其名称将基于 VM 的资源组的名称。 可以通过选择 "**代理**" 选项卡将 VM 附加到另一个存储帐户。

![诊断设置](media/diagnostics-extension-windows-install/diagnostic-settings.png)


启用诊断扩展后，可以修改默认配置。 下表描述了可在不同选项卡中修改的选项。 某些选项具有可用于指定更详细配置的**自定义**命令;有关不同设置的详细信息，请参阅[Windows 诊断扩展架构](diagnostics-extension-schema-windows.md)。

| 选项卡 | 说明 |
|:---|:---|
| 概述 | 显示具有其他选项卡链接的当前配置。 |
| 性能计数器 | 选择要收集的性能计数器和每个计数器的采样速率。  |
| 日志 | 选择要收集的日志数据。 这包括 Windows 事件日志、IIS 日志、.NET 应用程序日志和 ETW 事件。  |
| 故障转储 | 为不同的进程启用故障转储。 |
| 接收器 | 除了 Azure 存储，还允许数据接收器将数据发送到目标。<br>Azure Monitor-将性能数据发送到 Azure Monitor 指标。<br>Application Insights-将数据发送到 Application Insights 应用程序。 |
| 代理 | 修改代理的以下配置：<br>-更改存储帐户。<br>-指定用于该代理的最大本地磁盘。<br>-将日志配置为代理自身的运行状况。|


> [!NOTE]
> 尽管可以在 JSON 或 XML 格式设置诊断扩展的配置，但在 Azure 门户中完成的任何配置将始终存储为 JSON。 如果将 XML 与其他配置方法一起使用，然后使用 Azure 门户更改配置，则设置将更改为 JSON。

## <a name="resource-manager-template"></a>资源管理器模板
有关使用 Azure 资源管理器模板部署诊断扩展的信息，请参阅[将监视和诊断与 WINDOWS VM 和 azure 资源管理器模板配合使用](../../virtual-machines/extensions/diagnostics-template.md)。 

## <a name="azure-cli-deployment"></a>Azure CLI 部署
Azure CLI 可用于将 Azure 诊断扩展部署到使用[az vm extension 集](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set)的现有虚拟机，如以下示例中所示。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

受保护的设置在配置架构的[privateconfig.json 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定义。 下面是一个定义存储帐户的受保护设置文件的最小示例。 有关专用设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#privateconfig-element)。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
公共设置是在配置架构的[公共元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义的。 下面是允许收集诊断基础结构日志、单个性能计数器和单个事件日志的公共设置文件的最小示例。 有关公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell 部署
可以使用 PowerShell 将 Azure 诊断扩展部署到现有虚拟机，如以下示例中[所示。](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

专用设置是在[privateconfig.json 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定义的，而公共设置是在配置架构的[公共元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义的。 你还可以选择将存储帐户的详细信息指定为 AzVMDiagnosticsExtension cmdlet 的参数，而不是在专用设置中包含它们。

下面是配置文件的最小示例，该配置文件允许收集诊断基础结构日志、单个性能计数器和单个事件日志。 有关私有和公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

另请参阅[使用 PowerShell 在运行 Windows 的虚拟机中启用 Azure 诊断](../../virtual-machines/extensions/diagnostics-windows.md)。

## <a name="data-storage"></a>数据存储
下表列出了从诊断扩展收集的不同类型的数据，以及这些数据是否存储为表或 blob。 表中存储的数据还可以存储在 blob 中，具体取决于公共配置中的[StorageType 设置](diagnostics-extension-schema-windows.md#publicconfig-element)。


| data | 存储类型 | 说明 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | 表 | 诊断监视器和配置更改。 |
| WADDirectoriesTable | 表 | 诊断监视器监视的目录。  这包括 IIS 日志、IIS 失败请求日志和自定义目录。  在“容器”字段中指定 blob 日志文件的位置，在 RelativePath 字段中指定 blob 的名称。  AbsolutePath 字段指示文件的位置和名称，就像文件是存在于 Azure 虚拟机上一样。 |
| WadLogsTable | 表 | 使用跟踪侦听器以代码编写的日志。 |
| WADPerformanceCountersTable | 表 | 性能计数器。 |
| WADWindowsEventLogsTable | 表 | Windows 事件日志。 |
| wad-wad-iis-failedreqlogfiles | Blob | 包含 IIS 失败请求日志中的信息。 |
| wad-iis-logfiles | Blob | 包含有关 IIS 日志的信息。 |
| 客户 | Blob | 基于配置诊断监视器监视的目录的自定义容器。  此 blob 容器的名称会在 WADDirectoriesTable 中指定。 |

## <a name="tools-to-view-diagnostic-data"></a>用于查看诊断数据的工具
将数据传输到存储空间以后，可以使用多个工具来查看这些数据。 例如：

* Visual Studio 中的服务器资源管理器 - 如果安装了 Azure Tools for Microsoft Visual Studio，则可以在服务器资源管理器中使用“Azure 存储”节点从 Azure 存储帐户查看只读 Blob 和表数据。 既可以从本地存储模拟器帐户显示数据，也可以从为 Azure 创建的存储帐户显示数据。 有关详细信息，请参阅[使用服务器资源管理器浏览和管理存储资源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)是一款独立应用，可用于在 Windows、OSX 和 Linux 上轻松处理 Azure 存储数据。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 包括 Azure 诊断管理器，该诊断管理器用于查看、下载和管理在 Azure 上运行的应用程序收集的诊断数据。

## <a name="next-steps"></a>后续步骤
- 有关将监视数据转发到 Azure 事件中心的详细信息，请参阅[将数据从 Windows Azure 诊断扩展发送到事件中心](diagnostics-extension-stream-event-hubs.md)。
