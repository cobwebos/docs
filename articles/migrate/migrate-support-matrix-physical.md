---
title: Azure Migrate 中的物理服务器评估支持
description: 了解支持 Azure Migrate Server 评估的物理服务器评估
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: dffa95fe717f8588f56b9dee60ede8bbf44aceb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660354"
---
# <a name="support-matrix-for-physical-server-assessment"></a>物理服务器评估的支持矩阵 

本文汇总了使用 [Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具评估要迁移到 Azure 的物理服务器时的先决条件和支持要求。 如果要将物理服务器迁移到 Azure，请参阅 [迁移支持矩阵](migrate-support-matrix-physical-migration.md)。


若要评估物理服务器，请创建 Azure Migrate 项目，并将服务器评估工具添加到项目。 添加评估工具后，部署 [Azure Migrate 设备](migrate-appliance.md)。 此设备持续发现本地虚拟机，并向 Azure 发送虚拟机元数据和性能数据。 完成发现后，你将发现的虚拟机收集到组中，然后对组运行评估。


## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制** | 可在单个 [Azure Migrate 项目](migrate-support-matrix.md#azure-migrate-projects)中发现并评估多达35000的物理服务器。
**项目限制** | 可以在一个 Azure 订阅中创建多个项目。 除了物理服务器，项目还可以包括 VMware Vm 和 Hyper-v Vm，最高可达每个的评估限制。
**发现** | Azure Migrate 设备最多可以发现1000物理服务器。
**评估** | 最多可以在一个组中添加 35,000 个虚拟机。<br/><br/> 在单个评估中，最多可以评估35000台计算机。

[详细了解](concepts-assessment-calculation.md)评估。

## <a name="physical-server-requirements"></a>物理服务器要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **物理服务器部署**       | 物理服务器可以是独立服务器，也可以部署到群集中。 |
| **权限**           | **Windows：** 针对已加入域的计算机使用域帐户，针对未加入域的计算机使用本地帐户。 应将用户帐户添加到这些组：远程管理用户、性能监视器用户和性能日志用户。 <br/><br/> **Linux：** 需要在要发现的 Linux 服务器上拥有根帐户。 <br/> 另外，请确保使用以下命令设置所需的功能。 <br/> setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br/> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk（如果不存在 /usr/sbin/fdisk） <br/> setcap "cap_dac_override、cap_dac_read_search、cap_fowner、cap_fsetid、cap_setuid、cap_setpcap、cap_net_bind_service、cap_net_admin、cap_sys_chroot、cap_sys_admin、cap_sys_resource、cap_audit_control、cap_setfcap = + eip"/sbin/lvm <br/> setcap CAP_DAC_READ_SEARCH + eip/usr/sbin/dmidecode <br/> chmod a + r/sys/class/dmi/id/product_uuid
| **操作系统** | 所有 Windows 和 Linux 操作系统都可以进行迁移评估。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用 [Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 适用于物理服务器的设备可在 VM 或物理计算机上运行。 

- 了解物理服务器的 [设备要求](migrate-appliance.md#appliance---physical) 。
- 了解设备需要在[公有](migrate-appliance.md#public-cloud-urls)云和[政府](migrate-appliance.md#government-cloud-urls)云中访问的 URL。
- 使用从 Azure 门户下载的 [PowerShell 脚本](how-to-set-up-appliance-physical.md) 来设置设备。
在 Azure 政府版中， [使用此脚本](deploy-appliance-script-government.md)部署该设备。

## <a name="port-access"></a>端口访问

下表汇总了评估的端口要求。

**设备** | **Connection**
--- | ---
**设备** | TCP 端口3389上的入站连接，以允许与设备建立远程桌面连接。<br/><br/> 端口44368上的入站连接，使用 URL 远程访问设备管理应用程序： ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 端口443上的出站连接 (HTTPS) ，以将发现和性能元数据发送到 Azure Migrate。
**物理服务器** | **Windows：** WinRM 端口5985上的入站连接 (HTTP) 请求 Windows server 中的配置和性能元数据。 <br/><br/> **Linux：**  端口22上的入站连接 (TCP) ，以从 Linux 服务器拉取配置和性能元数据。 |

## <a name="agent-based-dependency-analysis-requirements"></a>基于代理的依赖关系分析要求

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地虚拟机之间的依赖关系。 下表总结了设置基于代理的依赖关系分析所需满足的要求。 物理服务器目前仅支持基于代理的依赖项分析。

**要求** | **详细信息** 
--- | --- 
**部署前** | 应有 Azure Migrate 项目，并已将服务器评估工具添加到项目中。<br/><br/>  在设置 Azure Migrate 设备来发现本地虚拟机后，就可以部署依赖关系可视化了<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> 了解如何设置 Azure Migrate 设备来评估 [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md) 或物理服务器。
**Azure Government** | 依赖关系可视化在 Azure 政府中不可用。
**Log Analytics** | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)中使用[服务映射](../azure-monitor/insights/service-map.md)解决方案来进行依赖关系可视化。<br/><br/> 你将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。 Azure Migrate 项目的工作区在添加后就无法修改了。 <br/><br/> 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或欧洲西部区域。 其他区域中的工作区无法与项目相关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 关联的工作区标记有迁移项目密钥和项目名称。
**必需代理** | 在要分析的每个虚拟机上，安装以下代理：<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地虚拟机没有连接到 Internet，你需要在虚拟机上下载并安装 Log Analytics 网关。<br/><br/> 详细了解如何安装 [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和欧洲西部区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> Azure Migrate 项目的工作区在添加后就无法修改了。
**成本** | 服务映射解决方案在前 180 天内（自你将 Log Analytics 工作区与 Azure Migrate 项目关联之日起）不收取任何费用。<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 在关联的 Log Analytics 工作区中使用除服务映射以外的其他任何解决方案都会产生 Log Analytics [标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射的使用不是免费的，每个节点将按照 Log Analytics 工作区的付费层收费。<br/><br/>如果你有在 Azure Migrate 正式发布（2018 年 2 月 28 日正式发布）之前创建的项目，可能需要支付额外的服务映射费用。 为了确保只在 180 天后付款，建议新建项目，因为在 Azure Migrate 正式发布之前的现有工作区仍需要收费。
**管理** | 将代理注册到工作区时，你使用 Azure Migrate 项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果你删除关联的 Azure Migrate 项目，工作区不会自动删除。 [请手动删除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非删除 Azure Migrate 项目，否则请不要删除由 Azure Migrate 创建的工作区。 否则，依赖项可视化功能将不会按预期工作。
**Internet 连接** | 如果虚拟机没有连接到 Internet，你需要在虚拟机上下载并安装 Log Analytics 网关。
**Azure Government** | 不支持基于代理的依赖关系分析。

## <a name="next-steps"></a>后续步骤

[准备物理服务器评估](tutorial-prepare-physical.md)。
