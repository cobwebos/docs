---
title: Azure 中的更新管理解决方案
description: 本文旨在帮助你了解如何使用 Azure 更新管理解决方案来管理 Windows 和 Linux 计算机的更新。
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 237f0d2b25230528c64bd47edd10ebae62750a0c
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345376"
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解决方案

可以使用 Azure 自动化中的更新管理解决方案，为部署在 Azure、本地环境或其他云提供程序中的 Windows 和 Linux 计算机管理操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。

可以直接通过 Azure 自动化帐户为虚拟机启用更新管理。 若要了解如何通过自动化帐户为虚拟机启用更新管理，请参阅[管理多个虚拟机的更新](manage-update-multi.md)。 还可以通过 Azure 门户中的虚拟机窗格为单个虚拟机启用更新管理。 此方案适用于 [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) 和 [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) 虚拟机。

## <a name="solution-overview"></a>解决方案概述

由更新管理托管的计算机使用以下配置执行评估和更新部署：

* 用于 Windows 或 Linux 的 Microsoft 监视代理 (MMA)
* 用于 Linux 的 PowerShell 所需状态配置 (DSC)
* 自动化混合 Runbook 辅助角色
* 适用于 Windows 计算机的 Microsoft 更新或 Windows Server 更新服务 (WSUS)

下图是针对行为和数据流的概念性视图，说明了解决方案如何评估安全更新并将其应用到工作区中所有连接的 Windows Server 和 Linux 计算机：

![更新管理流程](media/automation-update-management/update-mgmt-updateworkflow.png)

在计算机执行更新符合性扫描以后，代理会将信息批量转发到 Azure Log Analytics。 在 Windows 计算机上，符合性扫描默认情况下每 12 小时执行一次。

如果 MMA 重启，除了按扫描计划扫描，更新符合性扫描还会在更新安装前和更新安装后的 15 分钟内启动。

对于 Linux 计算机，符合性扫描默认情况下每 3 小时执行一次。 如果 MMA 代理重启，则会在 15 分钟内启动符合性扫描。

解决方案根据所配置的与之进行同步的源来报告计算机的更新情况。 如果将 Windows 计算机配置为向 WSUS 报告，则结果可能不同于 Microsoft 更新所显示的内容，具体取决于 WSUS 上次通过 Microsoft 更新进行同步的时间。 对于配置为向本地存储库（而非公共存储库）报告的 Linux 计算机来说，情况也是如此。

> [!NOTE]
> 若要正确地向服务进行报告，更新管理要求启用某些 URL 和端口。 若要了解有关这些要求的详细信息，请参阅[混合辅助角色的网络规划](automation-hybrid-runbook-worker.md#network-planning)。

可以通过创建计划的部署，在需要更新的计算机上部署和安装软件更新。 归类为“可选”的更新不包括在 Windows 计算机的部署范围内。 只有必需的更新会包括在部署范围内。 

计划的部署会通过显式指定计算机或通过根据特定的一组计算机的日志搜索结果来选择[计算机组](../log-analytics/log-analytics-computer-groups.md)，来定义哪些目标计算机接收适用更新。 也可以指定一个计划来批准并指定可以安装更新的一个时段。 

通过 Azure 自动化中的 runbook 安装更新。 你无法查看这些 runbook，它们不需要任何配置。 创建更新部署时，更新部署会创建一个计划，该计划在指定的时间为所包括的计算机启动主更新 Runbook。 此主 Runbook 会在每个代理上启动一个子 Runbook 来安装必需的更新。

目标计算机会按更新部署中指定的日期和时间，以并行方式执行部署。 在安装之前，会执行扫描来验证更新是否仍然是必需的。 对于 WSUS 客户端计算机，如果更新未在 WSUS 中获得批准，则更新部署会失败。

## <a name="clients"></a>客户端

### <a name="supported-client-types"></a>支持的客户端类型

下表显示了支持的操作系统列表：

|操作系统  |说明  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 仅支持更新评估。         |
|Windows Server 2008 R2 SP1 和更高版本     |需要 .NET Framework 4.5 或更高版本。 （[下载 .NET 框架](/dotnet/framework/install/guide-for-developers)）<br/> 需要 Windows PowerShell 4.0 或更高版本。 （[下载 WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)）<br/> 为提高可靠性，建议使用 Windows PowerShell 5.1。  （[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）        |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理必须具有访问更新存储库的权限。 基于分类的修补需要借助“yum”来返回 CentOS 当前没有的安全数据。         |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|Ubuntu 14.04 LTS 和 16.04 LTS (x86/x64)      |Linux 代理必须具有访问更新存储库的权限。         |

### <a name="unsupported-client-types"></a>不支持的客户端类型

下表列出了不受支持的操作系统：

|操作系统  |说明  |
|---------|---------|
|Windows 客户端     | 不支持客户端操作系统（例如 Windows 7 和 Windows 10）。        |
|Windows Server 2016 Nano Server     | 不支持。       |

### <a name="client-requirements"></a>客户端要求

#### <a name="windows"></a>Windows

Windows 代理必须配置为与 WSUS 服务器通信或必须有权访问 Microsoft 更新。 可以将更新管理与 System Center Configuration Manager 配合使用。 若要了解有关集成方案的详细信息，请参阅[将 System Center Configuration Manager 与更新管理集成](oms-solution-updatemgmt-sccmintegration.md#configuration)。 需要 [Windows 代理](../log-analytics/log-analytics-agent-windows.md)。 如果加入 Azure 虚拟机，则会自动安装此代理。

#### <a name="linux"></a>Linux

对于 Linux，计算机必须能够访问更新存储库。 更新存储库可以是专用的，也可以是公共的。 需要 TLS 1.1 或 TLS 1.2 才能与更新管理进行交互。 此解决方案不支持适用于 Linux 且配置为向多个 Log Analytics 工作区报告的 Operations Management Suite (OMS) 代理。

有关如何安装适用于 Linux 的 OMS 代理以及如何下载最新版本的信息，请参阅[适用于 Linux 的 Operations Management Suite 代理](https://github.com/microsoft/oms-agent-for-linux)。 有关如何安装适用于 Windows 的 OMS 代理的信息，请参阅[适用于 Windows 的 Operations Management Suite 代理](../log-analytics/log-analytics-windows-agent.md)。

## <a name="permissions"></a>权限

若要创建和管理更新部署，需要特定的权限。 若要了解这些权限，请参阅[基于角色的访问 - 更新管理](automation-role-based-access-control.md#update-management)。

## <a name="solution-components"></a>解决方案组件

该解决方案包括以下资源。 资源将添加到你的自动化帐户。 它们是直接连接的代理，在 Operations Manager 中，则是连接的管理组。

### <a name="hybrid-worker-groups"></a>混合辅助角色组

启用此解决方案以后，任何直接连接到 Log Analytics 工作区的 Windows 计算机都会自动配置为混合 Runbook 辅助角色，为此解决方案中包括的 Runbook 提供支持。

此解决方案管理的每台 Windows 计算机都会作为自动化帐户的一个“系统混合辅助角色组”列在“混合辅助角色组”窗格中。 解决方案使用命名约定 *Hostname FQDN_GUID*。 不能在帐户中通过 Runbook 将这些组作为目标进行操作。 如果尝试使用它们，它们将失败。 这些组仅用于为管理解决方案提供支持。

如果为解决方案和混合 Runbook 辅助角色组成员身份使用同一帐户，则可以将 Windows 计算机添加到自动化帐户中的混合 Runbook 辅助角色组来为自动化 Runbook 提供支持。 此功能是在 7.2.12024.0 版本的混合 Runbook 辅助角色中添加的。

### <a name="management-packs"></a>管理包

如果 System Center Operations Manager 管理组已连接到 Log Analytics 工作区，则会在 Operations Manager 中安装以下管理包。 在添加此解决方案后，还会在直接连接的 Windows 计算机上安装这些管理包。 你不需要对这些管理包进行配置或管理。

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

有关如何更新解决方案管理包的详细信息，请参阅[将 Operations Manager 连接到 Log Analytics](../log-analytics/log-analytics-om-agents.md)。

> [!NOTE]
> 对于使用 Operations Manger 代理的系统，若要能够由更新管理完全托管，需要将代理更新为 Microsoft Monitoring Agent。 若要了解如何更新代理，请参阅[如何升级 Operations Manager 代理](/system-center/scom/deploy-upgrade-agents.md)。

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>确认非 Azure 计算机已加入

若要确认直接连接的计算机是否正在与 Log Analytics 通信，可在数分钟后运行以下日志搜索之一。

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

在 Windows 计算机上，可以通过查看以下信息来验证代理与 Log Analytics 的连接：

1. 在控制面板中，打开 **Microsoft Monitoring Agent**。 在“Azure Log Analytics”选项卡上，代理会显示以下消息：“Microsoft Monitoring Agent 已成功连接到 Log Analytics”。
2. 打开“Windows 事件日志”。 转到“应用程序和服务日志\Operations Manager”，搜索来自“服务连接器”源的事件 ID 3000 和事件 ID 5002。 这些事件指示计算机已注册到 Log Analytics 工作区并且正在接收配置。

如果代理无法与 Log Analytics 通信且已配置为通过防火墙或代理服务器与 Internet 通信，请确认防火墙或代理服务器是否已正确配置。 若要了解如何验证防火墙或代理服务器是否已正确配置，请参阅 [Windows 代理的网络配置](../log-analytics/log-analytics-agent-windows.md)或 [Linux 代理的网络配置](../log-analytics/log-analytics-agent-linux.md)。

> [!NOTE]
> 如果 Linux 系统配置为与代理或 OMS 网关通信，并且你将载入此解决方案，请使用以下命令更新 *proxy.conf* 权限来向 omiuser 组授予对文件的读取权限：
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

执行评估后，新添加的 Linux 代理会显示状态“已更新”。 此过程可能需要长达 6 小时的时间。

若要确认 Operations Manager 管理组是否正在与 Log Analytics 通信，请参阅[验证 Operations Manager 与 Log Analytics 的集成](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics)。

## <a name="data-collection"></a>数据收集

### <a name="supported-agents"></a>支持的代理

下表介绍了该解决方案支持的连接的源：

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |该解决方案从 Windows 代理收集有关系统更新的信息，然后开始安装必需的更新。 |
| Linux 代理 |是 |该解决方案从 Linux 代理收集有关系统更新的信息，然后开始在受支持的发行版上安装必需的更新。 |
| Operations Manager 管理组 |是 |该解决方案从已连接的管理组中的代理收集有关系统更新的信息。<br/>并非必须具有从 Operations Manager 代理到 Log Analytics 的直接连接。 数据将从管理组转发到 Log Analytics 工作区。 |

### <a name="collection-frequency"></a>收集频率

对于每台托管的 Windows 计算机，每天执行两次扫描。 每隔 15 分钟就会调用一次 Windows API 来查询上次更新时间，以确定状态是否已更改。 如果状态已更改，则会启动符合性扫描。 

对于每台托管的 Linux 计算机，每 3 小时执行一次扫描。

可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

## <a name="viewing-update-assessments"></a>查看更新评估

在自动化帐户中，选择“更新管理”来查看计算机的状态。

此视图提供有关计算机、缺少的更新、更新部署和计划的更新部署的信息。 在“符合性”列中，可以看到计算机的最后评估时间。 在“更新代理准备”列中，可以看到更新代理的运行状况。 如果有问题，请选择相应链接来转到疑难解答文档，以帮助你了解可以采用什么步骤来更正问题。

若要运行日志搜索来返回有关计算机、更新或部署的信息，请在列表中选择相应的项。 此时将打开“日志搜索”窗格，其中显示了针对所选项的查询。

![更新管理默认视图](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>安装更新

对工作区中的所有 Linux 和 Windows 计算机进行更新评估后，可以通过创建“更新部署”安装必需的更新。 更新部署是为一台或多台计算机计划的必需更新安装。 应当指定部署日期和时间，以及要在部署范围中包括的计算机或计算机组。 若要详细了解计算机组，请参阅 [Log Analytics 中的计算机组](../log-analytics/log-analytics-computer-groups.md)。

 在更新部署中包括计算机组时，只会在创建计划时对组成员身份评估一次。 不会反映对组所做的后续更改。 要解决此问题，请删除计划的更新部署，并重新创建它。

> [!NOTE]
> 默认情况下，从 Azure 市场部署的 Windows 虚拟机设置为从 Windows 更新服务接收自动更新。 添加此解决方案或者将 Windows 虚拟机添加到工作区时，此行为不会更改。 如果不主动通过此解决方案管理更新，则会应用默认行为（即自动应用更新）。

若要避免在 Ubuntu 上的维护时段外应用更新，请重新配置无人参与升级包，禁用自动更新。 有关如何配置此包的信息，请参阅 [Ubuntu Server 指南中的自动更新主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

基于 Azure 市场中提供的按需 Red Hat Enterprise Linux (RHEL) 映像创建的虚拟机注册为访问 Azure 中部署的 [Red Hat 更新基础结构 (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)。 对于任何其他 Linux 发行版，必须按照其支持的方法从发行版联机文件存储库对其进行更新。

## <a name="view-missing-updates"></a>查看缺少的更新

选择“缺少的更新”可查看计算机中缺少的更新列表。 每个更新都会列出，并且可供选择。 将显示有关需要更新的计算机数和操作系统的信息，以及指向更多信息的链接。 “日志搜索”窗格显示有关更新的更多详细信息。

## <a name="view-update-deployments"></a>查看更新部署

选择“更新部署”选项卡可查看现有更新部署的列表。 选择表中的任一更新部署会打开该更新部署的“更新部署运行”窗格。

![更新部署结果的概述](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>创建或编辑更新部署

若要创建新的更新部署，请选择“计划更新部署”。 此时将打开“新建更新部署”窗格。 为下表中介绍的属性输入值：

| 属性 | 说明 |
| --- | --- |
|名称 |用于标识更新部署的唯一名称。 |
|操作系统| 选择 **Linux** 或 **Windows**。|
|要更新的计算机 |选择一个已保存的搜索，或者从下拉列表中选择“计算机”，然后选择个体计算机。 |
|更新分类|选择所需的所有更新分类。 CentOS 不能现成地支持此功能。|
|要排除的更新|输入要排除的更新。 对于 Windows，输入不带 **KB** 前缀的知识库文章。 对于 Linux，输入包名称或使用通配符。  |
|计划设置|选择启动时间，对于“定期”，然后“一次”或“重复”。|| 维护时段 |为更新设置的分钟数。 该值不能小于 30 分钟，也不能大于 6 小时。 |

## <a name="update-classifications"></a>更新分类

下表列出了更新管理中的更新分类，以及每个分类的定义。

### <a name="windows"></a>Windows

|分类  |说明  |
|---------|---------|
|关键更新     | 解决关键、非安全相关错误的特定问题的更新。        |
|安全更新     | 产品特定、安全相关问题的更新。        |
|更新汇总     | 一起打包以便于部署的一组累积修补程序。        |
|功能包     | 在产品版本以外发布的新产品功能。        |
|服务包     | 应用于应用程序的一组累积修补程序。        |
|定义更新     | 对病毒或其他定义文件的更新。        |
|工具     | 可帮助完成一个或多个任务的实用工具或功能。        |
|更新     | 对当前已安装的应用程序或文件的更新。        |

### <a name="linux"></a>Linux

|分类  |说明  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不是关键更新或不是安全更新的所有其他更新。        |

对于 Linux，由于云中的数据扩充，更新管理可以区分云中的关键更新和安全更新，同时显示评估数据。 为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他发行版不同，CentOS 没有现成的此信息。 如果你已将 CentOS 计算机配置为以某种方式返回以下命令的安全数据，则更新管理将能够基于分类进行修补。

```bash
sudo yum -q --security check-update
```

当前没有受支持的方法可用来在 CentOS 上提供原生分类数据。 目前，只能尽力为可能自己实现了此功能的客户提供支持。

## <a name="ports"></a>端口

更新管理特别需要以下地址。 与以下地址的通信通过端口 443 进行。

|Azure Public  |Azure Government   |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|* .blob.core.windows.net|*.blob.core.usgovcloudapi.net|

有关混合 Runbook 辅助角色所需端口的详细信息，请参阅[混合辅助角色端口](automation-hybrid-runbook-worker.md#hybrid-worker-role)。

建议在定义异常时使用列出的地址。 对于 IP 地址，可以下载 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此文件每周更新一次，反映当前已部署的范围和任何即将对 IP 范围进行的更改。

## <a name="search-logs"></a>搜索日志

除了 Azure 门户中提供的详细信息以外，还可以针对日志执行搜索。 在解决方案页上，选择“Log Analytics”。 此时将打开“日志搜索”窗格。

还可访问 [Log Analytics 搜索 API 文档](
https://dev.loganalytics.io/)，了解如何自定义查询或从不同客户端使用查询等。

### <a name="sample-queries"></a>示例查询

以下各部分提供了此解决方案收集的更新记录的示例日志查询：

#### <a name="single-azure-vm-assessment-queries-windows"></a>单个 Azure VM 评估查询 (Windows)

将 VMUUID 值替换为要查询的虚拟机的 VM GUID。 在 Log Analytics 中运行以下查询可找到应使用的 VMUUID：`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>缺少更新摘要

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>缺少更新列表

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>单个 Azure VM 评估查询 (Linux)

对于某些 Linux 发行版，Azure 资源管理器中与 Log Analytics 中的 VMUUID 值存在[字节序](https://en.wikipedia.org/wiki/Endianness)不匹配的情况。 以下查询可检查任一字节序的匹配情况。 使用 GUID 的 big-endian 和 little-endian 格式替换 VMUUID 值可正常地返回结果。 在 Log Analytics 中运行以下查询可找到应使用的 VMUUID：`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>缺少更新摘要

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>缺少更新列表

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>多 VM 评估查询

##### <a name="computers-summary"></a>计算机摘要

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>缺少更新摘要

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>计算机列表

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>缺少更新列表

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="integrate-with-system-center-configuration-manager"></a>集成 System Center Configuration Manager

已经投资购买了 System Center Configuration Manager 来管理电脑、服务器和移动设备的客户还依赖 Configuration Manager 的优势和成熟度来帮助他们管理软件更新。 Configuration Manager 是其软件更新管理 (SUM) 周期的一部分。

若要了解如何将管理解决方案与 System Center Configuration Manager 集成，请参阅[将 System Center Configuration Manager 与更新管理集成](oms-solution-updatemgmt-sccmintegration.md)。

## <a name="patch-linux-machines"></a>修补 Linux 计算机

下列部分说明了 Linux 修补的潜在问题。

### <a name="unexpected-os-level-upgrades"></a>意外的 OS 级别升级

在某些 Linux 版本（例如 Red Hat Enterprise Linux）中，可以通过包执行 OS 级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理使用相同的方法来更新管理员将在 Linux 计算机本地使用的包，因此，此行为是有意实施的。

若要避免通过更新管理运行来更新 OS 版本，可以使用“排除”功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为 redhat-release-server.x86_64。

![适用于 Linux 的要排除的包](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>不会应用关键 / 安全修补程序

将更新部署到 Linux 计算机时，可以选择更新分类。 这会筛选已应用到满足指定条件的计算机的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，更新管理中的某些更新可能标记为存在安全影响，但是本地计算机上不会显示该信息。 如果向 Linux 计算机应用关键更新，可能有些更新不会标记为对该计算机具有安全影响，因此不会应用这些更新。

但是，更新管理仍可能报告该计算机不合规，因为其中包含有关更新的其他信息。

按更新分类部署更新在 CentOS 上无法现成地运行。 对于 SUSE，如果首先需要与 zypper（包管理器）或其依赖项相关的安全更新，则仅选择“其他更新”作为分类可能会导致同时安装某些安全更新。 这是 zypper 的一项限制。 某些情况下，可能需要重新运行更新部署来验证检查更新日志。

## <a name="troubleshoot"></a>故障排除

要了解如何对更新管理进行故障排除，请参阅[更新管理故障排除](troubleshoot/update-management.md)

## <a name="next-steps"></a>后续步骤

继续学习教程来了解如何管理 Windows 虚拟机的更新。

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修补程序](automation-tutorial-update-management.md)

* 使用 [Log Analytics](../log-analytics/log-analytics-log-searches.md) 中的日志搜索来查看详细的更新数据。
* 当检测到计算机缺少关键更新或计算机禁用了自动更新时[创建警报](../log-analytics/log-analytics-alerts.md)。
