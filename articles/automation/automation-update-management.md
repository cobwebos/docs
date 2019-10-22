---
title: Azure 中的更新管理解决方案
description: 本文介绍如何使用 Azure 更新管理解决方案来管理 Windows 和 Linux 计算机的更新。
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 66acb1284f0814eec91715284259272a065dbae2
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690889"
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解决方案

可以使用 Azure 自动化中的更新管理解决方案来管理 Azure、本地环境和其他云提供商中的 Windows 和 Linux 计算机的操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。

可以直接从 Azure 自动化帐户为虚拟机（Vm）启用更新管理。 若要了解如何操作，请参阅[管理多个虚拟机的更新](manage-update-multi.md)。 还可以从 Azure 门户中的 "虚拟机" 页为 VM 启用更新管理。 此方案适用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) vm。

> [!NOTE]
> 更新管理解决方案需要将 Log Analytics 工作区链接到自动化帐户。 有关受支持区域的权威列表，请参阅[Azure 工作区映射](./how-to/region-mappings.md)。 区域映射不会影响从自动化帐户在单独的区域中管理 Vm 的功能。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>解决方案概述

由管理的计算机更新管理使用以下配置来执行评估和更新部署：

* 用于 Windows 或 Linux 的 Microsoft 监视代理 (MMA)
* 用于 Linux 的 PowerShell 所需状态配置 (DSC)
* 自动化混合 Runbook 辅助角色
* 适用于 Windows 计算机的 Microsoft 更新或 Windows Server 更新服务 (WSUS)

下图说明了此解决方案如何评估和应用工作区中所有连接的 Windows Server 和 Linux 计算机的安全更新：

![更新管理流程](./media/automation-update-management/update-mgmt-updateworkflow.png)

更新管理可用于在同一租户的多个订阅中本机登记计算机。

包发布后，将需要2到3小时才能显示适用于 Linux 计算机的修补程序进行评估。 对于 Windows 计算机，修补程序在发布后将需要12到15小时才能显示此修补程序。

计算机完成更新符合性扫描后，代理会将信息批量转发到 Azure Monitor 日志。 在 Windows 计算机上，符合性扫描默认情况下每 12 小时运行一次。

如果 MMA 重启，除了按扫描计划扫描，更新符合性扫描还会在更新安装前和更新安装后的 15 分钟内启动。

对于 Linux 计算机，默认情况下，每小时执行一次符合性扫描。 如果 MMA 代理重启，则会在 15 分钟内启动符合性扫描。

解决方案根据所配置的与之进行同步的源来报告计算机的更新情况。 如果将 Windows 计算机配置为报告到 WSUS，这取决于 WSUS 上次与 Microsoft 更新同步的时间，结果可能与 Microsoft 更新显示的结果不同。 对于配置为向本地存储库（而非公共存储库）报告的 Linux 计算机来说，行为也是如此。

> [!NOTE]
> 若要正确地向服务进行报告，更新管理要求启用某些 URL 和端口。 若要了解有关这些要求的详细信息，请参阅[混合辅助角色的网络规划](automation-hybrid-runbook-worker.md#network-planning)。

可以通过创建计划的部署，在需要更新的计算机上部署和安装软件更新。 归类为“可选”的更新不包括在 Windows 计算机的部署范围内。 只有必需的更新会包括在部署范围内。

计划的部署定义哪些目标计算机接收适用的更新。 它通过显式指定特定的计算机或选择基于特定计算机集的日志搜索的计算机组来执行此操作，或选择基于指定条件的[azure 查询](automation-update-management-query-logs.md)上的[计算机组](../azure-monitor/platform/computer-groups.md)。 这些组与[范围配置](../azure-monitor/insights/solution-targeting.md)不同，后者仅用于确定哪些计算机获得启用该解决方案的管理包。

你还可以指定要批准的计划，并设置一个时间段，在此期间可以安装更新。 此期间称为维护时段。 维护时段的20分钟跨度为重新启动保留，前提是需要一个，并选择适当的重新启动选项。 如果修补时间比预期时间长且维护时段少于20分钟，则不会进行重新启动。

通过 Azure 自动化中的 runbook 安装更新。 你无法查看这些 runbook，它们不需要任何配置。 当创建更新部署时，它将创建一个计划，该计划在指定的时间为所包含的计算机启动主更新 runbook。 此主 Runbook 会在每个代理上启动一个子 Runbook 来安装必需的更新。

目标计算机会按更新部署中指定的日期和时间，以并行方式执行部署。 在安装之前，会运行扫描来验证更新是否仍然是必需的。 对于 WSUS 客户端计算机，如果更新未在 WSUS 中获得批准，则更新部署会失败。

不支持在多个 Log Analytics 工作区（多宿主）中将计算机注册到更新管理。

## <a name="clients"></a>客户端

### <a name="supported-client-types"></a>支持的客户端类型

下表列出了支持的操作系统进行更新评估。 修补需要混合 Runbook 辅助角色。 有关混合 Runbook 辅助角色要求的信息，请参阅安装[Windows 混合 Runbook 辅助角色](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)和[Linux 混合 runbook 辅助角色](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)的安装指南。

|操作系统  |说明  |
|---------|---------|
|Windows Server 2019 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2016 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2012 R2 （Datacenter/Standard）<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 （RTM 和 SP1 Standard）||
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理必须具有访问更新存储库的权限。 基于分类的修补需要 `yum` 返回 CentOS 在其 RTM 版本中没有的安全数据。 有关 CentOS 上基于分类的修补的详细信息，请参阅[Linux 上的更新分类](#linux-2)。          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理必须具有访问更新存储库的权限。         |

> [!NOTE]
> 可以通过更新管理来管理 Azure 虚拟机规模集。 更新管理适用于实例本身，而不是基本映像。 你需要以增量方式计划更新，以便不会同时更新所有 VM 实例。
> 可以按照在[非 Azure 计算机](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)上进行操作中的步骤为虚拟机规模集添加节点。

### <a name="unsupported-client-types"></a>不支持的客户端类型

下表列出了不受支持的操作系统：

|操作系统  |说明  |
|---------|---------|
|Windows 客户端     | 不支持客户端操作系统（例如 Windows 7 和 Windows 10）。        |
|Windows Server 2016 Nano Server     | 不支持。       |
|Azure Kubernetes 服务节点 | 不支持。 使用[Azure Kubernetes Service （AKS）中的将安全和内核更新应用到 Linux 节点](../aks/node-updates-kured.md)中所述的修补过程|

### <a name="client-requirements"></a>客户端要求

以下信息描述了特定于操作系统的客户端要求。 有关其他指南，请参阅[网络规划](#ports)。

#### <a name="windows"></a>Windows

必须配置 Windows 代理以与 WSUS 服务器进行通信，或者必须有权访问 Microsoft 更新。

可以将更新管理与 System Center Configuration Manager 配合使用。 若要了解有关集成方案的详细信息，请参阅[将 System Center Configuration Manager 与更新管理集成](oms-solution-updatemgmt-sccmintegration.md#configuration)。 需要 [Windows 代理](../azure-monitor/platform/agent-windows.md)。 如果要载入 Azure VM，则会自动安装代理。

默认情况下，从 Azure Marketplace 部署的 Windows Vm 设置为接收来自 Windows 更新服务的自动更新。 添加此解决方案或将 Windows Vm 添加到工作区时，此行为不会发生更改。 如果不主动通过此解决方案管理更新，则会应用默认行为（即自动应用更新）。

> [!NOTE]
> 用户可以修改组策略以便计算机重启只能由用户执行，而不能由系统执行。 如果更新管理无权重新启动计算机而无需手动交互用户，托管计算机可能会停滞。
>
> 有关详细信息，请参阅[配置自动更新组策略设置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

对于 Linux，计算机必须能够访问更新存储库。 更新存储库可以是专用的，也可以是公共的。 需要 TLS 1.1 或 TLS 1.2 才能与更新管理进行交互。 此解决方案不支持配置为向多个 Log Analytics 工作区报告的 Log Analytics 代理。 计算机还必须安装 Python 2.x。

有关如何安装适用于 Linux 的 Log Analytics 代理并下载最新版本的信息，请参阅[适用于 linux 的 Log Analytics 代理](https://github.com/microsoft/oms-agent-for-linux)。 有关如何安装适用于 Windows 的 Log Analytics 代理的信息，请参阅[将 windows 计算机连接到 Azure Monitor](../log-analytics/log-analytics-windows-agent.md)。

通过 Azure Marketplace 中提供的按需 Red Hat Enterprise Linux （RHEL）映像创建的 Vm 将被注册，以访问 Azure 中部署的[Red Hat 更新基础结构（RHUI）](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) 。 必须使用分发的受支持方法，从分发的联机文件存储库中更新任何其他 Linux 分发版。

## <a name="permissions"></a>权限

若要创建和管理更新部署，需要特定的权限。 若要了解有关这些权限的信息，请参阅[基于角色的访问–更新管理](automation-role-based-access-control.md#update-management)。

## <a name="solution-components"></a>解决方案组件

该解决方案包括以下资源。 资源将添加到你的自动化帐户。 它们是直接连接的代理，在 Operations Manager 中，则是连接的管理组。

### <a name="hybrid-worker-groups"></a>混合辅助角色组

启用此解决方案以后，任何直接连接到 Log Analytics 工作区的 Windows 计算机都会自动配置为混合 Runbook 辅助角色，为此解决方案中包括的 Runbook 提供支持。

此解决方案管理的每台 Windows 计算机都会作为自动化帐户的一个“系统混合辅助角色组”列在“混合辅助角色组”窗格中。 这些解决方案使用*HOSTNAME FQDN_GUID*命名约定。 不能在帐户中通过 Runbook 将这些组作为目标进行操作。 如果尝试，则尝试失败。 这些组仅用于为管理解决方案提供支持。

如果为解决方案和混合 Runbook 辅助角色组成员身份使用同一帐户，则可以将 Windows 计算机添加到自动化帐户中的混合 Runbook 辅助角色组来为自动化 Runbook 提供支持。 此功能是在 7.2.12024.0 版本的混合 Runbook 辅助角色中添加的。

### <a name="management-packs"></a>管理包

如果 System Center Operations Manager 管理组已连接到 Log Analytics 工作区，则会在 Operations Manager 中安装以下管理包。 在添加此解决方案后，还会在直接连接的 Windows 计算机上安装这些管理包。 你不需要对这些管理包进行配置或管理。

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 假设你有一个 Operations Manager 1807 或2019管理组，并且在管理组级别配置了代理，以便将它们与工作区关联。 要使其显示出来，当前的解决方法是将**microsoft.intelligencepacks.updateassessment**中的**IsAutoRegistrationEnabled**重写为**True** 。

有关如何更新解决方案管理包的详细信息，请参阅[将 Operations Manager 连接到 Azure Monitor 日志](../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 对于具有操作管理器代理的系统：对于要通过更新管理完全管理的代理，必须将代理更新为 MMA。 若要了解如何更新代理，请参阅[如何升级 Operations Manager 代理](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)。 在使用 Operations Manager 的环境中，你必须运行 System Center Operations Manager 2012 R2 UR 14 或更高版本。

## <a name="data-collection"></a>数据收集

### <a name="supported-agents"></a>支持的代理

下表介绍了此解决方案支持的连接的源：

| 连接的源 | 受支持 | 描述 |
| --- | --- | --- |
| Windows 代理 |是 |该解决方案从 Windows 代理收集有关系统更新的信息，然后开始安装必需的更新。 |
| Linux 代理 |是 |该解决方案从 Linux 代理收集有关系统更新的信息，然后开始在受支持的发行版上安装必需的更新。 |
| Operations Manager 管理组 |是 |该解决方案从已连接的管理组中的代理收集有关系统更新的信息。<br/><br/>不需要从 Operations Manager 代理到 Azure Monitor 日志的直接连接。 数据将从管理组转发到 Log Analytics 工作区。 |

### <a name="collection-frequency"></a>收集频率

对于每台托管的 Windows 计算机，每天执行两次扫描。 每隔 15 分钟就会调用一次 Windows API 来查询上次更新时间，以确定状态是否已更改。 如果状态已更改，则会启动符合性扫描。

每个托管 Linux 计算机每小时执行一次扫描。

可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

使用更新管理的计算机 Azure Monitor 日志的平均数据使用量大约为每月25兆字节（MB）。 此值只是近似值，根据环境的不同而有所变化。 建议你监视环境，以跟踪你的实际使用情况。

## <a name="ports"></a>网络规划

更新管理特别需要以下地址。 与以下地址的通信通过端口 443 进行。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

对于 Windows 计算机，还必须允许流量发送到 Windows 更新所需的任何终结点。 可以在[与 HTTP/Proxy 相关的问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中找到所需终结点的更新列表。 如果你有本地[Windows 更新服务器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，则还必须允许流量流向[WSUS 密钥](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中指定的服务器。

对于 Red Hat Linux 计算机，请参阅[RHUI 内容传送服务器的 ip](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers)以获取所需的终结点。 对于其他 Linux 发行版，请参阅提供程序文档。

有关混合 Runbook 辅助角色所需端口的详细信息，请参阅[混合辅助角色端口](automation-hybrid-runbook-worker.md#hybrid-worker-role)。

建议在定义异常时使用列出的地址。 对于 IP 地址，你可以下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此文件每周更新一次，它会反映当前部署的范围和任何即将对 IP 范围进行的更改。

按照[连接无 internet 访问的计算机](../azure-monitor/platform/gateway.md)中的说明配置无法访问 internet 的计算机。

## <a name="view-update-assessments"></a>查看更新评估

在自动化帐户中，选择“更新管理”来查看计算机的状态。

此视图提供有关计算机、缺少的更新、更新部署和计划的更新部署的信息。 在 "**符合性**" 列中，可以看到上次评估计算机的时间。 在 "**更新代理准备情况**" 列中，可以检查更新代理的运行状况。 如果出现问题，请选择链接以中转到疑难解答文档，该文档可帮助你解决问题。

若要运行返回有关计算机、更新或部署的信息的日志搜索，请在列表中选择相应的项。 此时将打开“日志搜索”窗格，其中显示了针对所选项的查询。

![更新管理默认视图](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>查看缺少的更新

选择“缺少的更新”可查看计算机中缺少的更新列表。 每个更新都会列出，并且可供选择。 将显示有关需要更新的计算机数和操作系统的信息，以及指向更多信息的链接。 “日志搜索”窗格显示有关更新的更多详细信息。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分类

下表列出了更新管理中的更新分类，以及每个分类的定义。

### <a name="windows"></a>Windows

|分类  |描述  |
|---------|---------|
|关键更新     | 解决关键、非安全相关错误的特定问题的更新。        |
|安全更新     | 产品特定、安全相关问题的更新。        |
|更新汇总     | 一起打包以便于部署的一组累积修补程序。        |
|功能包     | 在产品版本以外发布的新产品功能。        |
|服务包     | 应用于应用程序的一组累积修补程序。        |
|定义更新     | 对病毒或其他定义文件的更新。        |
|工具     | 可帮助完成一个或多个任务的实用工具或功能。        |
|更新     | 对当前已安装的应用程序或文件的更新。        |

### <a name="linux-2"></a>Linux

|分类  |描述  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不重要或不是安全更新的所有其他更新。        |

对于 Linux，更新管理可以区分云中的关键更新和安全更新，同时显示评估数据，因为云中的数据扩充。 为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他分发不同，CentOS 没有 RTM 版本中提供的此信息。 如果已将 CentOS 计算机配置为返回以下命令的安全数据，则更新管理可以基于分类进行修补。

```bash
sudo yum -q --security check-update
```

目前，没有支持的方法可在 CentOS 上启用本机分类数据可用性。 目前，仅向可能已自行启用此功能的客户提供最大程度的支持。

## <a name="integrate-with-system-center-configuration-manager"></a>集成 System Center Configuration Manager

已经投资购买了 System Center Configuration Manager 来管理电脑、服务器和移动设备的客户还依赖 Configuration Manager 的优势和成熟度来帮助他们管理软件更新。 Configuration Manager 是其软件更新管理 (SUM) 周期的一部分。

若要了解如何将管理解决方案与 System Center Configuration Manager 集成，请参阅[将 System Center Configuration Manager 与更新管理集成](oms-solution-updatemgmt-sccmintegration.md)。

### <a name="third-party-patches-on-windows"></a>Windows 上的第三方修补程序

更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 这是 WSUS 或 Windows 更新。 可以使用 [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) 之类的工具将自定义更新发布到 WSUS 中。 此方案允许更新管理将使用 System Center Configuration Manager 作为其更新存储库的计算机与第三方软件配合使用。 若要了解如何配置 Updates Publisher，请参阅[安装 Updates Publisher](/sccm/sum/tools/install-updates-publisher)。

## <a name="patch-linux-machines"></a>修补 Linux 计算机

下列部分说明了 Linux 修补的潜在问题。

### <a name="unexpected-os-level-upgrades"></a>意外的 OS 级别升级

在某些 Linux 变体（如 Red Hat Enterprise Linux）上，可能会通过包进行操作系统级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理使用相同的方法来更新管理员将在 Linux 计算机本地使用的包，因此，此行为是有意实施的。

若要避免更新管理运行的情况下更新 OS 版本，请使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为 redhat-release-server.x86_64。

![适用于 Linux 的要排除的包](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>不应用严重/安全修补程序

将更新部署到 Linux 计算机时，可以选择更新分类。 此选项筛选应用到满足指定条件的计算机的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此某些更新可能会在更新管理中标记为具有安全影响，即使本地计算机没有该信息也是如此。 因此，如果将关键更新应用于 Linux 计算机，则可能存在未标记为对该计算机产生安全影响的更新，因此不会应用更新。 但是，更新管理可能仍会将该计算机报告为不合规，因为它包含有关相关更新的其他信息。

通过更新分类部署更新不适用于 CentOS 的 RTM 版本。 若要正确部署 CentOS 的更新，请选择 "所有分类" 以确保应用更新。 对于 SUSE，*仅*选择**其他更新**作为分类可能会导致某些安全更新（如果首先需要与 zypper （包管理器）或其依赖项相关的安全更新）。 此行为是 zypper 的一项限制。 在某些情况下，可能需要重新运行更新部署。 若要验证，请检查更新日志。

### <a name="multi-tenant"></a>跨租户更新部署

如果在另一个 Azure 租户报表中有要更新管理需要修补的计算机，则必须使用以下解决方法来计划这些计算机。 你可以将[或者使用 set-azurermautomationschedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 与 `-ForUpdate` 开关一起使用来创建一个计划，并使用[AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet 并将另一个租户中的计算机传递给 `-NonAzureComputer` 参数。 以下示例介绍如何执行此操作：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>启用更新管理

要开始修补系统，需要启用更新管理解决方案。 可使用多种方法将计算机加入到更新管理。 以下是推荐和支持的方法，可用于加入此解决方案：

* [通过虚拟机](automation-onboard-solutions-from-vm.md)
* [通过浏览多个计算机](automation-onboard-solutions-from-browse.md)
* [通过自动化帐户](automation-onboard-solutions-from-automation-account.md)
* [使用 Azure 自动化 runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>后续步骤

使用以下教程来了解如何管理 Windows Vm 的更新：

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修补程序](automation-tutorial-update-management.md)

* 使用[Azure Monitor 日志](../log-analytics/log-analytics-log-searches.md)中的日志搜索查看详细的更新数据。
* 针对更新部署状态[创建警报](automation-tutorial-update-management.md#configure-alerts)。

* 若要了解如何通过 REST API 与更新管理进行交互，请参阅[软件更新配置](/rest/api/automation/softwareupdateconfigurations)。
* 若要了解如何对更新管理进行故障排除，请参阅[更新管理故障排除](troubleshoot/update-management.md)。
