---
title: Azure Migrate 中的 Hyper-v 评估支持
description: 了解 Azure Migrate Server 评估的 Hyper-v 评估支持
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: c2df23b1566b4d793f4511499fd82d7912d0520f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85051917"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-v 评估的支持矩阵

本文汇总了使用[Azure Migrate： Server 评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具评估用于迁移到 Azure 的 hyper-v vm 时的先决条件和支持要求。 如果要将 Hyper-v Vm 迁移到 Azure，请参阅[迁移支持矩阵](migrate-support-matrix-hyper-v-migration.md)。

若要设置 Hyper-v VM 评估，请创建 Azure Migrate 项目，并将服务器评估工具添加到项目。 添加评估工具后，部署 [Azure Migrate 设备](migrate-appliance.md)。 此设备持续发现本地虚拟机，并向 Azure 发送虚拟机元数据和性能数据。 完成发现后，你将发现的虚拟机收集到组中，然后对组运行评估。


## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制** | 可在单个[Azure Migrate 项目](migrate-support-matrix.md#azure-migrate-projects)中发现并评估最多35000个 hyper-v vm。
**项目限制** | 可以在一个 Azure 订阅中创建多个项目。 除 Hyper-v Vm 外，项目还可以包括 VMware Vm 和物理服务器，每个服务器最多可包含评估限制。
**发现** | Azure Migrate 设备最多可以发现5000个 Hyper-v Vm。<br/><br/> 设备最多可以连接到300个 Hyper-v 主机。
**评估** | 最多可以在一个组中添加 35,000 个虚拟机。<br/><br/> 在一个组的单个评估中，最多可以评估35000个 Vm。

[详细了解](concepts-assessment-calculation.md)评估。



## <a name="hyper-v-host-requirements"></a>Hyper-v 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **Hyper-V 主机**       | Hyper-v 主机可以是独立的，也可以部署到群集中。<br/><br/> Hyper-v 主机可以运行 Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/> 不能访问运行 Windows Server 2012 的 Hyper-V 主机上的 VM。
| **权限**           | 你需要在 Hyper-v 主机上具有管理员权限。 <br/> 如果你不想分配管理员权限，请创建本地或域用户帐户，并将用户帐户添加到这些组-远程管理用户、Hyper-v 管理员和性能监视器用户。 |
| **PowerShell 远程处理**   | 必须在每个 Hyper-v 主机上启用[PowerShell 远程处理](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7)。 |
| **Hyper-V 副本**       | 如果使用 Hyper-v 副本（或拥有多个具有相同 VM 标识符的 Vm），并且使用 Azure Migrate 发现原始 Vm 和复制的 Vm，则 Azure Migrate 生成的评估可能不准确。 |


## <a name="vm-requirements"></a>VM 要求

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | 所有操作系统都可以进行迁移评估。  |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必须在你评估的 vm 上运行，才能捕获操作系统信息。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用 [Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 你可以使用从门户下载的压缩 Hyper-v VHD 或[PowerShell 脚本](deploy-appliance-script.md)来部署该设备。

- 了解 Hyper-v 的[设备要求](migrate-appliance.md#appliance---hyper-v)。
- 了解设备需要在[公有](migrate-appliance.md#public-cloud-urls)云和[政府](migrate-appliance.md#government-cloud-urls)云中访问的 URL。
- 在 Azure 政府中，必须[使用脚本](deploy-appliance-script-government.md)部署设备。

## <a name="port-access"></a>端口访问

下表汇总了评估的端口要求。

**设备** | **Connection**
--- | ---
**设备** | TCP 端口 3389 上的入站连接，可便于通过远程桌面连接来连接到设备。<br/><br/> 端口 44368 上的入站连接，用于使用 URL ``` https://<appliance-ip-or-name>:44368 ``` 远程访问设备管理应用<br/><br/> 端口443上的出站连接（HTTPS），用于将发现和性能元数据发送到 Azure Migrate。
**Hyper-v 主机/群集** | WinRM 端口5985（HTTP）上的入站连接，可使用通用信息模型（CIM）会话拉取 Hyper-v Vm 的元数据和性能数据。

## <a name="agent-based-dependency-analysis-requirements"></a>基于代理的依赖关系分析要求

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地虚拟机之间的依赖关系。 下表总结了设置基于代理的依赖关系分析所需满足的要求。 Hyper-v 目前仅支持基于代理的依赖项可视化。 

要求 | **详细信息** 
--- | --- 
**部署前** | 应有 Azure Migrate 项目，并已将服务器评估工具添加到项目中。<br/><br/>  在设置 Azure Migrate 设备来发现本地虚拟机后，就可以部署依赖关系可视化了<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> 了解如何设置 Azure Migrate 设备以便对[Hyper-v vm](how-to-set-up-appliance-hyper-v.md)进行评估。
**Azure Government** | 依赖关系可视化在 Azure 政府中不可用。
**Log Analytics** | Azure Migrate 在 [Azure Monitor 日志](../log-analytics/log-analytics-overview.md)中使用[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案来进行依赖关系可视化。<br/><br/> 你将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。 Azure Migrate 项目的工作区在添加后就无法修改了。 <br/><br/> 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或欧洲西部区域。 其他区域中的工作区无法与项目相关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 关联的工作区标记有迁移项目密钥和项目名称。
**必需代理** | 在要分析的每个虚拟机上，安装以下代理：<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地虚拟机没有连接到 Internet，你需要在虚拟机上下载并安装 Log Analytics 网关。<br/><br/> 详细了解如何安装 [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和欧洲西部区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域中。<br/><br/> Azure Migrate 项目的工作区在添加后就无法修改了。
**成本** | 服务映射解决方案在前 180 天内（自你将 Log Analytics 工作区与 Azure Migrate 项目关联之日起）不收取任何费用。<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 在关联的 Log Analytics 工作区中使用除服务映射以外的其他任何解决方案都会产生 Log Analytics [标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射的使用不是免费的，每个节点将按照 Log Analytics 工作区的付费层收费。<br/><br/>如果你有在 Azure Migrate 正式发布（2018 年 2 月 28 日正式发布）之前创建的项目，可能需要支付额外的服务映射费用。 为了确保只在 180 天后付款，建议新建项目，因为在 Azure Migrate 正式发布之前的现有工作区仍需要收费。
**管理** | 将代理注册到工作区时，你使用 Azure Migrate 项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果你删除关联的 Azure Migrate 项目，工作区不会自动删除。 [请手动删除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非删除 Azure Migrate 项目，否则请不要删除由 Azure Migrate 创建的工作区。 否则，依赖项可视化功能将不会按预期工作。
**Internet 连接** | 如果虚拟机没有连接到 Internet，你需要在虚拟机上下载并安装 Log Analytics 网关。
**Azure Government** | 不支持基于代理的依赖关系分析。

## <a name="next-steps"></a>后续步骤

[准备 Hyper-v VM 评估](tutorial-prepare-hyper-v.md)
