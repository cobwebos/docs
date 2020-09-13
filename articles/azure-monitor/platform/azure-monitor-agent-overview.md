---
title: Azure Monitor 代理概述
description: Azure Monitor 代理概述 (AMA) ，该代理从虚拟机的来宾操作系统中收集监视数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: ea2fae483da495bce9551899b9646868251f0454
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030821"
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
Azure Monitor 代理目前支持以下操作系统。

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>，7
  - Debian 9、10
  - Oracle Linux 6<sup>1</sup>，7
  - RHEL 6<sup>1</sup>、7
  - SLES 11、12、15
  - Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS

> [!IMPORTANT]
> <sup>1</sup>对于这些分发用于发送 Syslog 数据，必须在安装代理后一次重启 rsyslog 服务。


## <a name="security"></a>安全性
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
