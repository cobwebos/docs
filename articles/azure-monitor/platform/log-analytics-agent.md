---
title: Log Analytics 代理概述
description: 本主题可帮助你了解如何使用 Log Analytics 收集数据并监视托管在 Azure、本地或其他云环境中的计算机。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 0a29ee1536c7c808fe7d15c0abe26f27042bc962
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196248"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics 代理概述
之所以开发 Azure Log Analytics 代理，是为了跨任意云中的虚拟机、本地计算机以及通过 [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) 监视的计算机进行全面的管理。 Windows 和 Linux 代理将从不同源收集的数据发送到 Azure Monitor 中的 Log Analytics 工作区，并发送在监视解决方案中定义的任何特有日志或指标。 Log Analytics 代理还支持[用于 VM 的 Azure Monitor](../insights/vminsights-enable-overview.md)、 [azure 安全中心](/azure/security-center/)和[azure 自动化](../../automation/automation-intro.md)等 Azure Monitor 中的见解和其他服务。

本文提供该代理的详细概述、系统和网络要求以及不同的部署方法。

> [!NOTE]
> 可能还会看到称为 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理的 Log Analytics 代理。

> [!NOTE]
> Azure 诊断扩展是一个代理，适用于从计算资源的来宾操作系统中收集监视数据。 请参阅 [Azure Monitor 代理概述](agents-overview.md)，了解不同代理的说明，以及如何按要求选择适当的代理。

## <a name="comparison-to-azure-diagnostics-extension"></a>与 Azure 诊断扩展比较
Azure Monitor 中的 [Azure 诊断扩展](diagnostics-extension-overview.md)还可以用来从 Azure 虚拟机的来宾操作系统收集监视数据。 可以根据要求选择使用任一项，或者二者都使用。 请参阅 [Azure Monitor 代理概述](agents-overview.md)，详细比较 Azure Monitor 代理。 

需要考虑的主要差异为：

- Azure 诊断扩展只能与 Azure 虚拟机配合使用。 Log Analytics 代理可以与 Azure 中的、其他云中的和本地的虚拟机配合使用。
- Azure 诊断扩展将数据发送到 Azure 存储、[Azure Monitor 指标](data-platform-metrics.md)（仅限 Windows）和事件中心。 Log Analytics 代理将数据收集到 [Azure Monitor 日志](data-platform-logs.md)中。
- [解决方案](../monitor-reference.md#insights-and-core-solutions)、[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)和其他服务（例如[Azure 安全中心](/azure/security-center/)）需要 Log Analytics 代理。

## <a name="costs"></a>成本
Log Analytics 代理不会产生费用，但可能会产生数据引入费用。 请查看[通过 Azure Monitor 日志管理使用情况和成本](manage-cost-storage.md)，详细了解在 Log Analytics 工作区中收集的数据的定价。

## <a name="data-collected"></a>收集的数据
下表列出了在配置 Log Analytics 工作区后即可从所有连接的代理收集的数据的类型。 若要查看使用 Log Analytics 代理收集其他类型的数据的见解、解决方案和其他解决方案的列表，请参阅[Azure Monitor 监视的内容](../monitor-reference.md)。

| 数据源 | 说明 |
| --- | --- |
| [Windows 事件日志](data-sources-windows-events.md) | 发送到 Windows 事件日志记录系统的信息。 |
| [Syslog](data-sources-syslog.md)                     | 发送到 Linux 事件日志记录系统的信息。 |
| [“性能”](data-sources-performance-counters.md)  | 数值，用于度量操作系统和工作负载的不同方面的性能。 |
| [IIS 日志](data-sources-iis-logs.md)                 | 在来宾操作系统上运行的 IIS 网站的使用情况信息。 |
| [自定义日志](data-sources-custom-logs.md)           | Windows 和 Linux 计算机上的文本文件中的事件。 |

## <a name="data-destinations"></a>数据目标
Log Analytics 代理将数据发送到 Azure Monitor 中的 Log Analytics 工作区。 Windows 代理可以是多宿主的，用于将数据发送到多个工作区和 System Center Operations Manager 管理组。 Linux 代理只能发送到单个目标。

## <a name="other-services"></a>其他服务
适用于 Linux 和 Windows 的代理不仅适用于连接到 Azure Monitor，还支持 Azure 自动化以托管混合 Runbook 辅助角色和其他服务，如[更改跟踪](../../automation/change-tracking.md)、[更新管理](../../automation/automation-update-management.md)和[Azure 安全中心](../../security-center/security-center-intro.md)。 有关混合 Runbook 辅助角色的详细信息，请参阅 [Azure 自动化混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="installation-and-configuration"></a>安装和配置

使用 Log Analytics 代理收集数据时，需要了解以下各项以规划代理部署：

* 若要从 Windows 代理收集数据，可[将每个代理配置为向一个或多个工作区报告](agent-windows.md)，即使它目前正在向 System Center Operations Manager 管理组报告。 Windows 代理最多可向四个工作区报告。
* Linux 代理不支持多宿主，只能向一个工作区报告。
* Windows 代理支持[FIPS 140 标准](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)，而 Linux 代理不支持。  

如果使用 System Center Operations Manager 2012 R2 或更高版本：

* 每个 Operations Manager 管理组只能[连接到一个工作区](om-agents.md)。
* 向管理组报告的 Linux 计算机必须配置为直接向 Log Analytics 工作区报告。 如果 Linux 计算机已直接向工作区报告，而你想要使用 Operations Manager 来监视这些计算机，请遵循[向 Operations Manager 管理组报告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)的步骤。
* 可以在 Windows 计算机上安装 Log Analytics Windows 代理，并使其向与某个工作区集成的 Operations Manager 以及另一个工作区报告。


有多种方法可安装 Log Analytics 代理，并将计算机连接到 Azure Monitor，具体取决于你的要求。 下表详细介绍了每种方法，以便用户确定组织中最适用的方法。

|Source | 方法 | 说明|
|-------|-------------|-------------|
|Azure VM| [手动从 Azure 门户](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | 指定要从 Log Analytics 工作区部署的 Vm。 |
| | 使用 Azure CLI 或 Azure 资源管理器模板 Log Analytics 适用于[Windows](../../virtual-machines/extensions/oms-windows.md)或[Linux](../../virtual-machines/extensions/oms-linux.md)的 VM 扩展 | 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Azure Monitor 工作区中。 |
| | [用于 VM 的 Azure Monitor](../insights/vminsights-enable-overview.md) | 使用用于 VM 的 Azure Monitor 启用监视时，它将安装 Log Analytics 扩展和代理。 |
| | [Azure 安全中心自动预配](../../security-center/security-center-enable-data-collection.md) | Azure 安全中心可在所有受支持的 Azure Vm 和创建的任何新的 Azure Vm 上预配 Log Analytics 代理，以监视安全漏洞和威胁。 如果启用，则将预配未安装代理的任何新的或现有的虚拟机。 |
| 混合 Windows 计算机| [手动安装](agent-windows.md) | 从命令行安装 Microsoft Monitoring agent。 |
| | [Azure 自动化 DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | 通过 Azure Automation DSC 自动执行安装。 |
| | [具有 Azure Stack 的资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | 如果已在数据中心部署 Microsoft Azure Stack，请使用 Azure 资源管理器模板。| 
| 混合 Linux 计算机| [手动安装](../../azure-monitor/learn/quick-collect-linux-computer.md)|调用 GitHub 上托管的包装器脚本安装 Linux 代理。 | 
| System Center Operations Manager|[将 Operations Manager 与 Log Analytics 集成](../../azure-monitor/platform/om-agents.md) | 配置 Operations Manager 和 Azure Monitor 日志之间的集成，以便将从 Windows 计算机报告收集的数据转发到管理组。|  


## <a name="supported-windows-operating-systems"></a>支持的 Windows 操作系统

Windows 代理官方支持以下版本的 Windows 操作系统：

* Windows Server 2019
* Windows Server 2016，版本1709和1803
* Windows Server 2012，2012 R2
* Windows Server 2008 SP2 （x64）、2008 R2
* Windows 10 企业版和专业版
* Windows 8 企业版和专业版 
* Windows 7 SP1

>[!NOTE]
>虽然 Windows 的 Log Analytics 代理旨在支持服务器监视方案，但我们认识到，你可能会运行 Windows 客户端来支持为服务器操作系统配置和优化的工作负荷。 此代理确实支持 Windows 客户端，但是，我们的监视解决方案并不专注于客户端监视方案，除非明确指出。

## <a name="supported-linux-operating-systems"></a>受支持的 Linux 操作系统

本部分提供有关受支持的 Linux 分发版的详细信息。

从 2018 年 8 月之后发布的版本开始，我们对支持模型进行了以下更改：  

* 仅支持服务器版本，不支持客户端版本。  
* 在任何[Azure Linux 认可的发行版](../../virtual-machines/linux/endorsed-distros.md)上专注于支持。 请注意，新的发行版/版本被 Azure Linux 认可的新的/版本与支持的 Linux 代理之间可能存在一定 Log Analytics 的延迟。
* 列出的每个主版本支持所有的次版本。
* 超出制造商终止支持日期的版本不受支持。  
* 不支持新版本的 AMI。  
* 默认仅支持运行 SSL 1.x 的版本。

>[!NOTE]
>如果使用的是当前不受支持且与我们的支持模型不一致的发行版或版本，我们建议对此存储库创建分支，并接受 Microsoft 支持不会为已分支的代理版本提供帮助。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 （x64）和7（x64）  
* Oracle Linux 6 和7（x64） 
* Red Hat Enterprise Linux Server 6 （x64）、7（x64）和8（x64）
* Debian GNU/Linux 8 和9（x64）
* Ubuntu 14.04 LTS （x86/x64）、16.04 LTS （x64）和 18.04 LTS （x64）
* SUSE Linux Enterprise Server 12 (x64) 和 15 (x64)

>[!NOTE]
>仅 x86_x64 平台（64 位）支持 OpenSSL 1.1.0，任何平台均不支持早于 1.x 版本的 OpenSSL。
>

### <a name="agent-prerequisites"></a>代理先决条件

下表重点介绍了要安装代理的受支持 Linux 发行版所需的包。

|所需程序包 |说明 |最低版本 |
|-----------------|------------|----------------|
|Glibc |    GNU C 库 | 2.5-12 
|Openssl    | OpenSSL 库 | 1.0.x 或 1.1.x |
|Curl | cURL Web 客户端 | 7.15.5 |
|Python-ctype | | 
|PAM | 可插入验证模块 | | 

>[!NOTE]
>收集 Syslog 消息时需要 rsyslog 或 syslog ng。 不支持将 Red Hat Enterprise Linux 版本 5、CentOS 和 Oracle Linux 版本 (sysklog) 上的默认 syslog 守护程序用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。

## <a name="tls-12-protocol"></a>TLS 1.2 协议

为了确保传输中数据的安全性 Azure Monitor 日志，强烈建议将代理配置为至少使用传输层安全性（TLS）1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**。  有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](data-security.md#sending-data-securely-using-tls-12)。 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>适用于 Windows 的 SHA-2 代码签名支持要求
Windows 代理将在2020年8月17日开始独占使用 SHA-2 签名。 此更改会影响客户使用旧操作系统上的 Log Analytics 代理作为任何 Azure 服务（Azure Monitor、Azure 自动化、Azure 更新管理、Azure 更改跟踪、Azure 安全中心、Azure Sentinel、Windows Defender ATP）的一部分。 除非您在旧操作系统版本（Windows 7、Windows Server 2008 R2 和 Windows Server 2008）上运行代理，否则更改不需要任何客户操作。 在旧操作系统版本上运行的客户需要在2020年8月17日之前在其计算机上执行以下操作，否则，其代理将停止向其 Log Analytics 工作区发送数据：

1. 为操作系统安装最新的 Service Pack。 所需的 Service Pack 版本如下：
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. 为操作系统安装 sha-1 Windows 更新的 sha-1 签名，如[Windows 和 WSUS 的 2019 SHA-2 代码签名支持要求](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)中所述
3. 更新到最新版本的 Windows 代理（版本10.20.18029）。
4. 建议将代理配置为[使用 TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12)。 


## <a name="network-requirements"></a>网络要求
适用于 Linux 和 Windows 的代理通过 TCP 端口 443 与 Azure Monitor 服务进行出站通信；如果计算机通过防火墙或代理服务器连接以通过 Internet 进行通信，请查看以下要求来了解所需的网络配置。 如果 IT 安全策略不允许网络上的计算机连接到 Internet，则可以设置 [Log Analytics 网关](gateway.md)并将代理配置为通过该网关连接到 Azure Monitor 日志。 然后，代理可以接收配置信息，并发送根据已在工作区中启用的数据收集规则和监视解决方案收集的数据。

![Log Analytics 代理通信示意图](./media/log-analytics-agent/log-analytics-agent-01.png)

下表列出了 Linux 和 Windows 代理与 Azure Monitor 日志通信所需的代理和防火墙配置信息。

### <a name="firewall-requirements"></a>防火墙要求

|代理资源|端口 |方向 |绕过 HTTPS 检查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |端口 443 |入站和出站|是 |  
|*.oms.opinsights.azure.com |端口 443 |入站和出站|是 |  
|\* .blob.core.windows.net |端口 443 |入站和出站|是 |
|* .azure-automation.net |端口 443 |入站和出站|是 |

有关 Azure 政府版所需的防火墙信息，请参阅[Azure 政府版管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

如果你计划使用 Azure 自动化混合 Runbook 辅助角色连接到自动化服务并向该服务注册以在你的环境中使用 runbook 或管理解决方案，则它必须有权访问为[混合 Runbook 辅助角色配置网络](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的端口号和 url。 

### <a name="proxy-configuration"></a>代理配置

Windows 和 Linux 代理支持使用 HTTPS 协议通过代理服务器或 Log Analytics 网关与 Azure Monitor 进行通信。  并同时支持匿名身份验证和基本身份验证（用户名/密码）。  对于直接连接到服务的 Windows 代理，代理配置在安装过程中指定，或[在部署后](agent-manage.md#update-proxy-settings)从控制面板或使用 PowerShell 指定。  

对于 Linux 代理，代理服务器在安装过程中指定，或者[在安装后](agent-manage.md#update-proxy-settings)通过修改 proxy.conf 配置文件来指定。  Linux 代理的代理配置值具有以下语法：

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 如果代理服务器无需进行身份验证，Linux 代理仍要求提供伪用户名/密码。 这可以是任何用户名或密码。

|属性| 说明 |
|--------|-------------|
|协议 | https |
|user | 用于代理身份验证的可选用户名 |
|password | 用于代理身份验证的可选密码 |
|proxyhost | 代理服务器/Log Analytics 网关的地址或 FQDN |
|port | 代理服务器/Log Analytics 网关的可选端口号 |

例如： `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 如果你在密码中使用特殊字符（如 " \@ "），则会收到代理连接错误，因为未正确分析值。  若要解决此问题，请使用 [URLDecode](https://www.urldecoder.org/) 等工具在 URL 中对密码进行编码。  



## <a name="next-steps"></a>后续步骤

* 查看[数据源](agent-data-sources.md)，了解可用于从 Windows 或 Linux 系统收集数据的数据源。 
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和解决方案中收集的数据。 
* 了解[监视解决方案](../insights/solutions.md)如何将功能添加到 Azure Monitor，以及如何将数据收集到 Log Analytics 工作区中。

