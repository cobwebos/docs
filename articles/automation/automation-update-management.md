---
title: Azure 中的更新管理解决方案
description: 本文旨在帮助你了解如何使用此解决方案来管理 Windows 和 Linux 计算机的更新。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e426f2b90e3ac3ac6bcb9825c7848c76e52a1021
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解决方案

可以使用 Azure 自动化中的更新管理解决方案，为部署在 Azure、本地环境或其他云提供程序中的 Windows 和 Linux 计算机管理操作系统安全更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。

可以直接通过 [Azure 自动化](automation-offering-get-started.md)帐户为虚拟机启用“更新管理”。
若要了解如何通过自动化帐户为虚拟机启用更新管理，请参阅[管理多个虚拟机的更新](manage-update-multi.md)。

## <a name="solution-overview"></a>解决方案概述

由更新管理托管的计算机使用以下配置执行评估和更新部署：

* 用于 Windows 或 Linux 的 Microsoft 监视代理
* 用于 Linux 的 PowerShell 所需状态配置 (DSC)
* 自动化混合 Runbook 辅助角色
* 适用于 Windows 计算机的 Microsoft 更新或 Windows Server 更新服务

下图是针对行为和数据流的概念性视图，说明了解决方案如何评估安全更新并将其应用到工作区中所有连接的 Windows Server 和 Linux 计算机。    

![更新管理流程](media/automation-update-management/update-mgmt-updateworkflow.png)

在计算机执行更新符合性扫描以后，代理会将信息批量转发到 Log Analytics。 在 Windows 计算机上，符合性扫描默认情况下每 12 小时执行一次。 除了按扫描计划扫描，更新符合性扫描还会在更新安装前和更新安装后的 15 分钟内启动，前提是 Microsoft Monitoring Agent (MMA) 重新启动。 使用 Linux 计算机时，符合性扫描默认情况下每 3 小时执行一次。在重新启动 MMA 代理的情况下，15 分钟内就会启动符合性扫描。

解决方案报告计算机更新情况的依据是你被配置为通过什么源来同步。 如果将 Windows 计算机配置为向 WSUS 报告，则结果可能不同于 Microsoft 更新所显示的内容，具体取决于 WSUS 上次通过 Microsoft 更新进行同步的时间。 对于配置为向本地存储库（而不是公共存储库）报告的 Linux 计算机来说，情况也是如此。

可以通过创建计划的部署，在需要更新的计算机上部署和安装软件更新。 归类为“可选”的更新不包括在 Windows 计算机的部署范围内，仅必需更新包括在其内。 计划的部署会显式指定计算机或根据特定的一组计算机的日志搜索结果来选择[计算机组](../log-analytics/log-analytics-computer-groups.md)，从而定义会接收适用更新的目标计算机。 也可通过指定计划来批准和指定一个时段，允许在该时段内安装更新。 通过 Azure 自动化中的 runbook 安装更新。 无法查看这些 runbook，它们不需要任何配置。 更新部署在创建时，会创建一个计划，在指定的时间为包括在内的计算机启动主更新 Runbook。 此主 Runbook 会在每个代理上启动子 Runbook，以便执行所需更新的安装。

目标计算机会按更新部署中指定的日期和时间，以并行方式执行部署。 首先会执行扫描，验证是否仍然需要更新，如果是，则会安装相应的更新。 对于 WSUS 客户端计算机来说，如果更新未在 WSUS 中获得批准，则更新部署会失败。

## <a name="clients"></a>客户端

### <a name="supported-client-types"></a>支持的客户端类型

下表显示了支持的操作系统列表： 

|操作系统  |说明  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 仅支持更新评估         |
|Windows Server 2008 R2 SP1 和更高版本     |Windows Server 2008 R2 SP1 需要 .NET Framework 4.5 和 WMF 5.0 或更高版本        |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理必须具有访问更新存储库的权限。        |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|Ubuntu 12.04 LTS 和更高版本 x86/x64       |Linux 代理必须具有访问更新存储库的权限。         |

### <a name="unsupported-client-types"></a>不支持的客户端类型

下表列出了不支持的操作系统：

|操作系统  |说明  |
|---------|---------|
|Windows 客户端     | 不支持客户端操作系统（Windows 7、Windows 10 等）。        |
|Windows Server 2016 Nano Server     | 不支持       |

### <a name="client-requirements"></a>客户端要求

#### <a name="windows"></a>Windows

Windows 代理也必须配置为与 Windows Server Update Services (WSUS) 服务器通信或有权访问 Microsoft 更新。 另外，System Center Configuration Manager 无法同时管理 Windows 代理。 需要 [Windows 代理](../log-analytics/log-analytics-agent-windows.md)。 如果加入 Azure VM，则会自动安装此代理。

#### <a name="linux"></a>Linux

对于 Linux，计算机必须有权访问专用或公共的更新存储库。 此解决方案不支持适用于 Linux 且配置为向多个 Log Analytics 工作区报告的 OMS 代理。

若要详细了解如何安装适用于 Linux 的 OMS 代理并下载最新版本，请参阅[适用于 Linux 的 Operations Management Suite 代理](https://github.com/microsoft/oms-agent-for-linux)。 若要了解如何安装适用于 Windows 的 OMS 代理，请参阅[适用于 Windows 的 Operations Management Suite 代理](../log-analytics/log-analytics-windows-agent.md)。  

## <a name="permissions"></a>权限
若要创建和管理更新部署，需要特定的权限。 若要详细了解这些权限，请访问[基于角色的访问 - 更新管理](automation-role-based-access-control.md#update-management) 

## <a name="solution-components"></a>解决方案组件
此解决方案包含以下资源，这些资源添加到自动化帐户和直接连接的代理或 Operations Manager 连接的管理组。

### <a name="hybrid-worker-groups"></a>混合辅助角色组
启用此解决方案以后，任何直接连接到 Log Analytics 工作区的 Windows 计算机都会自动配置为混合 Runbook 辅助角色，为此解决方案随附的 Runbook 提供支持。 此方案管理的每个 Windows 计算机都会作为“系统混合辅助角色组”列在自动化帐户的“混合辅助角色组”页中，其命名依据命名约定 *Hostname FQDN_GUID*。 不能以在帐户中有 Runbook 的这些组为目标，否则会失败。 这些组只能用于为管理解决方案提供支持。

不过，只要将同一个帐户同时用于解决方案和混合 Runbook 辅助角色组成员身份，即可将 Windows 计算机添加到自动化帐户的混合 Runbook 辅助角色组，为自动化 Runbook 提供支持。 此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。

### <a name="management-packs"></a>管理包

如果 System Center Operations Manager 管理组已连接到 Log Analytics 工作区，则会在 Operations Manager 中安装以下管理包。 在添加此解决方案以后，这些管理包也会安装在直接连接的 Windows 计算机上。 这些管理包不需进行配置或管理。

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

有关如何更新解决方案管理包的详细信息，请参阅[将 Operations Manager 连接到 Log Analytics](../log-analytics/log-analytics-om-agents.md)。

### <a name="confirm-non-azure-machines-are-onboarded"></a>确认已加入非 Azure 计算机

若要确认直接连接的计算机是否正在与 Log Analytics 通信，可在数分钟后运行以下日志搜索：

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

在 Windows 计算机上，可以通过查看以下内容来验证代理与 Log Analytics 的连接：

1.  在控制面板中打开 Microsoft Monitoring Agent，此时该代理会在“Azure Log Analytics (OMS)”选项卡上显示一条消息，指出“Microsoft Monitoring Agent 已成功连接到 Microsoft Operations Management Suite 服务”。   
2.  打开 Windows 事件日志，导航到“应用程序和服务日志\Operations Manager”，搜索来自源服务连接器的事件 ID 3000 和 5002。 这些事件指示计算机已注册到 Log Analytics 工作区并且正在接收配置。  

如果代理无法与 Log Analytics 通信且已配置为通过防火墙或代理服务器与 Internet 通信，则请参阅 [Windows 代理的网络配置](../log-analytics/log-analytics-agent-windows.md)或 [Linux 代理的网络配置](../log-analytics/log-analytics-agent-linux.md)，确认防火墙或代理服务器是否已正确配置。

> [!NOTE]
> 如果 Linux 系统配置为与代理或 OMS 网关通信，并且你将载入此解决方案，请执行以下命令更新 *proxy.conf* 权限，向 omiuser 组授予对文件的读取权限：  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

执行评估后，新添加的 Linux 代理会显示状态“已更新”。 此过程可能需要长达 6 小时的时间。

若要确认 Operations Manager 管理组是否正在与 Log Analytics 通信，请参阅[验证 Operations Manager 与 OMS 的集成](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms)。

## <a name="data-collection"></a>数据收集

### <a name="supported-agents"></a>支持的代理
下表介绍了该解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |该解决方案从 Windows 代理收集有关系统更新的信息，并启动所需更新的安装。 |
| Linux 代理 |是 |该解决方案从 Linux 代理收集有关系统更新的信息，并在支持的发行版上启动所需更新的安装。 |
| Operations Manager 管理组 |是 |该解决方案从已连接的管理组中的代理收集有关系统更新的信息。<br>从 Operations Manager 代理到 Log Analytics 的直接连接不是必须的。 数据将从管理组转发到 Log Analytics 工作区。 |

### <a name="collection-frequency"></a>收集频率
对于每台托管的 Windows 计算机，每天执行两次扫描。 每隔 15 分钟就会调用一次 Windows API，以便查询上次更新时间，从而确定状态是否已更改，并在状态已更改的情况下启动符合性扫描。 对于每台托管的 Linux 计算机，每 3 小时执行一次扫描。

可能需要 30 分钟到 6 小时的时间，仪表板才会显示受托管计算机提供的已更新数据。   

## <a name="viewing-update-assessments"></a>查看更新评估
在自动化帐户中单击“更新管理”查看计算机的状态。

此视图提供有关计算机、缺少的更新、更新部署和计划的更新部署的信息。

可以在列表中选择相应的项运行日志搜索，以便返回有关计算机、更新或部署的信息。 这会打开“日志搜索”页，其中显示了选定项的查询。

## <a name="installing-updates"></a>安装更新

对工作区中的所有 Linux 和 Windows 计算机进行更新评估后，即可通过创建“更新部署”安装所需的更新。 更新部署是为一台或多台计算机计划的所需更新安装。 除了应包括在部署范围内的计算机或计算机组，还请指定部署的日期和时间。 若要详细了解计算机组，请参阅 [Log Analytics 中的计算机组](../log-analytics/log-analytics-computer-groups.md)。 在更新部署中包括计算机组时，只会在创建计划时对组成员身份评估一次。 不会反映对组所做的后续更改。 要解决此问题，请删除计划的更新部署，并重新创建它。

> [!NOTE]
> 默认情况下，从 Azure Marketplace 部署的 Windows VM 设置为从 Windows 更新服务接收自动更新。 将此解决方案或 Windows VM 添加到工作区后，该行为不会改变。 如果不主动通过此解决方案管理更新，系统会应用默认行为（即自动应用更新）。

若要避免在 Ubuntu 上的维护时段外应用更新，请重新配置无人参与升级包，禁用自动更新。 有关如何配置此包的信息，请参阅 [Ubuntu Server 指南中的自动更新主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

对于从 Azure Marketplace 中提供的按需 Red Hat Enterprise Linux (RHEL) 映像创建的虚拟机，已进行注册，以访问 Azure 中部署的 [Red Hat 更新基础结构 (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)。 对于任何其他 Linux 分发，必须按照其所支持的方法从发行版联机文件存储库对其进行更新。  

## <a name="viewing-missing-updates"></a>查看缺少的更新

单击“缺少的更新”可查看计算机中缺少的更新列表。 将列出每项更新，并显示有关需要更新的计算机、操作系统的信息，以及其他信息的链接。 可以选择每项更新，然后，“日志搜索”页会显示有关更新的更多详细信息。

## <a name="viewing-update-deployments"></a>查看更新部署

单击“更新部署”可查看现有更新部署的列表。 在列表中单击任一更新部署会打开该更新部署的“更新部署运行”页。

![更新部署结果的概述](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>创建更新部署

通过单击屏幕顶部的“计划更新部署”按钮打开“新建更新部署”页，创建新的更新部署。 必须提供下表中属性的值：

| 属性 | 说明 |
| --- | --- |
| 名称 |用于标识更新部署的唯一名称。 |
|操作系统| Linux 或 Windows|
| 要更新的计算机 |选择已保存的搜索，或者从下拉列表中选择“计算机”并选择单个计算机 |
|更新分类|选择所需的所有更新分类|
|要排除的更新|输入要排除的且不包含 KB 前缀的所有 KB|
|计划设置|选择启动时间，然后选择任“一次”或“重复”|
| 维护时段 |为更新设置的分钟数。 该值不能小于 30 分钟，且不能大于 6 小时 |

## <a name="search-logs"></a>搜索日志

除了门户中提供的详细信息以外，还可以针对日志执行搜索。 在打开“更改跟踪”页的情况下，单击“Log Analytics”打开“日志搜索”页

### <a name="sample-queries"></a>示例查询

下表提供了此解决方案收集的更新记录的示例日志搜索：

| 查询 | 说明 |
| --- | --- |
|更新<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; project Computer, Title, KBID, Classification, PublishedDate |缺少的更新的所有计算机<br>添加下列其中一项以限制 OS：<br>OSType = "Windows"<br>OSType == "Linux" |
| 更新<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; where Computer == "ContosoVM1.contoso.com"<br>&#124; project Computer, Title, KBID, Product, PublishedDate |特定计算机缺少的更新（请将相关值替换成自己的计算机名称）|
| 事件<br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "Security Updates" or Classification == "Critical Updates")<br>&#124; where UpdateState == "Needed" and Optional == false <br>&#124; distinct Computer)) |缺少关键或所需安全更新的计算机的错误事件 |
| 更新<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; distinct Title |所有计算机明显缺少的更新 | 
| UpdateRunProgress<br>&#124; where InstallationStatus == "failed" <br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |其更新在更新运行中失败的计算机<br>添加下列其中一项以限制 OS：<br>OSType = "Windows"<br>OSType == "Linux" | 
| 更新<br>&#124; where OSType == "Linux"<br>&#124; where UpdateState != "Not needed" and (Classification == "Critical Updates" or Classification == "Security Updates")<br>&#124; summarize AggregatedValue = count() by Computer |提供可解决关键或安全漏洞的更新包的 Linux 计算机列表 | 
| UpdateRunProgress<br>&#124; where UpdateRunName == "DeploymentName"<br>&#124; summarize AggregatedValue = count() by Computer|在此更新运行中进行了更新的计算机（请将相关值替换为更新部署名称） | 

## <a name="integrate-with-system-center-configuration-manager"></a>集成 System Center Configuration Manager

在软件更新管理 (SUM) 周期中，已经投资购买 System Center Configuration Manager 来管理电脑、服务器和移动设备的客户还可以依赖其在管理软件更新方面的优势和成熟度。

若要了解如何将 OMS 更新管理解决方案与 System Center Configuration Manager 集成，请参阅[将 System Center Configuration Manager 与 OMS 更新管理集成](oms-solution-updatemgmt-sccmintegration.md)。

## <a name="patching-linux-machines"></a>修补 Linux 计算机

下列部分说明了 Linux 修补的潜在问题。

### <a name="package-exclusion"></a>包排除

在某些 Linux 版本（例如 Red Hat Enterprise Linux）中，可以通过包执行 OS 级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理在更新包时使用的方法与管理员在 Linux 计算机本地更新包的方法一样，因此，此行为是符合预期的。

若要避免通过更新管理运行来更新 OS 版本，可以使用“排除”功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为：redhat-release-server.x86_64

![适用于 Linux 的要排除的包](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>未应用的安全修补程序

将更新部署到 Linux 计算机时，可以选择更新分类。 这会筛选已应用到满足指定条件的计算机的更新。 部署更新时，会在计算机本地应用此筛选器。 由于更新管理在云中执行更新扩充，更新管理中的某些更新可能标记为存在安全影响，不过，在本地计算机上不会显示该信息。 如果向 Linux 计算机应用关键更新，可能有些更新不会标记为对该计算机造成安全影响，因此不会应用这些更新。 但是，更新管理仍可能报告该计算机不合规，因为其中包含有关更新的其他信息。

由于使用的修补模型不同，在 openSUSE Linux 上可能无法通过更新分类部署更新。

## <a name="troubleshooting"></a>故障排除

此部分介绍如何排查“更新管理”解决方案的问题。

如果在尝试载入解决方案或虚拟机时遇到问题，请查看“应用程序和服务日志\Operations Manager”事件日志中是否存在事件 ID 为 4502、事件消息包含 Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent 的事件。 下表突出显示了特定的错误消息，以及每个消息的可能解决方案。  

| 消息 | 原因 | 解决方案 |   
|----------|----------|----------|  
| 无法注册进行修补程序管理的计算机，<br>注册失败，出现异常<br>System.InvalidOperationException: {"消息":"计算机已<br>注册到其他帐户。 "} | 计算机已载入到其他进行更新管理的工作区 | 通过[删除混合 runbook 组](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)对旧项目进行清理|  
| 无法注册进行修补程序管理的计算机，<br>注册失败，出现异常<br>System.Net.Http.HttpRequestException: 发送请求时出错。 ---><br>System.Net.WebException: 基础连接<br>已关闭: 在接收时<br>发生意外错误。 ---> System.ComponentModel.Win32Exception:<br>客户端和服务器无法通信，<br>因为没有通用算法 | 代理/网关/防火墙阻止通信 | [查看网络要求](automation-offering-get-started.md#network-planning)|  
| 无法注册进行修补程序管理的计算机，<br>注册失败，出现异常<br>Newtonsoft.Json.JsonReaderException: 分析正无穷大值时出错。 | 代理/网关/防火墙阻止通信 | [查看网络要求](automation-offering-get-started.md#network-planning)| 
| 由服务 <wsid>.oms.opinsights.azure.com 出示的证书<br>不是由<br>用于 Microsoft 服务的证书颁发机构颁发的。 联系人<br>网络管理员，确定其运行的代理是否截获<br>TLS/SSL 通信。 |代理/网关/防火墙阻止通信 | [查看网络要求](automation-offering-get-started.md#network-planning)|  
| 无法注册进行修补程序管理的计算机，<br>注册失败，出现异常<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>无法创建自签名证书。 ---><br>System.UnauthorizedAccessException: 访问被拒绝。 | 自签名证书生成失败 | 请验证系统帐户是否具有<br>以下文件夹的读取访问权限:<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

## <a name="next-steps"></a>后续步骤

继续学习本教程，了解如何管理 Windows VM 的更新。

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修补程序](automation-tutorial-troubleshoot-changes.md)

* 使用[Log Analytics](../log-analytics/log-analytics-log-searches.md)中的日志搜索可查看详细的更新数据。
* [创建警报](../log-analytics/log-analytics-alerts.md)检测到计算机缺少关键更新或计算机禁用了自动更新时发出警报。
