---
title: Azure Migrate 中的物理服务器评估支持
description: 了解支持 Azure Migrate Server 评估的物理服务器评估
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 31fd676a339a6c82cec84e0f355ac875f68a653c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983663"
---
# <a name="support-matrix-for-physical-server-assessment"></a>物理服务器评估的支持矩阵 

本文汇总了使用[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具评估要迁移到 Azure 的物理服务器时的先决条件和支持要求。 如果要将物理服务器迁移到 Azure，请参阅[迁移支持矩阵](migrate-support-matrix-physical-migration.md)。


若要评估物理服务器，请创建 Azure Migrate 项目，并将服务器评估工具添加到项目。 添加该工具后，可以部署[Azure Migrate 设备](migrate-appliance.md)。 设备持续发现本地计算机，并将计算机元数据和性能数据发送到 Azure。 发现完成后，可将发现的计算机收集到组中，并对组运行评估。


## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制** | 可在单个[Azure Migrate 项目](migrate-support-matrix.md#azure-migrate-projects)中发现并评估多达35000的物理服务器。
**项目限制** | 可以在 Azure 订阅中创建多个项目。 除了物理服务器，项目还可以包括 VMware Vm 和 Hyper-v Vm，最高可达每个的评估限制。
**发现** | Azure Migrate 设备最多可以发现250物理服务器。
**评估** | 最多可以在一个组中添加35000台计算机。<br/><br/> 在单个评估中，最多可以评估35000台计算机。

[了解](concepts-assessment-calculation.md)有关评估的详细信息。

## <a name="physical-server-requirements"></a>物理服务器要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **物理服务器部署**       | 物理服务器可以是独立服务器，也可以部署到群集中。 |
| **权限**           | **Windows：** 你需要是域管理员，或者是你要发现的所有 Windows 服务器上的本地管理员。 应将用户帐户添加到以下组：远程管理用户、性能监视器用户和性能日志用户。 <br/><br/> **Linux：** 需要在要发现的 Linux 服务器上拥有根帐户。 |
| **操作系统** | Azure 支持的所有[windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)服务器操作系统，WINDOWS Server 2003 和 SUSE Linux 除外。<br/><br/> Windows 10 和 Windows 8 客户端操作系统。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用[Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 适用于物理服务器的设备可在 VM 或物理计算机上运行。 

- 了解物理服务器的[设备要求](migrate-appliance.md#appliance---physical)。
- 了解设备需要在[公共](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)云中访问的 url。
- 使用从 Azure 门户下载的[PowerShell 脚本](how-to-set-up-appliance-physical.md)来设置设备。
在 Azure 政府版中，[使用此脚本](deploy-appliance-script-government.md)部署该设备。

## <a name="port-access"></a>端口访问

下表汇总了评估的端口要求。

**设备** | **连接**
--- | ---
**家用电器** | TCP 端口3389上的入站连接，以允许与设备建立远程桌面连接。<br/><br/> 端口44368上的入站连接，使用 URL 远程访问设备管理应用程序：``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 端口443上的出站连接（HTTPS），用于将发现和性能元数据发送到 Azure Migrate。
**物理服务器** | **Windows：** WinRM 端口5985（HTTP）和5986（HTTPS）上的入站连接，用于从 Windows server 拉取配置和性能元数据。 <br/><br/> **Linux：** 端口22（UDP）上的入站连接，用于从 Linux 服务器拉取配置和性能元数据。 |

## <a name="agent-based-dependency-analysis-requirements"></a>基于代理的依赖项分析需求

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地计算机之间的依赖关系。 该表总结了设置基于代理的依赖项分析的要求。 物理服务器目前仅支持基于代理的依赖项分析。

**要求** | **详细信息** 
--- | --- 
**部署之前** | 您应该准备好一个 Azure Migrate 项目，并将服务器评估工具添加到项目。<br/><br/>  设置 Azure Migrate 设备以发现本地计算机后，部署依赖关系可视化<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> 了解如何设置 Azure Migrate 设备，以便评估[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或物理服务器。
**Azure Government** | 依赖关系可视化在 Azure 政府版中不可用。
**Log Analytics** | Azure Migrate 使用[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案进行依赖关系可视化。<br/><br/> 将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。 添加 Azure Migrate 项目后，不能修改该工作区的工作区。 <br/><br/> 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或西欧区域。 其他区域中的工作区不能与项目关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 相关联的工作区用迁移项目密钥和项目名称进行标记。
**必需的代理** | 在要分析的每台计算机上，安装以下代理：<br/><br/> [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/> [依赖关系代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地计算机未连接到 internet，则需要下载并安装 Log Analytics 网关。<br/><br/> 了解有关安装[依赖关系代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的详细信息。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和西欧区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域中。<br/><br/> 添加 Azure Migrate 项目后，不能修改该工作区的工作区。
**代价** | 服务映射解决方案不会在前180天内产生任何费用（从将 Log Analytics 工作区与 Azure Migrate 项目关联的那一天）/<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 使用关联 Log Analytics 工作区中服务映射以外的任何解决方案将导致 Log Analytics 的[标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射使用情况并不免费，并按 Log Analytics 工作区的付费层收费<br/><br/>如果你的项目是在 Azure Migrate 正式发布（GA-28 2018 年2月）之前创建的，则可能会产生额外服务映射费用。 为了确保仅在180天后付款，建议你创建一个新项目，因为 GA 之前的现有工作区仍可计费。
**Management** | 将代理注册到工作区时，将使用 Azure Migrate 项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果删除关联的 Azure Migrate 项目，则不会自动删除该工作区。 请[手动将其删除](../azure-monitor/platform/manage-access.md)。<br/><br/> 请勿删除 Azure Migrate 创建的工作区，除非删除 Azure Migrate 项目。 否则，依赖项可视化功能将不会按预期工作。
**Internet 连接** | 如果计算机未连接到 internet，则需要在其上安装 Log Analytics 网关。
**Azure Government** | 不支持基于代理的依赖项分析。

## <a name="next-steps"></a>后续步骤

[准备物理服务器评估](tutorial-prepare-physical.md)。
