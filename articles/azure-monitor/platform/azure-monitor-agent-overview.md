---
title: Azure Monitor 代理概述
description: Azure Monitor 代理概述 (AMA) ，该代理从虚拟机的来宾操作系统中收集监视数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: cc49bec71f6c591ca3036592b0949e3fc7cef48e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263770"
---
# <a name="azure-monitor-agent-overview-preview"></a> (预览版 Azure Monitor 代理概述) 
Azure Monitor 代理 (AMA) 从虚拟机的来宾操作系统收集监视数据，并将其传送到 Azure Monitor。 本文概述了 Azure Monitor 代理，包括如何安装它以及如何配置数据收集。

## <a name="relationship-to-other-agents"></a>与其他代理的关系
Azure Monitor 代理会将 Azure Monitor 当前使用的以下代理替换为从虚拟机收集来宾数据：

- [Log Analytics 代理](log-analytics-agent.md) -将数据发送到 Log Analytics 工作区，并支持用于 VM 的 Azure Monitor 和监视解决方案。
- [诊断扩展](diagnostics-extension-overview.md) -仅 (Windows) 、Azure 事件中心和 azure 存储中的 Azure Monitor 度量值发送数据。
- [Telegraf 代理](collect-custom-metrics-linux-telegraf.md) -仅 (Linux) 将数据发送到 Azure Monitor 度量值。

除了将此功能合并到单个代理外，Azure Monitor 代理还在现有代理上具有以下优势：

- 监视范围。 集中配置不同 Vm 集的不同数据集的集合。  
- Linux 多主页：将数据从 Linux Vm 发送到多个工作区。
- Windows 事件筛选：使用 XPATH 查询来筛选要收集的 Windows 事件。
- 增强的扩展管理： Azure Monitor 代理使用一种新方法来处理比当前 Log Analytics 代理中的管理包和 Linux 插件更为透明且更具控制性的扩展性。

### <a name="changes-in-data-collection"></a>数据收集中的更改
用于定义现有代理的数据收集的方法与其他方法不同，每个方法都有 Azure Monitor 代理解决的难题。

- Log Analytics 代理从 Log Analytics 工作区获取其配置。 这易于集中配置，但难以为不同的虚拟机定义独立定义。 它只能将数据发送到 Log Analytics 工作区。

- 诊断扩展具有每个虚拟机的配置。 这可以轻松定义不同虚拟机的独立定义，但难以集中管理。 它只能将数据发送到 Azure Monitor 指标、Azure 事件中心或 Azure 存储。 对于 Linux 代理，需要开源 Telegraf 代理才能将数据发送到 Azure Monitor 指标。

Azure Monitor 代理使用 [ (DCR) 的数据收集规则 ](data-collection-rule-overview.md) 来配置要从每个代理收集的数据。 数据收集规则实现了大规模的集合设置的可管理性，同时还为计算机的子集启用了唯一的作用域配置。 它们独立于工作区，并且独立于虚拟机，这允许定义一次并在计算机和环境中重复使用。 请参阅 [配置 Azure Monitor 代理的数据收集 (预览版) ](data-collection-rule-azure-monitor-agent.md)。

## <a name="should-i-switch-to-azure-monitor-agent"></a>我应该切换到 Azure Monitor 代理吗？
Azure Monitor 代理使用 Azure Monitor 的 [通用代理进行](agents-overview.md)共存，但在 Azure Monitor 代理公共预览版期间，你可能会考虑将 vm 转换为非当前代理。 做出此决定时，请考虑以下因素。

- **环境要求。** Azure Monitor 代理的受支持的操作系统、环境和网络要求比当前代理要多。 将来的环境支持，例如新的操作系统版本和网络要求类型，最有可能只在 Azure Monitor 代理中提供。 你应通过 Azure Monitor 代理评估你的环境是否受支持。 如果没有，则需要保留当前代理。 如果 Azure Monitor 代理支持你当前的环境，则应考虑转换到它。
- **公共预览版风险容差。** 虽然已针对当前支持的方案全面测试 Azure Monitor 代理，但代理仍处于公共预览阶段。 版本更新和功能改进会频繁发生，并可能引入 bug。 你应在 Vm 上评估可能会停止数据收集的 bug 的风险。 如果数据收集的空白不会对服务造成重大影响，请继续 Azure Monitor 代理。 如果对任何不稳定具有低容差，则应该一直使用正式可用的代理，直至 Azure Monitor 代理达到此状态。
- **当前和新功能要求。** Azure Monitor 代理引入了几种新功能，如筛选、范围和多宿主，但目前还没有用于其他功能（例如自定义日志收集和与解决方案集成）的当前代理的奇偶校验。 Azure Monitor 中的大多数新功能仅适用于 Azure Monitor 代理，因此，超过时间的功能将仅在新代理中可用。 你应考虑 Azure Monitor 代理是否具有你所需的功能，以及你是否可以在不获取新代理中的其他重要功能的情况下临时执行的某些功能。 如果 Azure Monitor 代理具有所需的所有核心功能，请考虑将其转换为。 如果有需要的重要功能，请继续运行当前的代理，直至 Azure Monitor 代理达到奇偶校验。
- **改编的容差。** 如果你正在使用部署脚本和载入模板等资源设置新环境，你应考虑在 Azure Monitor 代理公开发布后是否能够对其进行改编。 如果这种改编的工作量很小，请立即与当前代理保持同步。 如果需要大量工作，请考虑使用新的代理设置新环境。 Azure Monitor 代理应公开上市，并且为2021中的 Log Analytics 代理发布了弃用日期。 一旦开始，最新的代理将支持几年。



## <a name="current-limitations"></a>当前限制
在 Azure Monitor 代理的公共预览版期间，有以下限制：

- Azure Monitor 代理不支持用于 VM 的 Azure Monitor 和 Azure 安全中心等解决方案和见解。 当前支持的唯一方案是使用你配置的数据收集规则收集数据。 
- 数据收集规则必须与用作目标的任何 Log Analytics 工作区在同一区域中创建。
- 目前仅支持 Azure 虚拟机。 当前不支持本地虚拟机、虚拟机规模集、服务器的 Arc、Azure Kubernetes 服务和其他计算资源类型。
- 虚拟机必须具有以下 HTTPS 终结点的访问权限：
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>与其他代理共存
Azure Monitor 代理可以与现有代理共存，因此你可以在评估或迁移期间继续使用其现有功能。 这一点特别重要，因为在支持现有解决方案时公共预览版中有一些限制。 但在收集重复数据时应该小心，因为这可能会使查询结果歪斜，并导致数据引入和保留的额外费用。

例如，用于 VM 的 Azure Monitor 使用 Log Analytics 代理将性能数据发送到 Log Analytics 工作区。 你还可以将工作区配置为从代理收集 Windows 事件和 Syslog 事件。 如果安装 Azure Monitor 代理，并为这些相同的事件和性能数据创建数据收集规则，则会导致数据重复。


## <a name="costs"></a>成本
Azure Monitor 代理无需支付费用，但可能会产生数据引入的费用。 有关 Log Analytics 数据收集和保留以及客户指标的详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/) 。

## <a name="data-sources-and-destinations"></a>数据源和目标
下表列出了当前可以使用数据收集规则与 Azure Monitor 代理一起收集的数据类型，以及可以发送该数据的位置。 若要查看使用 Azure Monitor 代理收集其他类型的数据的见解、解决方案和其他解决方案的列表，请参阅 [Azure Monitor 监视的内容](../monitor-reference.md) 。


Azure Monitor 代理会将数据发送到 Azure Monitor 指标或支持 Azure Monitor 日志的 Log Analytics 工作区。

| 数据源 | Destinations | 说明 |
|:---|:---|:---|
| 性能        | Azure Monitor 指标<br>Log Analytics 工作区 | 测量操作系统和工作负载不同方面性能的数值。 |
| Windows 事件日志 | Log Analytics 工作区 | 发送到 Windows 事件日志记录系统的信息。 |
| Syslog             | Log Analytics 工作区 | 发送到 Linux 事件日志记录系统的信息。 |


## <a name="supported-operating-systems"></a>支持的操作系统
有关 Log Analytics 代理当前支持的 Windows 和 Linux 操作系统版本的列表，请参阅 [支持的操作系统](agents-overview.md#supported-operating-systems) 。



## <a name="security"></a>安全
Azure Monitor 代理无需任何密钥，而是需要 [系统分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。 部署代理之前，必须在每个虚拟机上启用系统分配的托管标识。

## <a name="networking"></a>网络
Azure Monitor 代理支持 Azure 服务标记 (需要) AzureMonitor 和 AzureResourceManager 标记，但尚不能使用 Azure Monitor 私有链接范围或直接代理。

## <a name="install-the-azure-monitor-agent"></a>安装 Azure Monitor 代理
使用下表中的详细信息将 Azure Monitor 代理实现为 [AZURE VM 扩展](../../virtual-machines/extensions/overview.md) 。 

| 属性 | Windows | Linux |
|:---|:---|:---|
| 发布者 | Microsoft Azure。监视器  | Microsoft Azure。监视器 |
| 类型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

使用任意方法安装 Azure Monitor 代理，使用 PowerShell 或 CLI 安装虚拟机代理，包括以下各项。 另外，还可以使用门户在 Azure 订阅中的虚拟机上安装代理，并使用 [配置 Azure Monitor 代理 (预览版) 的数据收集 ](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal)过程中所述的过程来配置数据收集。

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>后续步骤

- [创建数据收集规则](data-collection-rule-azure-monitor-agent.md) 以从代理收集数据并将其发送到 Azure Monitor。
