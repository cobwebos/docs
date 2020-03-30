---
title: 安装和配置 Windows Azure 诊断扩展 （WAD）
description: 了解如何在 Azure 存储帐户中收集 Azure 诊断数据，以便可以使用几种可用工具之一查看这些数据。
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672253"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>安装和配置 Windows Azure 诊断扩展 （WAD）
Azure 诊断扩展是 Azure 监视器中的代理，用于从 Azure 虚拟机和其他计算资源的来宾操作系统和工作负荷收集监视数据。 本文提供有关安装和配置 Windows 诊断扩展的详细信息，以及数据在和 Azure 存储帐户中的存储方式的说明。

诊断扩展在 Azure 中作为[虚拟机扩展](../../virtual-machines/extensions/overview.md)实现，因此它支持使用资源管理器模板、PowerShell 和 CLI 的相同安装选项。 有关安装和维护虚拟机扩展的详细信息，请参阅[Windows 的虚拟机扩展和功能](../../virtual-machines/extensions/features-windows.md)。

## <a name="install-with-azure-portal"></a>使用 Azure 门户安装
您可以在 Azure 门户中的单个虚拟机上安装和配置诊断扩展，该虚拟机提供接口，而不是直接使用配置。 启用诊断扩展时，它将自动使用默认配置与最常见的性能计数器和事件。 您可以根据您的特定要求修改此默认配置。

> [!NOTE]
> 无法使用 Azure 门户配置诊断扩展设置，包括将数据发送到 Azure 事件中心。 您必须对这些设置使用其他配置方法之一。

1. 打开 Azure 门户中的虚拟机的菜单。
2. 单击 VM 菜单的 **"监视**"部分中的 **"诊断设置**"。
3. 如果诊断扩展尚未启用，请单击**启用来宾级监视**。
4. 将为具有该名称的 VM 创建新的 Azure 存储帐户，该帐户的名称将基于 VM 的资源组的名称。 您可以通过选择 **"代理"** 选项卡将 VM 附加到其他存储帐户。

![诊断设置](media/diagnostics-extension-windows-install/diagnostic-settings.png)


启用诊断扩展后，可以修改默认配置。 下表描述了可以在不同的选项卡中修改的选项。 某些选项具有 **"自定义"** 命令，允许您指定更详细的配置;有关不同设置的详细信息，请参阅[Windows 诊断扩展架构](diagnostics-extension-schema-windows.md)。

| 选项卡 | 描述 |
|:---|:---|
| 概述 | 显示当前配置，并带有指向其他选项卡的链接。 |
| 性能计数器 | 选择要收集的性能计数器以及每个计数器的采样率。  |
| 日志 | 选择要收集的日志数据。 这包括 Windows 事件日志、IIS 日志、.NET 应用程序日志和 ETW 事件。  |
| 故障转储 | 为不同的进程启用崩溃转储。 |
| 接收器 | 使数据接收器将数据发送到除 Azure 存储之外的目标。<br>Azure 监视器 - 将性能数据发送到 Azure 监视器指标。<br>应用程序见解 - 将数据发送到应用程序见解应用程序。 |
| 代理 | 修改代理的以下配置：<br>- 更改存储帐户。<br>- 指定用于代理的最大本地磁盘。<br>- 为代理本身的运行状况配置日志。|


> [!NOTE]
> 虽然诊断扩展的配置可以在 JSON 或 XML 中格式化，但在 Azure 门户中完成的任何配置将始终存储为 JSON。 如果将 XML 与其他配置方法一起使用，然后使用 Azure 门户更改配置，则设置将更改为 JSON。

## <a name="resource-manager-template"></a>资源管理器模板
有关使用 Azure 资源管理器模板部署诊断扩展[，请参阅将监视和诊断与 Windows VM 和 Azure 资源管理器模板](../../virtual-machines/extensions/diagnostics-template.md)一起使用。 

## <a name="azure-cli-deployment"></a>Azure CLI 部署
Azure CLI 可用于使用[az vm 扩展集](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set)将 Azure 诊断扩展部署到现有虚拟机，如以下示例所示。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

受保护的设置在配置架构的[私有配置元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定义。 下面是定义存储帐户的受保护设置文件的最小示例。 有关私有设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#privateconfig-element)。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
公共设置在配置架构的公共[元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义。 下面是公共设置文件的最小示例，该文件支持收集诊断基础结构日志、单个性能计数器和单个事件日志。 有关公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。

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
PowerShell 可用于使用[Set-AzVM 诊断扩展](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension)将 Azure 诊断扩展部署到现有虚拟机，如以下示例所示。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

私有设置在[Private Config 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定义，而公共设置在配置架构的[Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义。 您还可以选择将存储帐户的详细信息指定为 Set-AzVM 诊断扩展 cmdlet 的参数，而不是将它们包括在私有设置中。

下面是一个配置文件的最小示例，该文件支持诊断基础结构日志、单个性能计数器和单个事件日志的集合。 有关私有和公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。 

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
下表列出了从诊断扩展中收集的不同类型的数据，以及这些数据是作为表存储还是 Blob 存储。 存储在表中的数据也可以存储在 blob 中，具体取决于公共配置中的[StorageType 设置](diagnostics-extension-schema-windows.md#publicconfig-element)。


| 数据 | 存储类型 | 描述 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | 表 | 诊断监视器和配置更改。 |
| WAD 目录表 | 表 | 诊断监视器正在监视的目录。  这包括 IIS 日志、IIS 失败请求日志和自定义目录。  在“容器”字段中指定 blob 日志文件的位置，在 RelativePath 字段中指定 blob 的名称。  AbsolutePath 字段指示文件的位置和名称，就像文件是存在于 Azure 虚拟机上一样。 |
| WadLogsTable | 表 | 使用跟踪侦听器在代码中编写的日志。 |
| WADPerformanceCountersTable | 表 | 性能计数器。 |
| WADWindowsEventLogsTable | 表 | Windows 事件日志。 |
| wad-iis-失败重新日志文件 | Blob | 包含来自 IIS 失败请求日志的信息。 |
| wad-iis-logfiles | Blob | 包含有关 IIS 日志的信息。 |
| "自定义" | Blob | 基于配置由诊断监视器监视的目录的自定义容器。  此 blob 容器的名称会在 WADDirectoriesTable 中指定。 |

## <a name="tools-to-view-diagnostic-data"></a>用于查看诊断数据的工具
将数据传输到存储空间以后，可以使用多个工具来查看这些数据。 例如：

* Visual Studio 中的服务器资源管理器 - 如果安装了 Azure Tools for Microsoft Visual Studio，则可以在服务器资源管理器中使用“Azure 存储”节点从 Azure 存储帐户查看只读 Blob 和表数据。 既可以从本地存储模拟器帐户显示数据，也可以从为 Azure 创建的存储帐户显示数据。 有关详细信息，请参阅[使用服务器资源管理器浏览和管理存储资源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)是一款独立应用，可用于在 Windows、OSX 和 Linux 上轻松处理 Azure 存储数据。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 包括 Azure 诊断管理器，该诊断管理器用于查看、下载和管理在 Azure 上运行的应用程序收集的诊断数据。

## <a name="next-steps"></a>后续步骤
- 有关将监视数据转发到 Azure 事件中心的详细信息，请参阅[将数据从 Windows Azure 诊断扩展发送到事件中心](diagnostics-extension-stream-event-hubs.md)。
