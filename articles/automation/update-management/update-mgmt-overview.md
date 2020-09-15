---
title: Azure 自动化更新管理概述
description: 本文概述了为 Windows 和 Linux 计算机实现更新的更新管理功能。
services: automation
ms.subservice: update-management
ms.date: 09/11/2020
ms.topic: conceptual
ms.openlocfilehash: ab2c584b1e62ac8296c4e9489a72489cd815fc3c
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089847"
---
# <a name="update-management-overview"></a>更新管理概述

可以使用 Azure 自动化中的更新管理，为 Azure、本地环境或其他云环境中的 Windows 和 Linux 计算机管理操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。

可以通过以下方式为 VM 启用更新管理功能：

* Azure [自动化帐户](update-mgmt-enable-automation-account.md) 中的一个或多个 azure 和非 azure 计算机。
* 对于非 Azure 计算机（包括在 [启用了 Azure Arc 的服务器](../../azure-arc/servers/overview.md) 上注册的计算机或服务器）， (预览) 。
* 对于单个 Azure VM，通过 Azure 门户中的“虚拟机”页启用。 此方案适用于 [Linux](../../virtual-machines/linux/tutorial-config-management.md#enable-update-management) 和 [Windows](../../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM。
* 对于[多个 Azure VM](update-mgmt-enable-portal.md)，从 Azure 门户的“虚拟机”页中选择它们进行启用。

> [!NOTE]
> 更新管理要求将 Log Analytics 工作区链接到自动化帐户。 有关受支持区域的明确列表，请参阅 [Azure 工作区映射](../how-to/region-mappings.md)。 区域映射不会影响在单独的区域中管理自动化帐户内 VM 的功能。

[Azure 资源管理器模板](update-mgmt-enable-template.md)可帮助将更新管理部署到新的或现有的自动化帐户以及订阅中的 Log Analytics 工作区。

> [!NOTE]
> 不能使用配置了更新管理功能的计算机从 Azure 自动化运行自定义脚本。 此计算机只能运行 Microsoft 签名的更新脚本。

## <a name="about-update-management"></a>关于更新管理

由更新管理托管的计算机使用以下配置执行评估和更新部署：

* 适用于 Windows 或 Linux 的 Log Analytics 代理
* 用于 Linux 的 PowerShell 所需状态配置 (DSC)
* 自动化混合 Runbook 辅助角色
* 适用于 Windows 计算机的 Microsoft 更新或 Windows Server Update Services (WSUS)

下图说明了更新管理如何评估和应用工作区中所有连接的 Windows Server 和 Linux 服务器的安全更新：

![更新管理工作流](./media/update-mgmt-overview/update-mgmt-updateworkflow.png)

更新管理可用于本机部署到同一个租户中的多个订阅中的计算机。

发布包后，Linux 计算机需要 2-3 小时才会显示修补程序以供评估。 对于 Windows 计算机，发布后，需要 12-15 小时才会显示修补程序以供评估。 当计算机完成对更新符合性的扫描时，代理会将信息批量转发到 Azure Monitor 日志。 在 Windows 计算机上，符合性扫描默认情况下每 12 小时运行一次。 对于 Linux 计算机，符合性扫描默认情况下每小时执行一次。 如果 Log Analytics 代理重启，则会在 15 分钟内启动符合性扫描。

除了按扫描计划扫描，更新符合性扫描还会在 Log Analytics 代理重启的 15 分钟内、更新安装前和更新安装后启动。

更新管理根据所配置的与之进行同步的源来报告计算机的更新情况。 如果 Windows 计算机配置为报告 [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (wsus) ，具体取决于 wsus 上次与 Microsoft 更新同步的时间，结果可能与 Microsoft 更新显示的结果不同。 对于配置为向本地存储库（而非公共存储库）报告的 Linux 计算机来说，行为也是如此。

> [!NOTE]
> 若要正确地向服务进行报告，更新管理要求启用某些 URL 和端口。 若要了解有关这些要求的详细信息，请参阅[网络配置](../automation-hybrid-runbook-worker.md#network-planning)。

可以创建计划的部署，在需要更新的计算机上部署和安装软件更新。 归类为“可选”的更新不包括在 Windows 计算机的部署范围内。 只有必需的更新会包括在部署范围内。

计划的部署定义哪些目标计算机接收适用的更新。 它通过以下某种方式来实现此目的：显式指定特定的计算机，或选择基于一组特定计算机的日志搜索（或基于根据指定条件动态选择 Azure VM 的 [Azure 查询]update-mgmt-view-logs.md）的[计算机组](../../azure-monitor/platform/computer-groups.md)。 这些组与[范围配置](../../azure-monitor/insights/solution-targeting.md)不同，后者用于控制接收配置以启用更新管理的目标计算机。 这会阻止它们执行和报告更新符合性，并安装已批准的所需更新。

定义部署时，还可以指定要批准的计划，并设置可以安装更新的一个时段。 此时段称为维护时段。 假设需要重启，并选择了相应的重启选项，则会预留 20 分钟的维护时段进行重启。 如果修补时间比预期时间长且维护时段少于 20 分钟，则不会进行重启。

通过 Azure 自动化中的 runbook 安装更新。 无法查看这些 runbook，它们不需要任何配置。 创建更新部署时，会创建一个在指定的时间为所包含的计算机启动主更新 runbook 的计划。 此主 Runbook 会在每个代理上启动一个子 Runbook 来安装必需的更新。

目标计算机会按更新部署中指定的日期和时间，以并行方式执行部署。 在安装之前，会运行扫描来验证更新是否仍然是必需的。 对于 WSUS 客户端计算机，如果更新未在 WSUS 中获得批准，则更新部署会失败。

不支持在多个 Log Analytics 工作区（也称为多宿主）中对计算机注册更新管理。

## <a name="clients"></a>客户端

### <a name="supported-client-types"></a>支持的客户端类型

下表列出了适用于更新评估和修补的支持的操作系统。 修补需要混合 Runbook 辅助角色。 有关混合 Runbook 辅助角色要求的信息，请参阅[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md)和[部署 Linux 混合 Runbook 辅助角色](../automation-linux-hrw-install.md)。

> [!NOTE]
> 仅自动化帐户和 Log Analytics 工作区[映射表](../how-to/region-mappings.md#supported-mappings)中列出的特定区域支持 Linux 计算机的更新评估。 

|操作系统  |说明  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 ||
|Windows Server 2008 R2（RTM 和 SP1 Standard）| 更新管理仅支持对此操作系统进行评估和修补。 Windows Server 2008 R2 不支持[混合 Runbook 辅助角色](../automation-windows-hrw-install.md)。 |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理需要具有访问更新存储库的权限。 基于分类的修补需要借助 `yum` 来返回 CentOS 的 RTM 版本中没有的安全数据。 有关 CentOS 上基于分类的修补的详细信息，请参阅 [Linux 上的更新分类](update-mgmt-view-update-assessments.md#linux)。          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理需要具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理需要具有访问更新存储库的权限。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理需要具有访问更新存储库的权限。         |

> [!NOTE]
> Azure 虚拟机规模集可通过更新管理进行管理。 更新管理适用于实例本身，而非基础映像。 需要以增量方式计划更新，以便不会同时更新所有 VM 实例。 可以按照[添加要启用更改跟踪和库存的非 Azure 计算机](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory)下的步骤，为虚拟机规模集添加节点。

### <a name="unsupported-client-types"></a>不支持的客户端类型

下表列出了不受支持的操作系统：

|操作系统  |说明  |
|---------|---------|
|Windows 客户端     | 不支持客户端操作系统（例如 Windows 7 和 Windows 10）。<br> 对于 Azure Windows 虚拟桌面 (WVD)，管理更新<br> 若要管理更新，请 [Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) 适用于 Windows 10 客户端计算机的修补程序管理。 |
|Windows Server 2016 Nano Server     | 不支持。       |
|Azure Kubernetes 服务节点 | 不支持。 使用[对 Azure Kubernetes 服务 (AKS) 中的 Linux 节点应用安全和内核更新](../../aks/node-updates-kured.md)中所述的修补过程|

### <a name="client-requirements"></a>客户端要求

以下信息介绍了特定于操作系统的客户端要求。 有关其他指南，请参阅[网络规划](#ports)。 若要了解 TLS 1.2 的客户端要求，请参阅[强制 Azure 自动化执行 TLS 1.2](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

#### <a name="windows"></a>Windows

Windows 代理必须配置为与 WSUS 服务器通信或需要有权访问 Microsoft 更新。 有关如何安装适用于 Windows 的 Log Analytics 代理的信息，请参阅[将 Windows 计算机连接到 Azure Monitor](../../azure-monitor/platform/agent-windows.md)。

可以将更新管理与 Microsoft Endpoint Configuration Manager 配合使用。 若要了解有关集成方案的详细信息，请参阅[将更新管理与 Microsoft Endpoint Configuration Manager](update-mgmt-mecmintegration.md)。 对于由 Configuration Manager 环境中的站点托管的 Windows 服务器，需要[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/platform/agent-windows.md)。 

默认情况下，从 Azure Marketplace 部署的 Windows Vm 设置为接收来自 Windows 更新服务的自动更新。 将 Windows VM 添加到工作区时，此行为不会更改。 如果不主动使用更新管理来管理更新，则会应用默认行为（即自动应用更新）。

> [!NOTE]
> 可以修改组策略，以便仅由用户而非系统来执行计算机重启。 如果在用户不进行手动交互的情况下，更新管理无权重启计算机，则托管计算机可能会停滞。 有关详细信息，请参阅[配置自动更新的组策略设置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

对于 Linux，计算机需要有权访问专用或公共的更新存储库。 需要 TLS 1.1 或 TLS 1.2 才能与更新管理进行交互。 更新管理不支持配置为向多个 Log Analytics 工作区报告的适用于 Linux 的 Log Analytics 代理。 计算机还必须安装 Python 2.x。

> [!NOTE]
> 仅特定区域支持 Linux 计算机的更新评估。 请参阅自动化帐户和 Log Analytics 工作区[映射表](../how-to/region-mappings.md#supported-mappings)。

有关如何安装适用于 Linux 的 Log Analytics 代理以及如何下载最新版本的信息，请参阅[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/agent-linux.md)。

通过 Azure Marketplace 中提供的按需 Red Hat Enterprise Linux (RHEL) 映像创建的 Vm 将被注册，以访问 Azure 中部署的 [Red Hat 更新基础结构 (RHUI) ](../../virtual-machines/workloads/redhat/redhat-rhui.md) 。 对于任何其他 Linux 发行版，必须使用发行版支持的方法从发行版联机文件存储库对其进行更新。

## <a name="permissions"></a>权限

若要创建和管理更新部署，需要特定的权限。 若要了解这些权限，请参阅[基于角色的访问 - 更新管理](../automation-role-based-access-control.md#update-management-permissions)。

## <a name="update-management-components"></a>更新管理组件

更新管理使用本部分中所述的资源。 启用更新管理时，这些资源会自动添加到自动化帐户。

### <a name="hybrid-runbook-worker-groups"></a>混合 Runbook 辅助角色组

启用更新管理后，任何直接连接到 Log Analytics 工作区的 Windows 计算机都会自动配置为混合 Runbook 辅助角色，为支持更新管理的 Runbook 提供支持。

更新管理托管的每个 Windows 计算机都会作为自动化帐户的一个“系统混合辅助角色组”列在“混合辅助角色组”窗格中。 这些组使用 `Hostname FQDN_GUID` 命名约定。 不能在帐户中通过 Runbook 将这些组作为目标进行操作。 如果尝试，则尝试会失败。 这些组仅用于为更新管理提供支持。 若要详细了解如何查看配置为混合 Runbook 辅助角色的 Windows 计算机的列表，请参阅 [查看混合 runbook](../automation-hybrid-runbook-worker.md#view-hybrid-runbook-workers)辅助角色。

如果为更新管理和混合 Runbook 辅助角色组成员身份使用同一帐户，则可以将 Windows 计算机添加到自动化帐户中的混合 Runbook 辅助角色组来为自动化 Runbook 提供支持。 此功能是在 7.2.12024.0 版本的混合 Runbook 辅助角色中添加的。

### <a name="management-packs"></a>管理包

如果 Operations Manager 管理组[已连接到 Log Analytics 工作区](../../azure-monitor/platform/om-agents.md)，则会在 Operations Manager 中安装以下管理包。 对于直接连接的 Windows 计算机上的更新管理，也会安装这些管理包。 你不需要对这些管理包进行配置或管理。

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果已将 Operations Manager 1807 或 2019 管理组连接到 Log Analytics 工作区并且在管理组中将代理配置为收集日志数据，则需要重写参数 `IsAutoRegistrationEnabled` 并在 Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init 规则中将其设置为 True。

有关管理包更新内容的详细信息，请参阅[将 Operations Manager 连接到 Azure Monitor 日志](../../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 若要更新管理通过 Log Analytics 代理完全管理计算机，必须更新为适用于 Windows 的 Log Analytics 代理或适用于 Linux 的 Log Analytics 代理。 若要了解如何更新代理，请参阅[如何升级 Operations Manager 代理](/system-center/scom/deploy-upgrade-agents)。 在使用 Operations Manager 的环境中，必须运行 System Center Operations Manager 2012 R2 UR 14 或更高版本。

## <a name="data-collection"></a>数据收集

### <a name="supported-sources"></a>受支持的源

下表介绍了更新管理支持的连接的源：

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |更新管理从 Windows 代理收集有关系统更新的信息，然后开始安装必需的更新。 |
| Linux 代理 |是 |更新管理从 Linux 代理收集有关系统更新的信息，然后开始在受支持的发行版上安装必需的更新。 |
| Operations Manager 管理组 |是 |“更新管理”从已连接的管理组中的代理收集有关系统更新的信息。<br/><br/>从 Operations Manager 代理到 Azure Monitor 日志的直接连接不是必需的。 数据将从管理组转发到 Log Analytics 工作区。 |

### <a name="collection-frequency"></a>收集频率

更新管理使用以下规则扫描托管计算机中的数据。 可能需要 30 分钟到 6 小时，仪表板才会显示托管计算机提供的已更新数据。

* 每个 Windows 计算机 - 更新管理每天对每个计算机扫描两次。

* 每个 Linux 计算机 - 更新管理每小时执行一次扫描。

使用更新管理的计算机的每月平均 Azure Monitor 日志数据使用情况大约为 25 MB。 此值仅为近似值，且随时可能基于环境而更改。 建议监视环境，以跟踪实际使用情况。 有关分析 Azure Monitor 日志数据使用情况的详细信息，请参阅[管理使用情况和成本](../../azure-monitor/platform/manage-cost-storage.md)。

## <a name="network-planning"></a><a name="ports"></a>网络规划

更新管理特别需要以下地址。 与以下地址的通信通过端口 443 进行。

|Azure Public  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

创建网络组安全规则或配置 Azure 防火墙以允许流量流向自动化服务和 Log Analytics 工作区时，请使用 [服务标记](../../virtual-network/service-tags-overview.md#available-service-tags) GuestAndHybridManagement 和 AzureMonitor 。 这样可简化网络安全规则的日常管理。 若要安全且私下地从 Azure Vm 连接到自动化服务，请参阅 [使用 Azure 专用链接](../how-to/private-link-security.md)。 若要获取当前服务标记和范围信息，并将其包含为本地防火墙配置的一部分，请参阅[可下载的 JSON 文件](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

对于 Windows 计算机，还必须允许流量发送到 Windows 更新所需的任何终结点。 可以在[与 HTTP/Proxy 相关的问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中找到所需终结点的更新列表。 如果拥有本地 [Windows 更新服务器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，则还必须允许流量发送到 [WSUS 密钥](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中指定的服务器。

对于 Red Hat Linux 计算机，请参阅[适用于 RHUI 内容分发服务器的 IP](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 了解所需的终结点。 对于其他 Linux 发行版，请参阅提供程序文档。

若要详细了解混合 Runbook 辅助角色所需的端口，请参阅[混合 Runbook 辅助角色的更新管理地址](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)。

如果 IT 安全策略不允许网络上的计算机连接到 Internet，则可以设置 [Log Analytics 网关](../../azure-monitor/platform/gateway.md)，然后将计算机配置为通过该网关连接到 Azure 自动化和 Azure Monitor。

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

下表定义了受支持的 Linux 更新分类。

|分类  |说明  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不是关键更新或不是安全更新的所有其他更新。        |

>[!NOTE]
>仅当在支持的 Azure 公有云区域中使用时，才可以使用适用于 Linux 计算机的更新分类。 使用以下国家/地区云区域中的更新管理时：
>* Azure 美国政府
>* 中国世纪互联
>
> 没有 Linux 更新分类，它们在 " **其他更新** " 类别下进行报告。 更新管理使用受支持的分发版发布的数据，尤其是其发布的 [OVAL](https://oval.mitre.org/)（开放式漏洞与评估语言）文件。 由于 internet 访问受限于这些国家云，因此更新管理无法访问和使用这些文件。

对于 Linux，由于云中的数据扩充，更新管理可以区分云中的关键更新和安全更新，同时显示评估数据。 为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他发行版不同，CentOS 在 RTM 版本中未提供此信息。 如果已将 CentOS 计算机配置为返回以下命令的安全数据，则更新管理可以基于分类进行修补。

```bash
sudo yum -q --security check-update
```

当前没有受支持的方法可用来在 CentOS 上提供原生分类数据。 目前，只能尽力为可能自己实现了此功能的客户提供支持。

若要对 Red Hat Enterprise 版本 6 上的更新进行分类，需要安装 yum 安全插件。 在 Red Hat Enterprise Linux 7 上，yum 本身已包含该插件，无需安装任何内容。 有关详细信息，请参阅以下 Red Hat [知识文章](https://access.redhat.com/solutions/10021)。

## <a name="integrate-update-management-with-configuration-manager"></a>将更新管理与 Configuration Manager 集成

已经投资购买了 Microsoft Endpoint Configuration Manager 来管理电脑、服务器和移动设备的客户还依赖 Configuration Manager 的优势和成熟度来帮助管理软件更新。 若要了解如何将更新管理与 Configuration Manager 集成，请参阅[将更新管理与 Windows Endpoint Configuration Manager 集成](update-mgmt-mecmintegration.md)。

## <a name="third-party-updates-on-windows"></a>Windows 上的第三方更新

更新管理依赖于本地配置的更新存储库来更新受支持的 Windows 系统（WSUS 或 Windows 更新）。 借助 [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) 等工具，可通过 WSUS 导入和发布自定义更新。 在这种情况下，允许更新管理借助第三方软件来更新使用 Configuration Manager 作为其更新存储库的计算机。 若要了解如何配置 Updates Publisher，请参阅[安装 Updates Publisher](/configmgr/sum/tools/install-updates-publisher)。

## <a name="enable-update-management"></a>启用更新管理

Azure [资源管理器模板](update-mgmt-enable-template.md)可帮助将更新管理部署到新的或现有的自动化帐户以及订阅中的 Azure Monitor Log Analytics 工作区。 它不会配置应管理的计算机范围，而是在使用模板后在单独的步骤中执行此操作。

可以通过以下方式启用更新管理并选择要管理的计算机：

* [从 Azure 虚拟机](update-mgmt-enable-vm.md)
* [浏览多个 Azure 虚拟机](update-mgmt-enable-portal.md)
* [通过 Azure 自动化帐户](update-mgmt-enable-automation-account.md)
* 对于启用了 Arc 的服务器 (预览) 或非 Azure 计算机，请安装 [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md) ，然后 [在工作区中启用计算机](update-mgmt-enable-automation-account.md#enable-machines-in-the-workspace) 以更新管理。

## <a name="next-steps"></a>后续步骤

* 有关使用更新管理的详细信息，请参阅[管理 VM 的更新](update-mgmt-manage-updates-for-vm.md)。

* 查看 [Azure 自动化常见问题解答](../automation-faq.md)中有关更新管理的常见问题。
