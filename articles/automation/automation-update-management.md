---
title: Azure Automation 中的更新管理
description: 本文介绍为 Windows 和 Linux 计算机管理更新的更新管理功能。
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: ca5d8c35aea06143e058aade473282a038212605
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872172"
---
# <a name="update-management-in-azure-automation"></a>Azure Automation 中的更新管理

可以使用 Azure 自动化中的更新管理来管理 Azure、本地环境和其他云环境中的 Windows 和 Linux 计算机的操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。

可以使用以下方法为虚拟机（Vm）启用更新管理：

* 用于一个或多个 Azure 虚拟机的[Azure 自动化帐户](automation-onboard-solutions-from-automation-account.md)。
* 为非 Azure 计算机手动。
* 对于 "Azure 门户中的" 虚拟机 "页上的单个 Azure VM。 此方案适用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) vm。
* 通过在 Azure 门户的 "虚拟机" 页中选择[多个 Azure vm](manage-update-multi.md) 。

> [!NOTE]
> 更新管理需要将 Log Analytics 工作区链接到自动化帐户。 有关受支持区域的权威列表，请参阅[Azure 工作区映射](how-to/region-mappings.md)。 区域映射不会影响从自动化帐户在单独的区域中管理 Vm 的功能。

[Azure 资源管理器模板](automation-update-management-deploy-template.md)可用于帮助你将更新管理部署到新的或现有的自动化帐户，并在你的订阅中 Log Analytics 工作区。

> [!NOTE]
> 不能使用配置了更新管理的计算机从 Azure Automation 运行自定义脚本。 此计算机只能运行 Microsoft 签名的更新脚本。 

## <a name="update-management-overview"></a>更新管理概述

由管理的计算机更新管理使用以下配置来执行评估和更新部署：

* 适用于 Windows 或 Linux 的 Log Analytics 代理
* 用于 Linux 的 PowerShell 所需状态配置 (DSC)
* 自动化混合 Runbook 辅助角色
* 适用于 Windows 计算机的 Microsoft 更新或 Windows Server Update Services （WSUS）

下图说明了更新管理如何评估和应用工作区中所有连接的 Windows Server 和 Linux 计算机的安全更新：

![更新管理工作流](./media/automation-update-management/update-mgmt-updateworkflow.png)

更新管理可用于在同一租户的多个订阅中本机登记计算机。

包发布后，将需要2到3小时才能显示适用于 Linux 计算机的修补程序进行评估。 对于 Windows 计算机，修补程序在发布后将需要12到15小时才能显示此修补程序。

在计算机完成更新符合性扫描后，代理会将信息批量转发到 Azure Monitor 日志。 在 Windows 计算机上，默认情况下，符合性扫描每12小时运行一次。

除了扫描计划以外，还会在重新启动 Log Analytics 代理之前的15分钟内启动更新符合性扫描，并在更新安装前和更新安装后启动。

对于 Linux 计算机，默认情况下，每小时执行一次符合性扫描。 如果重新启动 Log Analytics 代理，则会在15分钟内启动符合性扫描。

更新管理报告计算机的最新状态取决于你配置的同步源。 如果将 Windows 计算机配置为报告到 WSUS，这取决于 WSUS 上次与 Microsoft 更新同步的时间，结果可能与 Microsoft 更新显示的结果不同。 对于配置为向本地存储库（而不是向公共存储库）报告的 Linux 计算机，此行为是相同的。

> [!NOTE]
> 若要正确地向服务进行报告，更新管理要求启用某些 URL 和端口。 若要了解有关这些要求的详细信息，请参阅[网络配置](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning)。

通过创建计划的部署，你可以在需要更新的计算机上部署和安装软件更新。 归类为可选的更新不包括在 Windows 计算机的部署范围内。 只有必需的更新会包括在部署范围内。

计划的部署定义哪些目标计算机接收适用的更新。 它通过显式指定特定的计算机或基于特定计算机集的日志搜索的[计算机组](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups)（或基于指定条件动态选择 azure Vm 的[azure 查询](automation-update-management-query-logs.md)）来执行此操作。 这些组与[范围配置](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)不同，后者用于控制接收配置以启用更新管理的计算机的目标。 这会阻止其执行和报告更新符合性，并安装已批准的所需更新。

定义部署时，还可以指定要批准的计划，并设置一个时间段，在此期间可以安装更新。 此期间称为维护时段。 维护时段的20分钟跨度为重新启动保留，前提是需要一个，并选择适当的重新启动选项。 如果修补时间比预期时间长且维护时段少于20分钟，则不会进行重新启动。

通过 Azure 自动化中的 runbook 安装更新。 你无法查看这些 runbook，它们不需要任何配置。 当创建更新部署时，它将创建一个计划，该计划在指定的时间为所包含的计算机启动主更新 runbook。 此主 Runbook 会在每个代理上启动一个子 Runbook 来安装必需的更新。

在更新部署中指定的日期和时间，目标计算机并行执行部署。 在安装之前，会运行扫描来验证更新是否仍然是必需的。 对于 WSUS 客户端计算机，如果更新未在 WSUS 中获得批准，则更新部署会失败。
不支持在多个 Log Analytics 工作区（也称为多宿主）中将计算机注册到更新管理。

## <a name="clients"></a>客户端

### <a name="supported-client-types"></a>支持的客户端类型

下表列出了支持的操作系统进行更新评估。 修补需要混合 Runbook 辅助角色。 有关混合 Runbook 辅助角色要求的信息，请参阅[部署 Windows 混合 Runbook 辅助](automation-windows-hrw-install.md)角色和[部署 Linux 混合 runbook 辅助角色](automation-linux-hrw-install.md)。

> [!NOTE]
> 仅在 "自动化帐户" 和 "Log Analytics 工作区[映射" 表](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)中列出的特定区域支持对 Linux 计算机进行更新评估。 

|操作系统  |说明  |
|---------|---------|
|Windows Server 2019 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2016 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2012 R2 （Datacenter/Standard）<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 （RTM 和 SP1 Standard）| 更新管理仅支持对此操作系统进行评估。 不支持修补，因为 Windows Server 2008 R2 不支持[混合 Runbook 辅助角色](automation-windows-hrw-install.md)。 |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理需要访问更新存储库。 基于分类的修补需要`yum`返回 CentOS 在其 RTM 版本中没有的安全数据。 有关 CentOS 上基于分类的修补的详细信息，请参阅[Linux 上的更新分类](automation-view-update-assessments.md#linux-2)。          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理需要访问更新存储库。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理需要访问更新存储库。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理需要访问更新存储库。         |

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

Windows 代理必须配置为与 WSUS 服务器通信，或者需要访问 Microsoft 更新。 有关如何安装适用于 Windows 的 Log Analytics 代理的信息，请参阅[将 windows 计算机连接到 Azure Monitor](../log-analytics/log-analytics-windows-agent.md)。

你可以使用更新管理与 Microsoft 终结点 Configuration Manager。 若要了解有关集成方案的详细信息，请参阅将[Configuration Manager 与更新管理集成](updatemgmt-mecmintegration.md#configuration)。 Configuration Manager 环境中的站点管理的 Windows 服务器需要[windows Log Analytics 代理](../azure-monitor/platform/agent-windows.md)。 

默认情况下，从 Azure Marketplace 部署的 Windows Vm 设置为接收来自 Windows 更新服务的自动更新。 将 Windows Vm 添加到工作区时，此行为不会发生更改。 如果不使用更新管理主动管理更新，则会应用默认行为（即自动应用更新）。

> [!NOTE]
> 您可以修改组策略以便计算机重启只能由用户执行，而不能由系统执行。 如果更新管理无权重新启动计算机而无需手动交互用户，托管计算机可能会停滞。 有关详细信息，请参阅[配置自动更新组策略设置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

对于 Linux，计算机需要访问 "专用" 或 "公共" 的更新存储库。 需要 TLS 1.1 或 TLS 1.2 才能与更新管理进行交互。 更新管理不支持配置为向多个 Log Analytics 工作区报告的 Linux Log Analytics 代理。 计算机还必须安装 Python 2.x。

> [!NOTE]
> 仅在某些区域支持对 Linux 计算机进行更新评估。 请参阅自动化帐户和 Log Analytics 工作区[映射表](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)。 

有关如何安装适用于 Linux 的 Log Analytics 代理并下载最新版本的信息，请参阅[适用于 linux 的 Log Analytics 代理](../azure-monitor/platform/agent-linux.md)。 

通过 Azure Marketplace 中提供的按需 Red Hat Enterprise Linux （RHEL）映像创建的 Vm 将被注册，以访问 Azure 中部署的[Red Hat 更新基础结构（RHUI）](../virtual-machines/workloads/redhat/redhat-rhui.md) 。 必须使用分发支持的方法，从分发的联机文件存储库中更新任何其他 Linux 分发版。

## <a name="permissions"></a>权限

若要创建和管理更新部署，需要特定的权限。 若要了解有关这些权限的信息，请参阅[基于角色的访问–更新管理](automation-role-based-access-control.md#update-management-permissions)。

## <a name="update-management-components"></a>更新管理组件

更新管理使用本节中所述的资源。 启用更新管理时，这些资源会自动添加到自动化帐户。

### <a name="hybrid-runbook-worker-groups"></a>混合 Runbook 辅助角色组

启用更新管理后，任何直接连接到 Log Analytics 工作区的 Windows 计算机都会自动配置为混合 Runbook 辅助角色，以支持支持更新管理的 runbook。

更新管理管理的每台 Windows 计算机都作为自动化帐户的系统混合辅助角色组列在 "混合辅助角色组" 窗格中。 组使用`Hostname FQDN_GUID`命名约定。 不能在帐户中通过 Runbook 将这些组作为目标进行操作。 如果尝试，则尝试失败。 这些组旨在仅支持更新管理。

如果你将同一帐户用于更新管理和混合 Runbook 辅助角色组成员身份，则可以将 Windows 计算机添加到自动化帐户的混合 Runbook 辅助角色组，以支持自动化 runbook。 此功能是在 7.2.12024.0 版本的混合 Runbook 辅助角色中添加的。

### <a name="management-packs"></a>管理包

如果 Operations Manager 管理组已[连接到 Log Analytics 工作区](../azure-monitor/platform/om-agents.md)，则 Operations Manager 中安装了以下管理包。 还会为直接连接的 Windows 计算机上的更新管理安装这些管理包。 你不需要对这些管理包进行配置或管理。

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果已将 Operations Manager 1807 或2019管理组连接到管理组中配置为收集日志数据的 Log Analytics 工作区，则需要重写参数`IsAutoRegistrationEnabled` ，并在**AzureAutomation**中将其设置为 True。

有关管理包更新的详细信息，请参阅[将 Operations Manager 连接到 Azure Monitor 日志](../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 若要更新管理通过 Log Analytics 代理完全管理计算机，你必须更新为适用于 Windows 的 Log Analytics 代理或适用于 Linux 的 Log Analytics 代理。 若要了解如何更新代理，请参阅[如何升级 Operations Manager 代理](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)。 在使用 Operations Manager 的环境中，你必须运行 System Center Operations Manager 2012 R2 UR 14 或更高版本。

## <a name="data-collection"></a>数据收集

### <a name="supported-sources"></a>支持的源

下表描述了更新管理支持的连接的源：

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |更新管理从 Windows 代理收集有关系统更新的信息，然后启动所需更新的安装。 |
| Linux 代理 |是 |更新管理从 Linux 代理收集有关系统更新的信息，然后开始在支持的分发上安装所需的更新。 |
| Operations Manager 管理组 |是 |“更新管理”从已连接的管理组中的代理收集有关系统更新的信息。<br/><br/>不需要从 Operations Manager 代理到 Azure Monitor 日志的直接连接。 数据将从管理组转发到 Log Analytics 工作区。 |

### <a name="collection-frequency"></a>收集频率

更新管理使用以下规则扫描托管计算机的数据。 仪表板可能需要30分钟到6小时才能显示托管计算机的更新数据。

* 每台 Windows 计算机更新管理每台计算机一次扫描两次。 每隔15分钟，它会在 Windows API 中查询上次更新时间，以确定状态是否已更改。 如果状态已更改，更新管理会开始符合性扫描。

* 每个 Linux 计算机-更新管理每小时执行一次扫描。

使用更新管理的计算机 Azure Monitor 日志的平均数据使用量大约为每月 25 MB。 此值只是近似值，根据环境的不同而有所变化。 建议你监视环境，以跟踪你的实际使用情况。 有关分析数据使用情况的详细信息，请参阅[管理使用情况和成本](../azure-monitor/platform/manage-cost-storage.md)。

## <a name="network-planning"></a><a name="ports"></a>网络规划

更新管理特别需要以下地址。 与以下地址的通信通过端口 443 进行。

|Azure Public  |Azure Government   |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|* .azure-automation.net | *.azure-automation.us|

对于 Windows 计算机，还必须允许流量发送到 Windows 更新所需的任何终结点。 可以在[与 HTTP/Proxy 相关的问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中找到所需终结点的更新列表。 如果你有本地[Windows 更新服务器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，则还必须允许流量流向[WSUS 密钥](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中指定的服务器。

对于 Red Hat Linux 计算机，请参阅[RHUI 内容传送服务器的 ip](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers)以获取所需的终结点。 对于其他 Linux 发行版，请参阅提供程序文档。

有关混合 Runbook 辅助角色所需的端口的详细信息，请参阅[用于混合 Runbook 辅助角色的更新管理地址](automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)。

建议在定义异常时使用列出的地址。 对于 IP 地址，你可以下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此文件每周更新一次，它会反映当前部署的范围和任何即将对 IP 范围进行的更改。

按照[连接无 internet 访问的计算机](../azure-monitor/platform/gateway.md)中的说明配置无法访问 internet 的计算机。

## <a name="update-classifications"></a>更新分类

下表定义了更新管理支持的 Windows 更新分类。 

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

下表定义了适用于 Linux 更新的受支持分类。

|分类  |说明  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不重要或不是安全更新的所有其他更新。        |

对于 Linux，更新管理可以区分云中的关键更新和安全更新，同时显示评估数据，因为云中的数据扩充。 为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他分发不同，CentOS 没有 RTM 版本中提供的此信息。 如果已将 CentOS 计算机配置为返回以下命令的安全数据，则更新管理可以基于分类进行修补。

```bash
sudo yum -q --security check-update
```

目前，没有支持的方法可在 CentOS 上启用本机分类数据可用性。 目前，仅向可能已启用此功能的客户提供最大程度的支持。 

若要在 Red Hat Enterprise 版本6上对更新进行分类，需要安装 yum 插件。 在 Red Hat Enterprise Linux 7 上，该插件已经是 yum 本身的一部分，无需安装任何内容。 有关详细信息，请参阅以下 Red Hat[知识库文章](https://access.redhat.com/solutions/10021)。

## <a name="integrate-update-management-with-configuration-manager"></a>将更新管理与 Configuration Manager 集成

已投资于管理 Pc、服务器和移动设备的 Microsoft 端点 Configuration Manager 的客户也依赖于 Configuration Manager 的强度和成熟度来帮助管理软件更新。 若要了解如何将更新管理与 Configuration Manager 集成，请参阅将[Configuration Manager 与更新管理集成](updatemgmt-mecmintegration.md)。

## <a name="third-party-updates-on-windows"></a>Windows 上的第三方更新

更新管理依赖于本地配置的更新存储库来更新受支持的 Windows 系统，即 WSUS 或 Windows 更新。 [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher)的工具允许你通过 WSUS 导入和发布自定义更新。 此方案允许更新管理将使用 Configuration Manager 的计算机更新为使用第三方软件的更新存储库。 若要了解如何配置 Updates Publisher，请参阅[安装 Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)。

## <a name="enable-update-management"></a>启用更新管理

Azure[资源管理器模板](automation-update-management-deploy-template.md)可用于帮助你将更新管理部署到新的或现有的自动化帐户，并在你的订阅中 Azure Monitor Log Analytics 工作区。 它不会配置应管理的计算机的作用域，这是在使用模板后的单独步骤中执行的。

可以通过以下方式启用更新管理并选择要管理的计算机：

* [从虚拟机中](automation-onboard-solutions-from-vm.md)。
* [浏览多台计算机](automation-onboard-solutions-from-browse.md)。
* [通过 Azure 自动化帐户](automation-onboard-solutions.md)。

## <a name="next-steps"></a>后续步骤

查看 Azure 自动化[常见问题解答](automation-faq.md)，查看有关更新管理的常见问题。
