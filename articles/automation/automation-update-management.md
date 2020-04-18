---
title: Azure 中的更新管理解决方案
description: 本文介绍如何使用 Azure 更新管理解决方案来管理 Windows 和 Linux 计算机的更新。
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617408"
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解决方案

可以使用 Azure 自动化中的**更新管理解决方案**管理 Azure 中的 Windows 和 Linux 计算机的操作系统更新、本地环境和其他云环境中的操作系统更新。 您可以快速评估所有代理计算机上可用更新的状态，并管理安装服务器所需更新的过程。

您可以使用以下方法为虚拟机 （VM） 启用更新管理：

- 从[Azure 自动化帐户](automation-onboard-solutions-from-automation-account.md)中考虑一个或多个 Azure 计算机，并手动为非 Azure 计算机。

- 对于 Azure 门户中的虚拟机页中的单个 Azure VM。 此方案适用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM。

- 通过从 Azure 门户中的虚拟机页面选择多个[Azure VM。](manage-update-multi.md) 

> [!NOTE]
> 更新**管理解决方案**需要将日志分析工作区链接到您的自动化帐户。 有关受支持区域的最终列表，请参阅[Azure 工作区映射](./how-to/region-mappings.md)。 区域映射不会影响在独立于自动化帐户的单独区域中管理 VM 的能力。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure[资源管理器模板](automation-update-management-deploy-template.md)可帮助您将**更新管理解决方案**部署到订阅中的新或现有自动化帐户和日志分析工作区。

## <a name="solution-overview"></a>解决方案概述

由更新管理管理的计算机使用以下配置执行评估和更新部署：

* 适用于 Windows 或 Linux 的日志分析代理
* 用于 Linux 的 PowerShell 所需状态配置 (DSC)
* 自动化混合 Runbook 辅助角色
* 适用于 Windows 计算机的微软更新或 Windows 服务器更新服务 （WSUS）

下图说明了解决方案如何评估和应用安全更新到工作区中的所有连接的 Windows 服务器和 Linux 计算机：

![更新管理流程](./media/automation-update-management/update-mgmt-updateworkflow.png)

更新管理可用于在同一租户的多个订阅中本机登记计算机。

包发布后，补丁需要 2 到 3 小时才能显示给 Linux 计算机进行评估。 对于 Windows 计算机，修补程序在发布后需要 12 到 15 小时才能显示进行评估。

计算机完成更新合规性扫描后，代理将信息批量转发到 Azure 监视器日志。 默认情况下，在 Windows 计算机上，合规性扫描每 12 小时运行一次。

除了扫描计划外，更新合规性扫描将在重新启动日志分析代理、更新安装之前和更新安装后的 15 分钟内启动。

默认情况下，对于 Linux 计算机，每小时执行一次合规性扫描。 如果重新启动日志分析代理，将在 15 分钟内启动合规性扫描。

该解决方案报告计算机的最新基于您配置为同步的源。 如果 Windows 计算机配置为向 WSUS 报告，具体取决于 WSUS 上次与 Microsoft 更新同步的次，结果可能与 Microsoft 更新显示的结果不同。 对于配置为向本地回购而不是公共回购报告的 Linux 计算机，此行为是相同的。

> [!NOTE]
> 若要正确地向服务进行报告，更新管理要求启用某些 URL 和端口。 若要了解有关这些要求的详细信息，请参阅[混合辅助角色的网络规划](automation-hybrid-runbook-worker.md#network-planning)。

您可以通过创建计划部署在需要更新的计算机上部署和安装软件更新。 分类为 *"可选"* 的更新不包括在 Windows 计算机的部署范围内。 只有必需的更新会包括在部署范围内。

计划部署定义哪些目标计算机接收适用的更新。 为此，它要么显式指定某些计算机，要么选择基于一组特定计算机的日志搜索[的计算机组](../azure-monitor/platform/computer-groups.md)（或者基于基于指定条件动态选择 Azure VM 的[Azure 查询](automation-update-management-query-logs.md)）。 这些组与[范围配置](../azure-monitor/insights/solution-targeting.md)不同，范围配置仅用于确定哪些计算机获取启用解决方案的管理包。

您还可以指定计划来批准和设置可以安装更新的时间段。 此期间称为维护窗口。 如果需要一个维护窗口，并且选择了适当的重新启动选项，则保留 20 分钟的维护窗口范围用于重新启动。 如果修补时间比预期长，并且维护窗口中的时间少于 20 分钟，则不会发生重新启动。

通过 Azure 自动化中的 runbook 安装更新。 您无法查看这些 Runbook，并且它们不需要任何配置。 创建更新部署时，它会创建一个计划，在指定时间为包含的计算机启动主更新 Runbook。 此主 Runbook 会在每个代理上启动一个子 Runbook 来安装必需的更新。

在更新部署中指定的日期和时间，目标计算机并行执行部署。 在安装之前，会运行扫描来验证更新是否仍然是必需的。 对于 WSUS 客户端计算机，如果更新在 WSUS 中未获得批准，则更新部署将失败。

不支持在多个日志分析工作区（也称为多宿主）中注册计算机以进行更新管理。

## <a name="clients"></a>客户端

### <a name="supported-client-types"></a>支持的客户端类型

下表列出了用于更新评估的支持操作系统。 修补需要混合 Runbook 辅助角色。 有关混合 Runbook 辅助角色要求的信息，请参阅安装 Windows[混合 Runbook 辅助角色](automation-windows-hrw-install.md)和[Linux 混合 Runbook 辅助项](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)的安装指南。

|操作系统  |说明  |
|---------|---------|
|Windows 服务器 2019 （数据中心/数据中心核心/标准）<br><br>Windows 服务器 2016 （数据中心/数据中心核心/标准）<br><br>Windows 服务器 2012 R2 （数据中心/标准）<br><br>Windows Server 2012 || 
|Windows 服务器 2008 R2 （RTM 和 SP1 标准）| 更新管理仅支持对此操作系统执行评估，不支持修补，因为 Windows Server 2008 R2 不支持[混合 Runbook 辅助角色](automation-windows-hrw-install.md)。 |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理需要访问更新存储库。 基于分类的修补需要`yum`返回 CentOS 在其 RTM 版本中没有的安全数据。 有关 CentOS 上基于分类的修补的详细信息，请参阅[在 Linux 上更新分类](automation-view-update-assessments.md#linux-2)。          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理需要访问更新存储库。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理需要访问更新存储库。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理需要访问更新存储库。         |

> [!NOTE]
> Azure 虚拟机缩放集可以通过更新管理进行管理。 更新管理适用于实例本身，而不是基本映像。 您需要以增量方式安排更新，以便不会同时更新所有 VM 实例。
> 可以通过按照[板载非 Azure 计算机](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)下的步骤添加虚拟机缩放集的节点。

### <a name="unsupported-client-types"></a>不支持的客户端类型

下表列出了不支持的操作系统：

|操作系统  |说明  |
|---------|---------|
|Windows 客户端     | 不支持客户端操作系统（例如 Windows 7 和 Windows 10）。        |
|Windows Server 2016 Nano Server     | 不支持。       |
|Azure 库伯奈斯服务节点 | 不支持。 使用[在 Azure Kubernetes 服务 （AKS） 中将安全性和内核更新应用于 Linux 节点中描述的](../aks/node-updates-kured.md)修补过程|

### <a name="client-requirements"></a>客户端要求

以下信息描述了特定于操作系统的客户端要求。 有关其他指导，请参阅[网络规划](#ports)。

#### <a name="windows"></a>Windows

必须将 Windows 代理配置为与 WSUS 服务器通信，否则它们需要访问 Microsoft 更新。

您可以将更新管理与配置管理器一起使用。 要了解有关集成方案的详细信息，请参阅[将配置管理器与更新管理集成](oms-solution-updatemgmt-sccmintegration.md#configuration)。 日志[分析窗口代理](../azure-monitor/platform/agent-windows.md)是必需的。 如果您要载入 Azure VM，则会自动安装代理。

默认情况下，从 Azure 应用商店部署的 Windows VM 设置为从 Windows 更新服务接收自动更新。 当您添加此解决方案或将 Windows VM 添加到工作区时，此行为不会更改。 如果不主动通过此解决方案管理更新，则会应用默认行为（即自动应用更新）。

> [!NOTE]
> 用户可以修改组策略，以便计算机重新启动只能由用户执行，而不能由系统执行。 如果更新管理没有权利在没有用户手动交互的情况下重新启动计算机，则托管计算机可能会卡住。
>
> 有关详细信息，请参阅[为自动更新配置组策略设置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

对于 Linux，计算机需要访问更新存储库。 更新存储库可以是专用的，也可以是公共的。 需要 TLS 1.1 或 TLS 1.2 才能与更新管理进行交互。 此解决方案不支持配置为向多个日志分析工作区报告的 Linux 日志分析代理。 计算机还必须安装 Python 2.x。

有关如何为 Linux 安装日志分析代理并下载最新版本的信息，请参阅[Linux 的日志分析代理](../azure-monitor/platform/agent-linux.md)。 有关如何安装 Windows 的日志分析代理的信息，请参阅将[Windows 计算机连接到 Azure 监视器](../log-analytics/log-analytics-windows-agent.md)。

从 Azure 应用商店中可用的按需红帽企业 Linux （RHEL） 映像创建的 VM 将注册以访问部署在 Azure 中的[红帽更新基础结构 （RHUI）。](../virtual-machines/workloads/redhat/redhat-rhui.md) 任何其他 Linux 发行版都必须使用分发支持的方法从分发的在线文件存储库中更新。

## <a name="permissions"></a>权限

若要创建和管理更新部署，需要特定的权限。 要了解这些权限，请参阅[基于角色的访问和更新管理](automation-role-based-access-control.md#update-management-permissions)。

## <a name="solution-components"></a>解决方案组件

该解决方案包括以下资源。 启用解决方案时，这些资源将自动添加到自动化帐户中。 

### <a name="hybrid-worker-groups"></a>混合辅助角色组

启用此解决方案后，直接连接到日志分析工作区的任何 Windows 计算机将自动配置为混合 Runbook 辅助角色，以支持此解决方案中包含的 Runbook。

由解决方案管理的每个 Windows 计算机都作为自动化帐户的系统混合辅助角色组列在"混合辅助角色组"窗格中。 解决方案使用`Hostname FQDN_GUID`命名约定。 不能在帐户中通过 Runbook 将这些组作为目标进行操作。 如果尝试，尝试失败。 这些组旨在仅支持此管理解决方案。

如果对解决方案和混合 Runbook 辅助角色组成员身份使用相同的帐户，则可以将 Windows 计算机添加到自动化帐户中的"混合 Runbook 辅助角色"组，以支持自动化 Runbook。 此功能是在 7.2.12024.0 版本的混合 Runbook 辅助角色中添加的。

### <a name="management-packs"></a>管理包

如果系统中心操作管理器管理组[连接到日志分析工作区](../azure-monitor/platform/om-agents.md)，则操作管理器中将安装以下管理包。 添加解决方案后，这些管理包也会安装在直接连接的 Windows 计算机上。 你不需要对这些管理包进行配置或管理。

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果操作管理器 1807 或 2019 管理组连接到日志分析工作区，管理组中配置了代理以收集日志数据，则需要在`IsAutoRegistrationEnabled`**Microsoft.IntelligencePack.Azure自动化.HybridAgent.Init**规则中重写该参数并将其设置为 True。

有关如何更新解决方案管理包的详细信息，请参阅[将操作管理器连接到 Azure 监视器日志](../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 对于具有操作管理器代理的计算机，必须由更新管理完全管理，必须将代理更新为 Windows 的日志分析代理或 Linux 的日志分析代理。 若要了解如何更新代理，请参阅[如何升级 Operations Manager 代理](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)。 在使用操作管理器的环境中，必须运行系统中心操作管理器 2012 R2 UR 14 或更高版本。

## <a name="data-collection"></a>数据收集

### <a name="supported-agents"></a>支持的代理

下表介绍了此解决方案支持的连接的源：

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |该解决方案从 Windows 代理收集有关系统更新的信息，然后开始安装必需的更新。 |
| Linux 代理 |是 |该解决方案从 Linux 代理收集有关系统更新的信息，然后开始在受支持的发行版上安装必需的更新。 |
| Operations Manager 管理组 |是 |该解决方案从已连接的管理组中的代理收集有关系统更新的信息。<br/><br/>不需要从操作管理器代理到 Azure 监视器日志的直接连接。 数据将从管理组转发到 Log Analytics 工作区。 |

### <a name="collection-frequency"></a>收集频率

扫描每天为每个托管 Windows 计算机执行两次。 每隔 15 分钟就会调用一次 Windows API 来查询上次更新时间，以确定状态是否已更改。 如果状态已更改，则会启动符合性扫描。

每台托管 Linux 计算机每小时执行一次扫描。

仪表板可能需要 30 分钟到 6 小时才能显示来自托管计算机的更新数据。

使用更新管理的计算机的 Azure 监视器日志的平均数据使用量约为每月 25 MB。 此值仅是近似值，可能会更改，具体取决于您的环境。 我们建议您监视环境以跟踪您的确切使用情况。 有关详细信息，请参阅[管理使用情况和成本](../azure-monitor/platform/manage-cost-storage.md)。

## <a name="network-planning"></a><a name="ports"></a>网络规划

更新管理特别需要以下地址。 与以下地址的通信通过端口 443 进行。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|* .azure-automation.net | *.azure-automation.us|

对于 Windows 计算机，还必须允许流量到 Windows 更新所需的任何终结点。 您可以在[与 HTTP/代理相关的问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中找到所需终结点的更新列表。 如果您有本地 Windows[更新服务器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，还必须允许流量到[WSUS 密钥](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中指定的服务器。

有关红帽 Linux 计算机，请参阅有关所需终结点[的 RHUI 内容交付服务器的 IP。](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 有关其他 Linux 发行版，请参阅提供程序文档。

有关混合 Runbook 工作线程所需的端口的详细信息，请参阅[混合辅助角色端口](automation-hybrid-runbook-worker.md#hybrid-worker-role)。

我们建议您在定义异常时使用列出的地址。 对于 IP 地址，可以下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此文件每周更新一次，它反映当前部署的范围和对 IP 范围的任何即将更改。

按照["连接没有 Internet 的计算机"](../azure-monitor/platform/gateway.md)中的说明进行配置没有 Internet 访问的计算机。

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

### <a name="linux"></a><a name="linux-2"></a>Linux

|分类  |说明  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 所有其他不在性质上不重要的更新或不是安全更新的更新。        |

对于 Linux，更新管理可以区分云中的关键更新和安全更新，同时显示由于云中的数据丰富而导致的评估数据。 为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他发行版不同，CentOS 在 RTM 版本中没有此信息。 如果配置了 CentOS 计算机以返回以下命令的安全数据，则更新管理可以基于分类进行修补。

```bash
sudo yum -q --security check-update
```

目前没有支持在 CentOS 上启用本机分类数据可用性的方法。 此时，仅向可能自行启用此功能的客户提供尽力而为的支持。 

要对红帽企业版 6 上的更新进行分类，您需要安装 yum-安全插件。 在红帽企业Linux 7上，插件已经是yum本身的一部分，没有必要安装任何东西。 有关详细信息，请参阅以下红帽[知识文章](https://access.redhat.com/solutions/10021)。

## <a name="integrate-with-configuration-manager"></a>与 Configuration Manager 集成

投资 Microsoft 端点配置管理器来管理 PC、服务器和移动设备的客户还依靠配置管理器的优势和成熟度来帮助他们管理软件更新。 Configuration Manager 是其软件更新管理 (SUM) 周期的一部分。

要了解如何将管理解决方案与配置管理器集成，请参阅[将配置管理器与更新管理集成](oms-solution-updatemgmt-sccmintegration.md)。

### <a name="third-party-updates-on-windows"></a>Windows 上的第三方更新

更新管理依赖于本地配置的更新存储库来更新支持的 Windows 系统。 这是 WSUS 或 Windows 更新。 [系统中心更新发布者](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher)（更新发布者）等工具允许您使用 WSUS 导入和发布自定义更新。 此方案允许更新管理使用第三方软件更新使用配置管理器作为更新存储库的计算机。 若要了解如何配置 Updates Publisher，请参阅[安装 Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)。

## <a name="enable-update-management"></a><a name="onboard"></a>启用更新管理

要开始更新系统，您需要启用更新管理解决方案。 以下是用于加入解决方案的建议和支持的方法：

- [通过虚拟机](automation-onboard-solutions-from-vm.md)
- [通过浏览多个计算机](automation-onboard-solutions-from-browse.md)
- [通过自动化帐户](automation-onboard-solutions-from-automation-account.md)
- [使用 Azure 自动化 runbook](automation-onboard-solutions.md)
- [使用 Azure 资源管理器模板](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>后续步骤

查看 Azure 自动化[常见问题解答](automation-faq.md)，查看有关此解决方案的常见问题。
