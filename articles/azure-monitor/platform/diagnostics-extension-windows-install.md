---
title: 安装并配置 Microsoft Azure 诊断扩展 (WAD)
description: 了解如何安装和配置 Windows 诊断扩展。 另外，了解如何将数据存储在和 Azure 存储帐户中的说明。
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e6ccba27fb599cb26da86e94d3500f4f806ecb76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328864"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>安装并配置 Microsoft Azure 诊断扩展 (WAD)
[Azure 诊断扩展](diagnostics-extension-overview.md)是 Azure Monitor 中的一个代理，可从 Azure 虚拟机的来宾操作系统和工作负载以及其他计算资源中收集监视数据。 本文详细介绍如何安装并配置 Windows 诊断扩展，以及如何将数据存储在 Azure 存储帐户中。

诊断扩展作为 Azure 中的[虚拟机扩展](../../virtual-machines/extensions/overview.md)实现，因此，它支持使用资源管理器模板、PowerShell 和 CLI 的相同安装选项。 有关安装和维护虚拟机扩展的详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](../../virtual-machines/extensions/features-windows.md)。

## <a name="overview"></a>概述
配置 Microsoft Azure 诊断扩展时，必须指定一个存储帐户，所有指定的数据都将发送到该帐户。 可以选择添加一个或更多数据接收器，以便将数据发送到不同的位置。

- Azure Monitor 接收器 - 将来宾性能数据发送到 Azure Monitor 指标。
- 事件中心接收器 - 将来宾性能和日志数据发送到 Azure 事件中心以在 Azure 外部转发。 无法在 Azure 门户中配置此接收器。


## <a name="install-with-azure-portal"></a>使用 Azure 门户安装
你可以在 Azure 门户中的单个虚拟机上安装并配置诊断扩展，门户会提供一个界面，而不是直接让你进行配置。 启用诊断扩展时，它会自动使用包含最常见性能计数器和事件的默认配置。 可根据具体要求修改此默认配置。

> [!NOTE]
> 以下内容描述了诊断扩展的最常见设置。 有关所有配置选项的详细信息，请参阅 [Windows 诊断扩展架构](diagnostics-extension-schema-windows.md)。

1. 在 Azure 门户中打开虚拟机的菜单。

2. 在 VM 菜单的“监视”部分中单击“诊断设置” 。

3. 如果尚未启用诊断扩展，请单击“启用来宾级监视”。

   ![启用监视](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. 随后将为 VM 创建一个新的 Azure 存储帐户，其名称基于 VM 资源组的名称，并且将选择一组默认的来宾性能计数器和日志。

   ![诊断设置](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. 在“性能计数器”选项卡中，选择要从此虚拟机收集的来宾指标。 对于更高级的选择，请使用“自定义”设置。

   ![性能计数器](media/diagnostics-extension-windows-install/performance-counters.png)

6. 在“日志”选项卡中，选择要从虚拟机中收集的日志。 可以将日志发送到存储或事件中心，但不能将其发送到 Azure Monitor。 使用 [Log Analytics 代理](log-analytics-agent.md)将来宾日志收集到 Azure Monitor。

   ![屏幕截图显示为虚拟机选择不同日志的日志选项卡。](media/diagnostics-extension-windows-install/logs.png)

7. 在“故障转储”选项卡中，指定在发生故障后用于收集内存转储的任何进程。 数据将写入到存储帐户以进行诊断设置，你可以选择指定 blob 容器。

   ![故障转储](media/diagnostics-extension-windows-install/crash-dumps.png)

8. 在“接收器”选项卡中，指定是否将数据发送到 Azure 存储以外的位置。 如果选择“Azure Monitor”，则来宾性能数据将发送到 Azure Monitor 指标。 不能使用 Azure 门户配置事件中心接收器。

   ![屏幕截图显示 "接收器" 选项卡，其中启用了 "将诊断数据发送到 Azure Monitor" 选项。](media/diagnostics-extension-windows-install/sinks.png)
   
   如果尚未启用为虚拟机配置的系统分配的标识，则在使用 Azure Monitor 接收器保存配置时可能会看到以下警告。 单击横幅以启用系统分配的标识。
   
   ![托管实体](media/diagnostics-extension-windows-install/managed-entity.png)

9. 在“代理”中，可以更改存储帐户、设置磁盘配额，并指定是否收集诊断基础结构日志。  

   ![屏幕截图显示 "代理" 选项卡，其中包含用于设置存储帐户的选项。](media/diagnostics-extension-windows-install/agent.png)

10. 单击 **保存** 以保存配置。 

> [!NOTE]
> 尽管诊断扩展的配置可以采用 JSON 或 XML 格式，但在 Azure 门户中完成的任何配置始终以 JSON 格式存储。 如果将 XML 与其他配置方法配合使用，然后使用 Azure 门户更改配置，则设置将更改为 JSON 格式。

## <a name="resource-manager-template"></a>资源管理器模板
有关如何使用 Azure 资源管理器模板部署诊断扩展的信息，请参阅[将监视和诊断与 Windows VM 和 Azure 资源管理器模板配合使用](../../virtual-machines/extensions/diagnostics-template.md)。 

## <a name="azure-cli-deployment"></a>Azure CLI 部署
可以在 Azure CLI 中使用 [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) 将 Azure 诊断扩展部署到现有的虚拟机，如以下示例所示。 

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

公共设置在配置架构的 [Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义。 下面是公共设置文件的极简示例，该示例启用诊断基础结构日志、单个性能计数器和单个事件日志的集合。 有关公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。

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
可以在 PowerShell 中使用 [Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) 将 Azure 诊断扩展部署到现有的虚拟机，如以下示例所示。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

专用设置在 [PrivateConfig 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定义，公共设置在配置架构的 [Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定义。 还可以选择将存储帐户详细信息指定为 Set-AzVMDiagnosticsExtension cmdlet 的参数，而无需在专用设置中包含这些信息。

下面是配置文件的极简示例，该示例启用诊断基础结构日志、单个性能计数器和单个事件日志的集合。 有关专用设置和公共设置的完整详细信息，请参阅[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。 

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
下表列出了从诊断扩展收集的不同数据类型，以及这些数据是以表还是 blob 的形式存储。 表中存储的数据还可以根据公共配置中的 [StorageType 设置](diagnostics-extension-schema-windows.md#publicconfig-element)存储在 blob 中。


| 数据 | 存储类型 | 说明 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | 表 | 诊断监视器和配置更改。 |
| WADDirectoriesTable | 表 | 诊断监视器正在监视的目录。  这包括 IIS 日志、IIS 失败请求日志和自定义目录。  在“容器”字段中指定 blob 日志文件的位置，在 RelativePath 字段中指定 blob 的名称。  AbsolutePath 字段指示文件的位置和名称，就像文件是存在于 Azure 虚拟机上一样。 |
| WadLogsTable | 表 | 使用跟踪侦听器在代码中写入的日志。 |
| WADPerformanceCountersTable | 表 | 性能计数器。 |
| WADWindowsEventLogsTable | 表 | Windows 事件日志。 |
| wad-iis-failedreqlogfiles | Blob | 包含 IIS 失败请求日志中的信息。 |
| wad-iis-logfiles | Blob | 包含有关 IIS 日志的信息。 |
| "custom" | Blob | 一个自定义容器，该容器取决于诊断监视器所监视的配置目录。  此 blob 容器的名称会在 WADDirectoriesTable 中指定。 |

## <a name="tools-to-view-diagnostic-data"></a>用于查看诊断数据的工具
将数据传输到存储空间以后，可以使用多个工具来查看这些数据。 例如：

* Visual Studio 中的服务器资源管理器 - 如果安装了 Azure Tools for Microsoft Visual Studio，则可以在服务器资源管理器中使用“Azure 存储”节点从 Azure 存储帐户查看只读 Blob 和表数据。 既可以从本地存储模拟器帐户显示数据，也可以从为 Azure 创建的存储帐户显示数据。 有关详细信息，请参阅[使用服务器资源管理器浏览和管理存储资源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)是一款独立应用，可用于在 Windows、OSX 和 Linux 上轻松处理 Azure 存储数据。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 包括 Azure 诊断管理器，该诊断管理器用于查看、下载和管理在 Azure 上运行的应用程序收集的诊断数据。

## <a name="next-steps"></a>后续步骤
- 有关将监视数据转发到 Azure 事件中心的详细信息，请参阅[将数据从 Microsoft Azure 诊断扩展发送到事件中心](diagnostics-extension-stream-event-hubs.md)。
