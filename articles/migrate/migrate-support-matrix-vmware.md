---
title: Azure 迁移中的 VMware 评估支持
description: 通过 Azure 迁移服务器评估了解对 VMware VM 评估的支持。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: e0172656d06075f89a7c3a06e8d4e9be94e6f5d0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389301"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 评估的支持矩阵 

本文使用 Azure 迁移：服务器评估*（迁移服务-概述.md_azure-迁移-服务器-评估-工具）工具评估 VMware VM 以迁移到 Azure 时汇总先决条件和支持要求。 如果要将 VMware VM 迁移到 Azure，请查看[迁移支持矩阵](migrate-support-matrix-vmware-migration.md)。

要评估 VMware VM，请创建 Azure 迁移项目，然后将服务器评估工具添加到项目中。 添加该工具后，将部署 Azure[迁移设备](migrate-appliance.md)。 设备不断发现本地计算机，并将计算机元数据和性能数据发送到 Azure。 发现完成后，您将发现的计算机收集到组中，并为组运行评估。

## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**项目限制** | 可以在 Azure 订阅中创建多个项目。<br/><br/> 您可以在单个[项目中](migrate-support-matrix.md#azure-migrate-projects)发现和评估多达 35，000 个 VMware VM。 项目还可以包括物理服务器和超 VM，每个服务器都达到评估限制。
**发现** | Azure 迁移设备可以在 vCenter 服务器上发现多达 10，000 个 VMware VM。
**评估** | 单个组中最多可以添加 35，000 台计算机。<br/><br/> 您可以在单个评估中评估多达 35，000 个 VM。

[了解有关评估的更多](concepts-assessment-calculation.md)详细信息。


## <a name="application-discovery"></a>应用程序发现

除了发现计算机之外，服务器评估还可以发现在计算机上运行的应用、角色和功能。 通过发现应用清单，您可以识别和规划针对本地工作负荷量身定制的迁移路径。 

**支持** | **详细信息**
--- | ---
**支持的计算机** | 当前仅支持 VMware VM 的应用发现。
**发现** | 应用发现是无代理的。 它使用计算机来宾凭据，并使用 WMI 和 SSH 调用远程访问计算机。
**虚拟机支持** | 支持所有 Windows 和 Linux 版本的应用发现。
**vCenter 凭据** | 应用发现需要一个 vCenter Server 帐户，该帐户具有只读访问权限，并为虚拟机启用了>来宾操作的权限。
**VM 凭据** | 应用发现当前支持对所有 Windows 服务器使用一个凭据，支持对所有 Linux 服务器使用一个凭据。<br/><br/> 为 Windows VM 创建来宾用户帐户，为所有 Linux VM 创建常规/正常用户帐户（非 sudo 访问）。
**VMware 工具** | VMware 工具必须在要发现的 VM 上安装和运行。 <br/> VMware 工具版本必须晚于 10.2.0。
**电源外壳** | VM 必须安装 PowerShell 版本 2.0 或更高版本。
**端口访问** | 在运行要发现的 VM 的 ESXi 主机上，Azure 迁移设备必须能够连接到 TCP 端口 443。
**限制** | 对于应用发现，您可以发现每个 Azure 迁移设备上最多 10000 个 VM。



## <a name="vmware-requirements"></a>VMware 要求

**Vmware** | **详细信息**
--- | ---
**VMware VM** | 支持所有 Windows 和 Linux 操作系统的评估。
**vCenter 服务器** | 要发现和评估的计算机必须由 vCenter Server 版本 5.5、6.0、6.5 或 6.7 进行管理。
**权限（评估）** | vCenter 服务器只读帐户。
**权限（应用发现）** | vCenter 服务器帐户具有只读访问权限，并为虚拟机启用了>**来宾操作**的权限。
**权限（依赖项可视化）** | 具有只读访问权限的中央服务器帐户，并为**虚拟机** > **来宾操作**启用了权限。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure 迁移使用[Azure 迁移设备](migrate-appliance.md)进行发现和评估。 您可以使用 OVA 模板、导入到 vCenter 服务器或使用[PowerShell 脚本](deploy-appliance-script.md)将设备部署为 VMWare VM。

- 了解 VMware[的设备要求](migrate-appliance.md#appliance---vmware)。
- 了解设备需要访问的[URL。](migrate-appliance.md#url-access)

## <a name="port-access"></a>端口访问

**设备** | **连接**
--- | ---
家用电器 | TCP 端口 3389 上的入站连接，以允许远程桌面连接到设备。<br/><br/> 端口 44368 上的入站连接，用于使用 URL 远程访问设备管理应用：```https://<appliance-ip-or-name>:44368``` <br/><br/>端口 443 （HTTPS） 上的出站连接，用于向 Azure 迁移发送发现和性能元数据。
vCenter 服务器 | TCP 端口 443 上的入站连接，允许设备收集配置和性能元数据以进行评估。 <br/><br/> 默认情况下，设备连接到端口 443 上的 vCenter。 如果 vCenter 服务器侦听其他端口，则可以在设置发现时修改端口。
ESXi 主机（应用发现/无代理依赖关系分析） | 如果要执行[应用发现](how-to-discover-applications.md)或[无代理依赖项分析](concepts-dependency-visualization.md#agentless-analysis)，则设备连接到 TCP 端口 443 上的 ESXi 主机，以发现应用程序，并在 VM 上运行无代理依赖项可视化。

## <a name="agentless-dependency-analysis-requirements"></a>无代理依赖项分析要求

[依赖项分析](concepts-dependency-visualization.md)可帮助您确定要评估和迁移到 Azure 的本地计算机之间的依赖关系。 该表总结了设置无代理依赖项分析的要求。 

**要求** | **详细信息**
--- | --- 
**部署前** | 应设置 Azure 迁移项目，将服务器评估工具添加到项目中。<br/><br/>  设置 Azure 迁移设备以发现本地 VMWare 计算机后部署依赖项可视化。<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)将评估工具添加到现有项目。<br/> [了解如何](how-to-set-up-appliance-vmware.md)设置 Azure 迁移设备以评估 VMware VM。
**虚拟机支持** | 当前仅支持 VMware VM。
**视窗 VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows 服务器 2008 R2 （64 位）。
**Windows 帐户** |  对于依赖项分析，Azure 迁移设备需要本地或域管理员帐户来访问 Windows VM。
**Linux VM** | 红帽企业 Linux 7、 6、 5<br/> Ubuntu Linux 14.04， 16.04<br/> Debian 7、8<br/> 甲骨文 Linux 6， 7<br/> CentOS 5， 6， 7.
**Linux 帐户** | 对于依赖项分析，在 Linux 计算机上，Azure 迁移设备需要具有 Root 权限的用户帐户。<br/><br/> 或者，用户帐户需要 /bin/netstat 和 /bin/ls 文件的这些权限：CAP_DAC_READ_SEARCH和CAP_SYS_PTRACE。
**必需的代理** | 要分析的计算机上不需要代理。
**VMware 工具** | VMware 工具（晚于 10.2）必须在要分析的每个 VM 上安装和运行。
**vCenter 服务器凭据** | 依赖项可视化需要具有只读访问权限的 vCenter Server 帐户，并为虚拟机启用>来宾操作的权限。 
**电源外壳** | VM 必须安装 PowerShell 版本 2.0 或以上。
**端口访问** | 在运行要分析的 VM 的 ESXi 主机上，Azure 迁移设备必须能够连接到 TCP 端口 443。

## <a name="agent-based-dependency-analysis-requirements"></a>基于代理的依赖项分析要求

[依赖项分析](concepts-dependency-visualization.md)可帮助您确定要评估和迁移到 Azure 的本地计算机之间的依赖关系。 该表总结了设置基于代理的依赖项分析的要求。 

**要求** | **详细信息** 
--- | --- 
**部署前** | 应使用 Azure 迁移项目，将 Azure 迁移：服务器评估工具添加到项目中。<br/><br/>  设置 Azure 迁移设备以发现本地计算机后部署依赖项可视化<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)将评估工具添加到现有项目。<br/> 了解如何设置 Azure 迁移设备以评估[Hyper-V、VMware](how-to-set-up-appliance-hyper-v.md)或物理服务器。 [VMware](how-to-set-up-appliance-vmware.md)
**Azure 政府** | 依赖项可视化在 Azure 政府中不可用。
**日志分析** | Azure 迁移使用[Azure 监视器日志](../log-analytics/log-analytics-overview.md)中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案进行依赖项可视化。<br/><br/> 将新的或现有的日志分析工作区与 Azure 迁移项目相关联。 添加 Azure 迁移项目的工作区后无法对其进行修改。 <br/><br/> 工作区必须与 Azure 迁移项目处于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或西欧区域。 其他区域中的工作区不能与项目关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域。<br/><br/> 在日志分析中，与 Azure 迁移关联的工作区将使用迁移项目键和项目名称进行标记。
**必需的代理** | 在要分析的每台计算机上，安装以下代理：<br/><br/> [微软监控代理 （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [依赖项代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地计算机未连接到互联网，则需要在它们上下载并安装日志分析网关。<br/><br/> 了解有关安装[依赖项代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的更多详细信息。
**日志分析工作区** | 工作区必须与 Azure 迁移项目处于同一订阅中。<br/><br/> Azure 迁移支持驻留在美国东部、东南亚和西欧区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域。<br/><br/> 添加 Azure 迁移项目的工作区后无法对其进行修改。
**成本** | 服务映射解决方案在前 180 天内不产生任何费用（从将日志分析工作区与 Azure 迁移项目关联的当天起）/<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 在关联的日志分析工作区中使用服务映射以外的任何解决方案将会产生日志分析[的标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射使用不是免费的，每个节点将按日志分析工作区的付费层收费/<br/><br/>如果在 Azure 迁移通用性（GA- 2018 年 2 月 28 日）之前创建的项目，则可能已产生额外的服务映射费用。 为了确保仅在 180 天后付款，我们建议您创建新项目，因为 GA 之前的现有工作区仍可收费。
**管理** | 将代理注册到工作区时，可以使用 Azure 迁移项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果删除关联的 Azure 迁移项目，则不会自动删除工作区。 [手动删除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非删除 Azure 迁移项目，否则不要删除 Azure 迁移创建的工作区。 否则，依赖项可视化功能将不会按预期工作。
**互联网连接** | 如果计算机未连接到互联网，则需要在它们上安装日志分析网关。


## <a name="next-steps"></a>后续步骤

- [查看](best-practices-assessment.md)创建评估的最佳做法。
- [准备 VMware](tutorial-prepare-vmware.md)评估。
