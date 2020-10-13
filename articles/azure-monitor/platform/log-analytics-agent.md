---
title: Log Analytics 代理概述
description: 本主题可帮助你了解如何使用 Log Analytics 收集数据并监视托管在 Azure、本地或其他云环境中的计算机。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 9f0a7b6f68c5a3adeb320fd18bec2f195a833dbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309993"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics 代理概述
Azure Log Analytics 代理会从 Windows 和 Linux 虚拟机中收集遥测数据，这些虚拟机在任何云、本地计算机上以及通过 [System Center Operations Manager](/system-center/scom/) 监视，并将其收集的数据发送到 Azure Monitor 中的 Log Analytics 工作区。 Log Analytics 代理还支持 Azure Monitor 中的见解和其他服务，例如[用于 VM 的 Azure Monitor](../insights/vminsights-enable-overview.md)、[Azure 安全中心](../../security-center/index.yml)和 [Azure 自动化](../../automation/automation-intro.md)。 本文详细概述了代理、系统和网络要求以及部署方法。

> [!NOTE]
> 你可能还会看到 Log Analytics 代理称为 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理。

## <a name="comparison-to-azure-diagnostics-extension"></a>与 Azure 诊断扩展的比较
Azure Monitor 中的 [Azure 诊断扩展](diagnostics-extension-overview.md)也可用于从 Azure 虚拟机的来宾操作系统收集监视数据。 根据自己的需求，你可以选择使用任一代理或两者。 如需详细了解 Azure Monitor 代理的比较，请参阅 [Azure Monitor 代理概述](agents-overview.md)。 

需要考虑的主要区别是：

- Azure 诊断扩展只能在 Azure 中的虚拟机中使用。 Log Analytics 代理可在 Azure、其他云和本地中的虚拟机中使用。
- Azure 诊断扩展将数据发送到 Azure 存储、[Azure Monitor 指标](data-platform-metrics.md)（仅限 Windows）和事件中心。 Log Analytics 代理会将数据发送到 [Azure Monitor 日志](data-platform-logs.md)。
- [解决方案](../monitor-reference.md#insights-and-core-solutions)、[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md) 和其他服务（如 [Azure 安全中心](../../security-center/index.yml)）需要 Log Analytics 代理。

## <a name="costs"></a>成本
Log Analytics 代理不收取任何费用，但引入的数据可能产生费用。 请查看[使用 Azure Monitor 日志管理使用情况和成本](manage-cost-storage.md)，获取有关 Log Analytics 工作区中收集的数据定价的详细信息。

## <a name="supported-operating-systems"></a>支持的操作系统

 有关 Log Analytics 代理支持的 Windows 和 Linux 操作系统版本的列表，请参阅 [支持的操作系统](agents-overview.md#supported-operating-systems) 。 


## <a name="data-collected"></a>收集的数据
下表列出了在配置 Log Analytics 工作区后即可从所有连接的代理收集的数据的类型。 请参阅 [Azure Monitor 监视的内容是什么？](../monitor-reference.md)，获取使用 Log Analytics 代理收集其他类型的数据的见解、解决方案和其他解决方案的列表。

| 数据源 | 说明 |
| --- | --- |
| [Windows 事件日志](data-sources-windows-events.md) | 发送到 Windows 事件日志记录系统的信息。 |
| [Syslog](data-sources-syslog.md)                     | 发送到 Linux 事件日志记录系统的信息。 |
| [“性能”](data-sources-performance-counters.md)  | 测量操作系统和工作负载不同方面性能的数值。 |
| [IIS 日志](data-sources-iis-logs.md)                 | 在来宾操作系统上运行的 IIS 网站的使用情况信息。 |
| [自定义日志](data-sources-custom-logs.md)           | Windows 和 Linux 计算机上的文本文件中的事件。 |

## <a name="data-destinations"></a>数据目标
Log Analytics 代理将数据发送到 Azure Monitor 中的 Log Analytics 工作区。 Windows 代理可以是多宿主的，将数据发送到多个工作区和 System Center Operations Manager 管理组。 Linux 代理只能发送到一个目标工作区或管理组。

## <a name="other-services"></a>其他服务
适用于 Linux 和 Windows 的代理不仅适用于连接到 Azure Monitor。 其他服务（例如 Azure 安全中心和 Azure Sentinel）依赖于代理及其连接 Log Analytics 工作区。 代理还支持 Azure 自动化以托管混合 Runbook 辅助角色和其他服务，例如 [更改跟踪](../../automation/change-tracking.md)、 [更新管理](../../automation/update-management/update-mgmt-overview.md)和 [Azure 安全中心](../../security-center/security-center-intro.md)。 有关混合 Runbook 辅助角色的详细信息，请参阅 [Azure 自动化混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="workspace-and-management-group-limitations"></a>工作区和管理组限制

有关将代理连接到 Operations Manager 管理组的详细信息，请参阅 [配置代理以便向 Operations Manager 管理组报告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) 。

* 即使连接到 System Center Operations Manager 管理组，Windows 代理也可以连接到最多四个工作区。
* Linux 代理不支持多宿主，只能连接到单个工作区或管理组。


## <a name="security-limitations"></a>安全限制

* Windows 和 Linux 代理支持 [FIPS 140 标准](/windows/security/threat-protection/fips-140-validation)，但 [可能不支持其他类型的强化](agent-linux.md#supported-linux-hardening)。


## <a name="installation-options"></a>安装选项

可通过多种方法安装 Log Analytics 代理并将计算机连接到 Azure Monitor，具体取决于你的要求。 以下部分列出了不同类型的虚拟机的可能方法。
> [!NOTE]
> 不支持克隆已配置 Log Analytics 代理的计算机。 如果该代理已与工作区关联，则此操作将不适用于 "黄金映像"。

### <a name="azure-virtual-machine"></a>Azure 虚拟机

- [用于 VM 的 Azure Monitor](../insights/vminsights-enable-overview.md) 提供多种方法，可大规模启用代理。 这包括安装 Log Analytics 代理和依赖项代理。 
- [Azure 安全中心可](../../security-center/security-center-enable-data-collection.md) 在所有受支持的 Azure vm 和创建的任何新的 azure vm 上预配 Log Analytics 代理，以监视安全漏洞和威胁。
- 适用于 [Windows](../../virtual-machines/extensions/oms-windows.md) 或 [Linux](../../virtual-machines/extensions/oms-linux.md) Log Analytics VM 扩展可以与 Azure 门户、Azure CLI、Azure PowerShell 或 Azure 资源管理器模板一起安装。
- [从 Azure 门户手动](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)安装单个 Azure 虚拟机。


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>本地或其他云中的 Windows 虚拟机 

- [手动](agent-windows.md) 从命令行安装代理。
- 通过 [Azure Automation DSC](agent-windows.md#install-agent-using-dsc-in-azure-automation)自动执行安装。
- 结合使用 [资源管理器模板 Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>本地或其他云中的 Linux 虚拟机

- [手动安装](../learn/quick-collect-linux-computer.md) 托管在 GitHub 上的包装脚本的代理。
- System Center Operations Manager |将[Operations Manager 与 Azure Monitor 集成](./om-agents.md) ，以便从向管理组报告的 Windows 计算机中转发收集的数据。

## <a name="workspace-id-and-key"></a>工作区 ID 和密钥
无论使用何种安装方法，您都需要 Log Analytics 代理连接到的工作区的工作区 ID 和密钥。 从 Azure 门户中的 " **Log Analytics 工作区** " 菜单中选择工作区。 然后在 "**设置**" 部分中选择 "**代理管理**"。 

[![工作区详细信息](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1.2 协议

为了确保传输到 Azure Monitor 日志的数据的安全性，我们强烈建议你将代理配置为至少使用传输层安全性 (TLS) 1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**。  有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](data-security.md#sending-data-securely-using-tls-12)。 

## <a name="network-requirements"></a>网络要求
适用于 Linux 和 Windows 的 agent 通过 TCP 端口443与 Azure Monitor 服务进行出站通信。 如果计算机通过防火墙或代理服务器进行连接以通过 Internet 进行通信，请查看下面的要求以了解所需的网络配置。 如果 IT 安全策略不允许网络上的计算机连接到 Internet，则可以设置 [Log Analytics 网关](gateway.md) ，然后将代理配置为通过网关进行连接，以 Azure Monitor。 然后，代理可以接收配置信息并发送收集的数据。

![Log Analytics 代理通信示意图](./media/log-analytics-agent/log-analytics-agent-01.png)

下表列出了 Linux 和 Windows 代理与 Azure Monitor 日志通信所需的代理和防火墙配置信息。

### <a name="firewall-requirements"></a>防火墙要求

|代理资源|端口 |方向 |绕过 HTTPS 检查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |端口 443 |出站|是 |  
|*.oms.opinsights.azure.com |端口 443 |出站|是 |  
|\* .blob.core.windows.net |端口 443 |出站|是 |
|\* .azure-automation.net |端口 443 |出站|是 |

有关 Azure 政府所需的防火墙信息，请参阅 [Azure 政府管理](../../azure-government/compare-azure-government-global-azure.md#azure-monitor)。 

如果计划使用 Azure 自动化混合 Runbook 辅助角色连接并注册自动化服务以在环境中使用 Runbook 或管理解决方案，则它必须可以访问[针对混合 Runbook 辅助角色配置网络](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的端口号和 URL。 

### <a name="proxy-configuration"></a>代理配置

Windows 和 Linux 代理支持使用 HTTPS 协议通过代理服务器或 Log Analytics 网关与 Azure Monitor 服务进行通信。  并同时支持匿名身份验证和基本身份验证（用户名/密码）。  对于直接连接到服务的 Windows 代理，代理配置在安装过程中指定，或[在部署后](agent-manage.md#update-proxy-settings)从控制面板或使用 PowerShell 指定。  

对于 Linux 代理，代理服务器在安装过程中指定，或者[在安装后](agent-manage.md#update-proxy-settings)通过修改 proxy.conf 配置文件来指定。  Linux 代理的代理配置值具有以下语法：

`[protocol://][user:password@]proxyhost[:port]`

|属性| 说明 |
|--------|-------------|
|协议 | https |
|user | 用于代理身份验证的可选用户名 |
|password | 用于代理身份验证的可选密码 |
|proxyhost | 代理服务器/Log Analytics 网关的地址或 FQDN |
|port | 代理服务器/Log Analytics 网关的可选端口号 |

例如： `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 如果密码中使用了特殊字符（如“\@”），则会收到代理连接错误，因为值解析不正确。  若要解决此问题，请使用 [URLDecode](https://www.urldecoder.org/) 等工具在 URL 中对密码进行编码。  



## <a name="next-steps"></a>后续步骤

* 查看[数据源](agent-data-sources.md)，了解可用于从 Windows 或 Linux 系统收集数据的数据源。 
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和解决方案中收集的数据。 
* 了解[监视解决方案](../insights/solutions.md)如何将功能添加到 Azure Monitor，以及如何将数据收集到 Log Analytics 工作区中。

