---
title: 使用 Azure Log Analytics 代理收集日志数据 | Microsoft Docs
description: 本主题可帮助你了解如何使用 Log Analytics 收集数据并监视托管在 Azure、本地或其他云环境中的计算机。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 9729c7313f275201fc61bb5c7552066eecb13373
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637982"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>使用 Azure Log Analytics 代理收集日志数据

Azure Log Analytics (OMS) 代理，前称为 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理，是为了对本地计算机、[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) 监视的计算机和任何云中的虚拟机进行全面管理而开发的。 Windows 和 Linux 代理附加到 Log Analytics 工作区，以从不同的源收集数据，并收集监视解决方案中定义的任何独特日志或指标。 

本文提供该代理的详细概述、系统和网络要求以及不同的部署方法。   

## <a name="overview"></a>概述

![Log Analytics 代理通信示意图](./media/log-analytics-agent/log-analytics-agent-01.png)

在分析和处理收集的数据之前，首先需要为要将数据发送到 Log Analytics 服务的所有计算机安装代理并进行连接。 可以使用适用于 Windows 和 Linux 的 Azure Log Analytics VM 扩展在 Azure VM 上安装代理；对于混合环境中的计算机，可以使用 Azure 自动化中的安装程序、命令行或 Desired State Configuration (DSC) 来安装代理。 

适用于 Linux 和 Windows 的代理通过 TCP 端口 443 与 Log Analytics 服务进行出站通信；如果计算机通过防火墙或代理服务器连接以通过 Internet 进行通信，请查看以下要求来了解所需的网络配置。 如果 IT 安全策略不允许网络上的计算机连接到 Internet，则可以设置 [Log Analytics 网关](gateway.md)并将代理配置为通过该网关连接到 Log Analytics。 然后，代理可以接收配置信息，并发送根据已启用的数据收集规则和监视解决方案收集的数据。 

如果使用 System Center Operations Manager 2012 R2 或更高版本监视计算机，该计算机可以与 Log Analytics 服务进行多宿主连接，以便收集数据并将数据转发到该服务，且仍受 [Operations Manager](../../log-analytics/log-analytics-om-agents.md) 监视。 受 Operations Manager 管理组监视并与 Log Analytics 集成的 Linux 计算机不通过管理组接收数据源配置，也不转发收集的数据。 Windows 代理最多可以向四个 Log Analytics 工作区报告，而 Linux 代理只支持向单个工作区报告。  

适用于 Linux 和 Windows 的代理不仅可连接到 Log Analytics，还支持使用 Azure 自动化来托管混合 Runbook 辅助角色和其他服务（例如[更改跟踪](../../automation/automation-change-tracking.md)和[更新管理](../../automation/automation-update-management.md)）。 有关混合 Runbook 辅助角色的详细信息，请参阅 [Azure 自动化混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="supported-windows-operating-systems"></a>支持的 Windows 操作系统
Windows 代理官方支持以下版本的 Windows 操作系统：

* Windows Server 2008 Service Pack 1 (SP1) 或更高版本
* Windows 7 SP1 及更高版本。

## <a name="supported-linux-operating-systems"></a>受支持的 Linux 操作系统
本部分提供有关受支持的 Linux 分发版的详细信息。    

从 2018 年 8 月之后发布的版本开始，我们对支持模型进行了以下更改：  

* 仅支持服务器版本，不支持客户端版本。  
* 始终支持新版本的 [Azure Linux 认可的发行版](../../virtual-machines/linux/endorsed-distros.md)。  
* 列出的每个主版本支持所有的次版本。
* 超出制造商终止支持日期的版本不受支持。  
* 不支持新版本的 AMI。  
* 默认仅支持运行 SSL 1.x 的版本。

如果使用的是当前不受支持且与我们的支持模型不一致的发行版或版本，我们建议对此存储库创建分支，并接受 Microsoft 支持不会为已分支的代理版本提供帮助。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) 和 7 (x64)  
* Oracle Linux 6 和 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) 和 7 (x64)
* Debian GNU/Linux 8 和 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64)、16.04 LTS (x86/x64) 和 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64)

>[!NOTE]
>仅 x86_x64 平台（64 位）支持 OpenSSL 1.1.0，任何平台均不支持早于 1.x 版本的 OpenSSL。
>

## <a name="tls-12-protocol"></a>TLS 1.2 协议
为了确保传输到 Log Analytics 的数据的安全性，我们强烈建议你将代理配置为至少使用传输层安全性 (TLS) 1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**。  有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](../../log-analytics/log-analytics-data-security.md#sending-data-securely-using-tls-12)。 

## <a name="network-firewall-requirements"></a>网络防火墙要求
下面的信息列出了实现 Linux 和 Windows 代理与 Log Analytics 通信所必需的代理和防火墙配置信息。  

|代理资源|端口 |方向 |绕过 HTTPS 检查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |端口 443 |入站和出站|是 |  
|*.oms.opinsights.azure.com |端口 443 |入站和出站|是 |  
|* .blob.core.windows.net |端口 443 |入站和出站|是 |  
|* .azure-automation.net |端口 443 |入站和出站|是 |  


如果计划使用 Azure 自动化混合 Runbook 辅助角色连接并注册自动化服务以在环境中使用 Runbook，则它必须可以访问[针对混合 Runbook 辅助角色配置网络](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的端口号和 URL。 

Windows 和 Linux 代理支持使用 HTTPS 协议通过代理服务器或 Log Analytics 网关与 Log Analytics 服务进行通信。  并同时支持匿名身份验证和基本身份验证（用户名/密码）。  对于直接连接到服务的 Windows 代理，代理配置在安装过程中指定，或[在部署后](agent-manage.md#update-proxy-settings)从控制面板或使用 PowerShell 指定。  

对于 Linux 代理，代理服务器在安装过程中指定，或者[在安装后](agent-manage.md#update-proxy-settings)通过修改 proxy.conf 配置文件来指定。  Linux 代理的代理配置值具有以下语法：

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 如果代理服务器无需进行身份验证，Linux 代理仍要求提供伪用户名/密码。 这可以是任何用户名或密码。

|属性| Description |
|--------|-------------|
|协议 | https |
|user | 用于代理身份验证的可选用户名 |
|password | 用于代理身份验证的可选密码 |
|proxyhost | 代理服务器/Log Analytics 网关的地址或 FQDN |
|port | 代理服务器/Log Analytics 网关的可选端口号 |

例如： `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 如果密码中使用了特殊字符（如“\@”），则会收到代理连接错误，因为值解析不正确。  若要解决此问题，请使用 [URLDecode](https://www.urldecoder.org/) 等工具在 URL 中对密码进行编码。  

## <a name="install-and-configure-agent"></a>安装并配置代理 
可以根据要求使用不同的方法，将 Azure 订阅或混合环境中的计算机直接连接到 Azure Log Analytics。 下表详细介绍了每种方法，以便用户确定组织中最适用的方法。

|源 | 方法 | Description|
|-------|-------------|-------------|
|Azure VM| - 使用 Azure CLI 或 Azure 资源管理器模板通过适用于 [Windows](../../virtual-machines/extensions/oms-windows.md) 或 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 Log Analytics VM 扩展进行安装<br>- [通过 Azure 门户手动安装](../../log-analytics/log-analytics-quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)。 | 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Azure Monitor 工作区中。|
| 混合 Windows 计算机|- [手动安装](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [具有 Azure Stack 的资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |可从命令行或使用自动化方法（如 Azure Automation DSC、[System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)）安装 Microsoft Monitoring Agent，或者，如果已在数据中心部署 Microsoft Azure Stack，则可使用 Azure 资源管理器进行安装。| 
| 混合 Linux 计算机| [手动安装](../../log-analytics/log-analytics-quick-collect-linux-computer.md)|调用 GitHub 上托管的包装器脚本安装 Linux 代理。 | 
| System Center Operations Manager|[将 Operations Manager 与 Log Analytics 集成](../../log-analytics/log-analytics-om-agents.md) | 配置 Operations Manager 和 Log Analytics 之间的集成，以便转发从 Linux 和 Windows 计算机收集的数据，这些计算机向管理组报告。|  

## <a name="next-steps"></a>后续步骤

* 查看[数据源](../../azure-monitor/platform/agent-data-sources.md)，了解可用于从 Windows 或 Linux 系统收集数据的数据源。 

* 了解[日志查询](../../log-analytics/log-analytics-queries.md)以便分析从数据源和解决方案中收集的数据。 

* 了解[监视解决方案](../../azure-monitor/insights/solutions.md)如何将功能添加到 Azure Monitor，以及如何将数据收集到 Log Analytics 工作区中。
