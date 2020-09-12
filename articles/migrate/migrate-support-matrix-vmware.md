---
title: Azure Migrate 中的 VMware 评估支持
description: 了解使用 Azure Migrate 服务器评估时的 VMware VM 评估支持。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 6716bea08347783d8c5728a4e346ffab8ea60a07
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660277"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 评估支持矩阵 

本文汇总了使用 [Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具发现和评估要迁移到 Azure 的 VMware vm 时的先决条件和支持要求。 若要评估 VMware Vm，请创建一个 Azure Migrate 项目，然后将服务器评估工具添加到项目。 添加评估工具后，部署 Azure Migrate 设备。 此设备持续发现本地虚拟机，并向 Azure 发送虚拟机元数据和性能数据。 完成发现后，你将发现的虚拟机收集到组中，然后对组运行评估。 

若要将 VMware VM 迁移到 Azure，请查阅[迁移支持矩阵](migrate-support-matrix-vmware-migration.md)。



## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**项目限制** | 可以在一个 Azure 订阅中创建多个项目。<br/><br/> 最多可以在一个[项目](migrate-support-matrix.md#azure-migrate-projects)中发现和评估 35,000 个 VMware VM。 项目中还可以包含物理服务器和 Hyper-V VM，只要不超过每个项目的评估限制即可。
**发现** | Azure Migrate 设备最多可以在 vCenter Server 上发现 10,000 个 VMware VM。
**评估** | 最多可以在一个组中添加 35,000 个虚拟机。<br/><br/> 一次评估中最多可以评估 35,000 个 VM。

[详细了解](concepts-assessment-calculation.md)评估。


## <a name="vmware-requirements"></a>VMware 要求

**VMware** | **详细信息**
--- | ---
**vCenter Server** | 你要发现和评估的计算机必须通过 vCenter Server 版本5.5、6.0、6.5、6.7 或7.0 进行管理。<br/><br/> 当前不支持通过在设备中提供 ESXi 主机详细信息来发现 VMware Vm。
**权限** | 服务器评估需要 vCenter Server 只读帐户进行发现和评估。<br/><br/> 如果要执行应用程序发现或依赖项可视化，帐户需要为**虚拟机**  >  **来宾操作**启用特权。

## <a name="vm-requirements"></a>VM 要求
**VMware** | **详细信息**
--- | ---
**VMware VM** | 所有操作系统都可以进行迁移评估。 
**存储** | 支持附加到 SCSI、IDE 和基于 SATA 的控制器的磁盘。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用 [Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 可以将设备部署为 VMWare VM，具体方法为使用 OVA 模板、导入到 vCenter Server 或使用 [PowerShell 脚本](deploy-appliance-script.md)。

- 了解 VMware 的[设备要求](migrate-appliance.md#appliance---vmware)。
- 在 Azure 政府中，必须[使用脚本](deploy-appliance-script-government.md)部署设备。
- 查看设备需要在 [公共](migrate-appliance.md#public-cloud-urls) 和 [政府](migrate-appliance.md#government-cloud-urls) 云中访问的 url。


## <a name="port-access-requirements"></a>端口访问要求

**设备** | **Connection**
--- | ---
**设备** | TCP 端口 3389 上的入站连接，可便于通过远程桌面连接来连接到设备。<br/><br/> 端口 44368 上的入站连接，用于使用 URL ```https://<appliance-ip-or-name>:44368``` 远程访问设备管理应用 <br/><br/>端口 443 (HTTPS) 上的出站连接，用于将发现和性能元数据发送到 Azure Migrate。
**vCenter 服务器** | TCP 端口 443 上的入站连接，可便于设备收集用于评估的配置和性能元数据。 <br/><br/> 默认情况下，设备在端口 443 上连接到 vCenter。 如果 vCenter 服务器在不同的端口上侦听，你可以在设置发现时修改端口。
**ESXi 主机** | 如果要执行 [应用程序发现](how-to-discover-applications.md)或 [无代理依赖项分析](concepts-dependency-visualization.md#agentless-analysis)，则设备会连接到 TCP 端口443上的 ESXi 主机，以发现应用程序，并在 vm 上运行无代理依赖项可视化。

## <a name="application-discovery-requirements"></a>应用程序发现要求

除了发现计算机外，服务器评估还可以发现计算机上运行的应用、角色和功能。 通过发现应用清单，可以确定和计划为本地工作负荷定制的迁移路径。 

**支持** | **详细信息**
--- | ---
**支持的虚拟机** | 目前，只有 VMware VM 才支持应用发现。
**发现** | 应用发现是无代理的。 它使用虚拟机来宾凭据，并使用 WMI 和 SSH 调用来远程访问虚拟机。
**VM 支持** | 运行所有 Windows 和 Linux 版本的 Vm 支持应用发现。
**vCenter** | 用于评估的 vCenter Server 只读帐户，需要为**虚拟机**  >  **来宾操作**启用特权，以便与用于应用程序发现的 VM 交互。
**VM 访问** | 应用发现需要虚拟机上的本地用户帐户才能发现应用程序。<br/><br/> Azure Migrate 当前支持将一个凭据用于所有 Windows 服务器，为所有 Linux 服务器支持一个凭据。<br/><br/> 你为 Windows VM 创建来宾用户帐户，并为所有 Linux VM 创建常规/普通用户帐户（非 sudo 访问权限）。
**VMware 工具** | 必须在要发现的 VM 上安装并运行 VMware 工具。 <br/><br/> VMware 工具版本必须高于 10.2.0。
**PowerShell** | VM 必须安装 PowerShell 版本 2.0 或更高版本。
**端口访问** | 在运行要发现的 VM 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口 443。
**限制** | 对于应用发现，最多可以在每个 Azure Migrate 设备上发现 10,000 个 VM。


## <a name="dependency-analysis-requirements-agentless"></a>依赖关系分析要求 (无代理) 

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地虚拟机之间的依赖关系。 下表总结了设置无代理依赖关系分析所需满足的要求。

要求 | **详细信息**
--- | --- 
**部署前** | 应有 Azure Migrate 项目，并已将服务器评估工具添加到项目中。<br/><br/>  设置 Azure Migrate 设备后，请部署依赖关系可视化，以发现本地 VMware 计算机。<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> [了解如何](how-to-set-up-appliance-vmware.md)设置 Azure Migrate 设备来评估 VMware VM。
**支持的虚拟机** | 目前，只有 VMware VM 才支持。
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2（64 位）。
**vCenter Server 凭据** | 依赖关系可视化需要 vCenter Server 帐户，帐户具有只读访问权限，并需要为“虚拟机”>“来宾操作”启用的特权。
**Windows VM 权限** |  对于依赖关系分析，Azure Migrate 设备必须有域管理员帐户或本地管理员帐户，才能访问 Windows VM。
**Linux VM** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。
**Linux 帐户** | 对于依赖关系分析，在 Linux 虚拟机上，Azure Migrate 设备需要具有根权限的用户帐户。<br/><br/> 或者，用户帐户需要对 /bin/netstat 和 /bin/ls 文件具有以下权限：CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。 使用以下命令设置这些功能： <br/> sudo setcap CAP_DAC_READ_SEARCH，CAP_SYS_PTRACE = ep/bin/ls <br/> sudo setcap CAP_DAC_READ_SEARCH，CAP_SYS_PTRACE = ep/bin/netstat
**必需代理** | 要分析的虚拟机上不需要任何代理。
**VMware 工具** | 必须在要分析的每个 VM 上安装并运行 VMware 工具（版本高于 10.2）。

**PowerShell** |Windows Vm 必须安装了 PowerShell 版本2.0 或更高版本。
**端口访问** |在运行要分析的 Vm 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口443。


## <a name="dependency-analysis-requirements-agent-based"></a>基于代理的)  (依赖关系分析需求

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地虚拟机之间的依赖关系。 下表总结了设置基于代理的依赖关系分析所需满足的要求。 

要求 | **详细信息** 
--- | --- 
**部署前** | 应有 Azure Migrate 项目（使用 Azure Migrate）：已将服务器评估工具添加到项目中。<br/><br/>  在设置 Azure Migrate 设备来发现本地虚拟机后，就可以部署依赖关系可视化了<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> 了解如何设置 Azure Migrate 设备来评估 [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md) 或物理服务器。
**支持的虚拟机** | 所有计算机都支持。
**Azure Government** | 依赖关系可视化在 Azure 政府中不可用。
**Log Analytics** | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)中使用[服务映射](../azure-monitor/insights/service-map.md)解决方案来进行依赖关系可视化。<br/><br/> 你将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。 Azure Migrate 项目的工作区在添加后就无法修改了。 <br/><br/> 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或欧洲西部区域。 其他区域中的工作区无法与项目相关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 关联的工作区标记有迁移项目密钥和项目名称。
**必需代理** | 在要分析的每个虚拟机上，安装以下代理：<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地虚拟机没有连接到 Internet，你需要在虚拟机上下载并安装 Log Analytics 网关。<br/><br/> 详细了解如何安装 [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和欧洲西部区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> Azure Migrate 项目的工作区在添加后就无法修改了。
**成本** | 服务映射解决方案在前 180 天内（自你将 Log Analytics 工作区与 Azure Migrate 项目关联之日起）不收取任何费用。<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 在关联的 Log Analytics 工作区中使用除服务映射以外的其他任何解决方案都会产生 Log Analytics [标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射的使用不是免费的，每个节点将按照 Log Analytics 工作区的付费层收费。<br/><br/>如果你有在 Azure Migrate 正式发布（2018 年 2 月 28 日正式发布）之前创建的项目，可能需要支付额外的服务映射费用。 为了确保只在 180 天后付款，建议新建项目，因为在 Azure Migrate 正式发布之前的现有工作区仍需要收费。
**管理** | 将代理注册到工作区时，你使用 Azure Migrate 项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果你删除关联的 Azure Migrate 项目，工作区不会自动删除。 [请手动删除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非删除 Azure Migrate 项目，否则请不要删除由 Azure Migrate 创建的工作区。 否则，依赖项可视化功能将不会按预期工作。
**Internet 连接** | 如果虚拟机没有连接到 Internet，你需要在虚拟机上下载并安装 Log Analytics 网关。
**Azure Government** | 不支持基于代理的依赖关系分析。


## <a name="next-steps"></a>后续步骤

- [查阅](best-practices-assessment.md)关于创建评估的最佳做法。
- [为 VMware VM 评估做好准备](tutorial-prepare-vmware.md)。
