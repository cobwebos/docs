---
title: Azure Migrate 中的 VMware 评估支持
description: 了解对 VMware VM 评估的支持 Azure Migrate Server 评估。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a0d05c56670c54aca25232a86b5a0e89d2f0bcfd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983646"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 评估的支持矩阵 

本文汇总了使用[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具评估要迁移到 Azure 的 VMware vm 时的先决条件和支持要求。 如果要将 VMware Vm 迁移到 Azure，请参阅[迁移支持矩阵](migrate-support-matrix-vmware-migration.md)。

若要评估 VMware Vm，请创建一个 Azure Migrate 项目，然后将服务器评估工具添加到项目。 添加该工具后，可以部署[Azure Migrate 设备](migrate-appliance.md)。 设备持续发现本地计算机，并将计算机元数据和性能数据发送到 Azure。 发现完成后，可将发现的计算机收集到组中，并对组运行评估。

## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**项目限制** | 可以在 Azure 订阅中创建多个项目。<br/><br/> 可以在单个[项目](migrate-support-matrix.md#azure-migrate-projects)中发现并评估多达35000的 VMware vm。 项目还可以包括物理服务器、Hyper-v Vm，最高可达每个的评估限制。
**发现** | Azure Migrate 设备在 vCenter Server 上最多可以发现 10000 VMware Vm。
**评估** | 最多可以在一个组中添加35000台计算机。<br/><br/> 在单个评估中，最多可以评估 35000 Vm。

[了解](concepts-assessment-calculation.md)有关评估的详细信息。


## <a name="application-discovery"></a>应用程序发现

除了发现计算机外，服务器评估还可以发现计算机上运行的应用、角色和功能。 发现您的应用程序清单后，您可以确定和规划为本地工作负荷定制的迁移路径。 

**支持** | **详细信息**
--- | ---
**支持的计算机** | 目前仅 VMware Vm 支持应用程序发现。
**发现** | 应用程序发现是无代理的。 它使用计算机来宾凭据，并使用 WMI 和 SSH 调用远程访问计算机。
**VM 支持** | 所有 Windows 和 Linux 版本都支持应用发现。
**vCenter 凭据** | 应用发现需要具有只读访问权限的 vCenter Server 帐户，并为虚拟机启用了特权 > 来宾操作。
**VM 凭据** | 应用程序发现当前支持所有 Windows 服务器使用一个凭据，并支持为所有 Linux 服务器使用一个凭据。<br/><br/> 为 Windows Vm 创建来宾用户帐户，为所有 Linux Vm 创建常规/普通用户帐户（非 sudo 访问权限）。
**VMware 工具** | 必须在要发现的 Vm 上安装并运行 VMware 工具。 <br/> VMware 工具版本必须晚于10.2.0。
**PowerShell** | Vm 必须安装了 PowerShell 版本2.0 或更高版本。
**端口访问** | 在运行要发现的 Vm 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口443。
**限制** | 对于应用程序发现，可以在每个 Azure Migrate 设备上发现多达 10000 Vm。



## <a name="vmware-requirements"></a>VMware 要求

**VMware** | **详细信息**
--- | ---
**VMware VM** | 所有 Windows 和 Linux 操作系统都支持评估。
**vCenter Server** | 要发现和评估的计算机必须由 vCenter Server 5.5、6.0、6.5 或6.7 版本来管理。
**权限（评估）** | vCenter Server 只读帐户。
**权限（应用程序发现）** | vCenter Server 具有只读访问权限的帐户以及为虚拟机启用的特权 **> 来宾操作**。
**权限（依赖项可视化）** | vCenter Server 具有只读访问权限的帐户，以及为**虚拟机** > **来宾操作**启用的特权。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用[Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 你可以使用 .OVA 模板将设备部署为 VMWare VM，并将其导入 vCenter Server 或使用[PowerShell 脚本](deploy-appliance-script.md)。

- 了解 VMware 的[设备要求](migrate-appliance.md#appliance---vmware)。
- 了解设备需要在[公共](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)云中访问的 url。
- 在 Azure 政府版中，必须[使用脚本](deploy-appliance-script-government.md)部署该设备。


## <a name="port-access"></a>端口访问

**设备** | **连接**
--- | ---
家用电器 | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/><br/> 端口44368上的入站连接，使用 URL 远程访问设备管理应用程序：```https://<appliance-ip-or-name>:44368``` <br/><br/>端口443上的出站连接（HTTPS），用于将发现和性能元数据发送到 Azure Migrate。
vCenter 服务器 | TCP 端口443上的入站连接，使设备能够收集配置和性能元数据以进行评估。 <br/><br/> 默认情况下，设备会在端口443上连接到 vCenter。 如果 vCenter 服务器侦听其他端口，则可以在设置发现时修改端口。
ESXi 主机（应用程序发现/无代理依赖项分析） | 如果要执行[应用程序发现](how-to-discover-applications.md)或[无代理依赖项分析](concepts-dependency-visualization.md#agentless-analysis)，则设备会连接到 TCP 端口443上的 ESXi 主机，以发现应用程序，并在 vm 上运行无代理依赖项可视化。

## <a name="application-discovery"></a>应用程序发现

除了发现计算机外，服务器评估还可以发现计算机上运行的应用、角色和功能。 发现您的应用程序清单后，您可以确定和规划为本地工作负荷定制的迁移路径。 

**支持** | **详细信息**
--- | ---
**支持的计算机** | 目前仅 VMware Vm 支持应用程序发现。
**发现** | 应用程序发现是无代理的。 它使用计算机来宾凭据，并使用 WMI 和 SSH 调用远程访问计算机。
**VM 支持** | 所有 Windows 和 Linux 版本都支持应用发现。
**vCenter 凭据** | 应用发现需要具有只读访问权限的 vCenter Server 帐户，并为虚拟机启用了特权 > 来宾操作。
**VM 凭据** | 应用程序发现当前支持所有 Windows 服务器使用一个凭据，并支持为所有 Linux 服务器使用一个凭据。<br/><br/> 为 Windows Vm 创建来宾用户帐户，为所有 Linux Vm 创建常规/普通用户帐户（非 sudo 访问权限）。
**VMware 工具** | 必须在要发现的 Vm 上安装并运行 VMware 工具。 <br/> VMware 工具版本必须晚于10.2.0。
**PowerShell** | Vm 必须安装了 PowerShell 版本2.0 或更高版本。
**端口访问** | 在运行要发现的 Vm 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口443。
**限制** | 对于应用程序发现，可以在每个 Azure Migrate 设备上发现多达 10000 Vm。


## <a name="agentless-dependency-analysis-requirements"></a>无代理依赖项分析需求

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地计算机之间的依赖关系。 该表总结了设置无代理依赖项分析的要求。 

**要求** | **详细信息**
--- | --- 
**部署之前** | 您应该准备好一个 Azure Migrate 项目，并将服务器评估工具添加到项目。<br/><br/>  设置 Azure Migrate 设备后，请部署依赖关系可视化，以发现本地 VMWare 计算机。<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> [了解如何](how-to-set-up-appliance-vmware.md)为 VMware vm 设置 Azure Migrate 设备进行评估。
**VM 支持** | 目前仅支持 VMware Vm。
**Windows Vm** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 （64）。
**Windows 帐户** |  对于依赖项分析，Azure Migrate 设备需要域管理员帐户或本地管理员帐户才能访问 Windows Vm。
**Linux Vm** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。
**Linux 帐户** | 对于依赖项分析，在 Linux 计算机上，Azure Migrate 设备需要具有 Root 权限的用户帐户。<br/><br/> 或者，用户帐户需要对/bin/netstat 和/bin/ls 文件的这些权限： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。
**必需的代理** | 你要分析的计算机上无需代理。
**VMware 工具** | 必须在要分析的每个 VM 上安装并运行 VMware 工具（超过10.2 个）。
**vCenter Server 凭据** | 依赖项可视化需要 vCenter Server 帐户具有只读访问权限，并且为虚拟机启用了特权 > 来宾操作。 
**PowerShell** | Vm 必须安装了 PowerShell 版本2.0 或更高版本。
**端口访问** | 在运行要分析的 Vm 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口443。


## <a name="agent-based-dependency-analysis-requirements"></a>基于代理的依赖项分析需求

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地计算机之间的依赖关系。 该表总结了设置基于代理的依赖项分析的要求。 

**要求** | **详细信息** 
--- | --- 
**部署之前** | 您应该准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。<br/><br/>  设置 Azure Migrate 设备以发现本地计算机后，部署依赖关系可视化<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> 了解如何设置 Azure Migrate 设备，以便评估[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或物理服务器。
**Azure Government** | 依赖关系可视化在 Azure 政府版中不可用。
**Log Analytics** | Azure Migrate 使用[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案进行依赖关系可视化。<br/><br/> 将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。 添加 Azure Migrate 项目后，不能修改该工作区的工作区。 <br/><br/> 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或西欧区域。 其他区域中的工作区不能与项目关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 相关联的工作区用迁移项目密钥和项目名称进行标记。
**必需的代理** | 在要分析的每台计算机上，安装以下代理：<br/><br/> [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/> [依赖关系代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地计算机未连接到 internet，则需要下载并安装 Log Analytics 网关。<br/><br/> 了解有关安装[依赖关系代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的详细信息。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和西欧区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域中。<br/><br/> 添加 Azure Migrate 项目后，不能修改该工作区的工作区。
**代价** | 服务映射解决方案不会在前180天内产生任何费用（从将 Log Analytics 工作区与 Azure Migrate 项目关联的那一天）/<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 使用关联 Log Analytics 工作区中服务映射以外的任何解决方案将导致 Log Analytics 的[标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射使用情况并不免费，并按 Log Analytics 工作区的付费层收费<br/><br/>如果你的项目是在 Azure Migrate 正式发布（GA-28 2018 年2月）之前创建的，则可能会产生额外服务映射费用。 为了确保仅在180天后付款，建议你创建一个新项目，因为 GA 之前的现有工作区仍可计费。
**Management** | 将代理注册到工作区时，将使用 Azure Migrate 项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果删除关联的 Azure Migrate 项目，则不会自动删除该工作区。 请[手动将其删除](../azure-monitor/platform/manage-access.md)。<br/><br/> 请勿删除 Azure Migrate 创建的工作区，除非删除 Azure Migrate 项目。 否则，依赖项可视化功能将不会按预期工作。
**Internet 连接** | 如果计算机未连接到 internet，则需要在其上安装 Log Analytics 网关。
**Azure Government** | 不支持基于代理的依赖项分析。


## <a name="next-steps"></a>后续步骤

- [查看](best-practices-assessment.md)创建评估的最佳实践。
- [准备 VMware](tutorial-prepare-vmware.md)评估。
