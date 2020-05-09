---
title: 安装并配置 Windows Azure 诊断扩展 (WAD)
description: 了解如何在 Azure 存储帐户中收集 Azure 诊断数据，以便可以使用几种可用工具之一查看这些数据。
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: dd18fd484ac456f0c38cd6d9b73a2395a08ad5d0
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883101"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>安装并配置 Windows Azure 诊断扩展 (WAD)
Azure 诊断扩展是 Azure Monitor 中的一个代理，可从 Azure 虚拟机的来宾操作系统和工作负荷及其他计算资源收集监视数据。 本文详细介绍如何安装并配置 Windows 诊断扩展，以及如何将数据存储在 Azure 存储帐户中。

诊断扩展作为 Azure 中的[虚拟机扩展](../../virtual-machines/extensions/overview.md)实现，因此，它支持相同的、使用资源管理器模板、PowerShell 和 CLI 的安装选项。 有关安装和维护虚拟机扩展的详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](../../virtual-machines/extensions/features-windows.md)。

## <a name="install-with-azure-portal"></a>使用 Azure 门户安装
可以通过 Azure 门户在单个虚拟机上安装并配置诊断扩展。门户中会提供一个界面，而不是直接让你进行配置。 启用诊断扩展时，它会自动使用包含最常见性能计数器和事件的默认配置。 可以根据具体的要求修改此默认配置。

> [!NOTE]
> 有些诊断扩展设置无法使用 Azure 门户进行配置，包括将数据发送到 Azure 事件中心的设置。 对于这些设置，必须使用其他配置方法之一。

1. 在 Azure 门户中打开虚拟机的菜单。
2. 在 VM 菜单的“监视”部分单击“诊断设置”。  
3. 如果尚未启用诊断扩展，请单击“启用来宾级监视”。 
4. 随后将为 VM 创建一个新的 Azure 存储帐户，其名称基于 VM 资源组的名称。 可以选择“代理”选项卡将 VM 附加到另一个存储帐户。 

![诊断设置](media/diagnostics-extension-windows-install/diagnostic-settings.png)


启用诊断扩展后，可以修改默认配置。 下表描述了可在不同选项卡中修改的选项。 某些选项提供“自定义”命令用于指定更详细的配置；有关不同设置的详细信息，请参阅 [Windows 诊断扩展架构](diagnostics-extension-schema-windows.md)。 

| 选项卡 | 说明 |
|:---|:---|
| 概述 | 显示当前配置，以及其他选项卡的链接。 |
| 性能计数器 | 选择要收集的性能计数器以及各自的采样率。  |
| 日志 | 选择要收集的日志数据。 这包括 Windows 事件日志、IIS 日志、.NET 应用程序日志和 ETW 事件。  |
| 故障转储 | 为不同的进程启用故障转储。 |
| 接收器 | 启用数据接收器，以便除了将数据发送到 Azure 存储以外，还发送到目标。<br>Azure Monitor - 将性能数据发送到 Azure Monitor 指标。<br>Application Insights - 将数据发送到 Application Insights 应用程序。 |
| Agent | 修改代理的以下配置：<br>- 更改存储帐户。<br>- 指定代理使用的最大本地磁盘数。<br>- 配置代理本身运行状况的日志。|


> [!NOTE]
> 尽管诊断扩展的配置可以采用 JSON 或 XML 格式，但在 Azure 门户中完成的任何配置始终以 JSON 格式存储。 如果将 XML 与其他配置方法配合使用，然后使用 Azure 门户更改配置，则设置将更改为 JSON 格式。

## <a name="resource-manager-template"></a>Resource Manager 模板
请参阅[将监视和诊断与 Windows VM 和 Azure 资源管理器模板配合使用](../../virtual-machines/extensions/diagnostics-template.md)，了解如何使用 Azure 资源管理器模板部署诊断扩展。 

## <a name="azure-cli-deployment"></a>Azure CLI 部署
可以在 Azure CLI 中使用 [az vm extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) 将 Azure 诊断扩展部署到现有的虚拟机，如以下示例所示。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

受保护的设置在配置架构的 [PrivateConfig 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定义。 下面是用于定义存储帐户的受保护设置文件的极简示例。 有关专用设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#privateconfig-element)。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
公共设置在配置架构的 [Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义。 下面是公共设置文件的极简示例，该示例启用诊断基础结构日志、单个性能计数器和单个事件日志的收集。 有关公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。

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
可以在 PowerShell 中使用 [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) 将 Azure 诊断扩展部署到现有的虚拟机，如以下示例所示。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

专用设置在 [PrivateConfig 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定义，公共设置在配置架构的 [Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义。 还可以选择将存储帐户详细信息指定为 Set-AzVMDiagnosticsExtension cmdlet 的参数，而无需在专用设置中包含这些信息。

下面是配置文件的极简示例，该示例启用诊断基础结构日志、单个性能计数器和单个事件日志的收集。 有关专用和公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
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
下表列出了从诊断扩展收集的不同数据类型，以及这些数据是以表还是 Blob 的形式存储。 表中存储的数据还可以根据公共配置中的 [StorageType 设置](diagnostics-extension-schema-windows.md#publicconfig-element)存储在 Blob 中。


| 数据 | 存储类型 | 说明 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | 表 | 诊断监视器和配置更改。 |
| WADDirectoriesTable | 表 | 诊断监视器正在监视的目录。  这包括 IIS 日志、IIS 失败请求日志和自定义目录。  在“容器”字段中指定 blob 日志文件的位置，在 RelativePath 字段中指定 blob 的名称。  AbsolutePath 字段指示文件的位置和名称，就像文件存在于 Azure 虚拟机上一样。 |
| WadLogsTable | 表 | 使用跟踪侦听器在代码中写入的日志。 |
| WADPerformanceCountersTable | 表 | 性能计数器。 |
| WADWindowsEventLogsTable | 表 | Windows 事件日志。 |
| wad-iis-failedreqlogfiles | Blob | 包含 IIS 失败请求日志中的信息。 |
| wad-iis-logfiles | Blob | 包含有关 IIS 日志的信息。 |
| "custom" | Blob | 一个自定义容器，该容器取决于诊断监视器所监视的目录的配置方式。  此 blob 容器的名称在 WADDirectoriesTable 中指定。 |

## <a name="tools-to-view-diagnostic-data"></a>用于查看诊断数据的工具
将数据传输到存储后，可以使用多个工具进行查看。 例如：

* Visual Studio 中的服务器资源管理器 - 如果已安装 Azure Tools for Microsoft Visual Studio，则可以在服务器资源管理器中使用“Azure 存储”节点从 Azure 存储帐户查看只读 Blob 和表数据。 既可以从本地存储模拟器帐户显示数据，也可以从为 Azure 创建的存储帐户显示数据。 有关详细信息，请参阅[使用服务器资源管理器浏览和管理存储资源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一款独立应用，可用于在 Windows、OSX 和 Linux 上轻松处理 Azure 存储数据。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 随附 Azure 诊断管理器，可用于查看、下载和管理 Azure 中运行的应用程序收集的诊断数据。

## <a name="next-steps"></a>后续步骤
- 有关将监视数据转发到 Azure 事件中心的详细信息，请参阅[将数据从 Windows Azure 诊断扩展发送到事件中心](diagnostics-extension-stream-event-hubs.md)。
