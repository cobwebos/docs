---
title: "使用 Azure Log Analytics 收集环境中的数据 | Microsoft Docs"
description: "本主题有助于了解如何使用 Log Analytics 收集数据并监视托管在本地或其他云环境中的计算机。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: magoedte
ms.openlocfilehash: 85fde471f0d99b976e319d552c6a031d63854cf4
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>使用 Log Analytics 从环境中的计算机收集数据

Azure Log Analytics 可以从驻留在以下环境中的 Windows 或 Linux 计算机收集数据并进行处理：

* 使用 Log Analytics VM 扩展的 [Azure 虚拟机](log-analytics-quick-collect-azurevm.md) 
* 作为物理服务器或虚拟机的数据中心
* Amazon Web Services (AWS) 等云托管服务中的虚拟机

环境中托管的计算机可以直接连接到 Log Analytics，或者，如果已使用 System Center Operations Manager 2012 R2 或 2016 监视这些计算机，则可以将 Operations Manage 管理组与 Log Analytics 集成，并继续维持服务操作进程和策略。  

## <a name="overview"></a>概述

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

在分析和处理收集的数据之前，首先需要为要将数据发送到 Log Analytics 服务的所有计算机安装代理并进行连接。 可使用安装程序、命令行或 Azure 自动化中的 Desired State Configuration (DSC) 在本地计算机上安装代理。 

适用于 Linux 和 Windows 的代理通过 TCP 端口 443 与 Log Analytics 服务进行出站通信；如果计算机连接到防火墙或代理服务器以通过 Internet 进行通信，请查看[对代理进行配置以便与代理服务器或 OMS 网关一起使用](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway)，了解需要应用哪些配置更改。 如果使用 System Center 2016（Operations Manager 或 Operations Manager 2012 R2）监视计算机，该计算机可以与 Log Analytics 服务进行多宿主连接，以便收集数据并将数据转发到该服务，且仍受 [Operations Manager](log-analytics-om-agents.md) 监视。 受 Operations Manager 管理组监视并与 Log Analytics 集成的 Linux 计算机不通过管理组接收数据源配置，也不转发收集的数据。 Windows 代理最多可以向四个工作区报告，而 Linux 代理只支持向单个工作区报告。  

适用于 Linux 和 Windows 的代理不仅可连接到 Log Analytics，还可与 Azure 自动化进行连接，以托管混合 Runbook 辅助角色和管理解决方案（如更改跟踪和更新管理）。  有关混合 Runbook 辅助角色的详细信息，请参阅 [Azure 自动化混合 Runbook 辅助角色](../automation/automation-offering-get-started.md#automation-architecture-overview)。  

如果 IT 安全策略不允许网络上的计算机连接到 Internet，可将代理配置为连接到 OMS 网关，以根据启用的解决方案接收配置信息并发送收集的数据。 有关如何将 Linux 或 Windows 代理配置为通过 OMS 网关与 Log Analytics 服务进行通信的详细信息和步骤，请参阅[使用 OMS 网关将计算机连接到 OMS](log-analytics-oms-gateway.md)。 

> [!NOTE]
> Windows 代理仅支持传输层安全性 (TLS) 1.0 和 1.1。  
> 

## <a name="prerequisites"></a>先决条件
开始之前，请查看以下详细信息，验证是否满足最低系统要求。

### <a name="windows-operating-system"></a>Windows 操作系统
Windows 代理正式支持以下版本的 Windows 操作系统：

* Windows Server 2008 Service Pack 1 (SP1) 或更高版本
* Windows 7 SP1 及更高版本。

#### <a name="network-configuration"></a>网络配置
下面的信息列出了实现 Windows 代理与 Log Analytics 通信所必需的代理和防火墙配置信息。 流量从网络传出到 Log Analytics 服务。 

| 代理资源 | 端口 | 绕过 HTTPS 检查|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | 是 |
|*.oms.opinsights.azure.com | 443 | 是 | 
|* .blob.core.windows.net | 443 | 是 | 
|*.azure-automation.net | 443 | 是 | 

### <a name="linux-operating-systems"></a>Linux 操作系统
以下 Linux 分发版受官方支持。  不过，Linux 代理在未列出的其他发行版上可能也可以运行。  除非另行说明，列出每个主要版本支持所有的次要版本。  

* Amazon Linux 2012.09 到 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)  
* Oracle Linux 5、6 和 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

#### <a name="network-configuration"></a>网络配置
下面的信息列出了实现 Linux 代理与 Log Analytics 通信所必需的代理和防火墙配置信息。 流量从网络传出到 Log Analytics 服务。 

|代理资源| 端口 |  
|------|---------|  
|*.ods.opinsights.azure.com | 端口 443|   
|*.oms.opinsights.azure.com | 端口 443|   
|* .blob.core.windows.net | 端口 443|   
|* .azure-automation.net | 端口 443|  

Linux 代理支持使用 HTTPS 协议通过代理服务器或 OMS 网关与 Log Analytics 服务进行通信。  并同时支持匿名身份验证和基本身份验证（用户名/密码）。  可在安装期间指定代理服务器，也可在安装后通过修改 proxy.conf 配置文件来指定。  

代理配置值具有以下语法：

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 如果代理服务器无需进行身份验证，Linux 代理仍要求提供伪用户名/密码。 这可以是任何用户名或密码。

|属性| 说明 |
|--------|-------------|
|协议 | https |
|user | 用于代理身份验证的可选用户名 |
|password | 用于代理身份验证的可选密码 |
|proxyhost | 代理服务器/OMS 网关的地址或 FQDN |
|端口 | 代理服务器/OMS 网关的可选端口号 |

例如： `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 如果密码中使用了特殊字符（如“@”），则会收到代理连接错误，因为值解析不正确。  若要解决此问题，请使用 [URLDecode](https://www.urldecoder.org/) 等工具在 URL 中对密码进行编码。  

## <a name="install-and-configure-agent"></a>安装并配置代理 
可根据要求使用不同的方法将本地计算机与 Log Analytics 直接连接。 下表详细介绍了每种方法，以便用户确定组织中最适用的方法。

|Source | 方法 | 说明|
|-------|-------------|-------------|
| Windows 计算机|- [手动安装](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [具有 Azure Stack 的资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |可从命令行或使用自动化方法（如 Azure Automation DSC、[System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)）安装 Microsoft Monitoring Agent，或者，如果已在数据中心部署 Microsoft Azure Stack，则可使用 Azure 资源管理器进行安装。| 
|Linux 计算机| [手动安装](log-analytics-quick-collect-linux-computer.md)|调用 GitHub 上托管的包装器脚本安装 Linux 代理。 | 
| System Center Operations Manager|[将 Operations Manager 与 Log Analytics 集成](log-analytics-om-agents.md) | 配置 Operations Manager 和 Log Analytics 之间的集成，以便转发从 Linux 和 Windows 计算机收集的数据，这些计算机向管理组报告。|  

## <a name="next-steps"></a>后续步骤

* 查看[数据源](log-analytics-data-sources.md)，了解可用于从 Windows 或 Linux 系统收集数据的数据源。 

* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。 

* 了解[解决方案](log-analytics-add-solutions.md)如何将功能添加到 Log Analytics，以及如何将数据收集到 OMS 存储库。