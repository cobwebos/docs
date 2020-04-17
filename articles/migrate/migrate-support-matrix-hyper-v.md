---
title: 支持 Azure 迁移中的 Hyper-V 评估
description: 通过 Azure 迁移服务器评估了解对 Hyper-V 评估的支持
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 990d5026d9621c144c31635fabac4416eb9d20e6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538165"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-V 评估的支持矩阵

本文使用[Azure 迁移：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具汇总了评估 Hyper-V VM 以迁移到 Azure 时的先决条件和支持要求。 如果要将 Hyper-VM 迁移到 Azure，请查看[迁移支持矩阵](migrate-support-matrix-hyper-v-migration.md)。

要设置 Hyper-V VM 评估，请创建 Azure 迁移项目，并将服务器评估工具添加到项目中。 添加该工具后，将部署 Azure[迁移设备](migrate-appliance.md)。 设备不断发现本地计算机，并将计算机元数据和性能数据发送到 Azure。 发现完成后，您将发现的计算机收集到组中，并为组运行评估。


## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制** | 您可以在单个[Azure 迁移项目中](migrate-support-matrix.md#azure-migrate-projects)发现和评估多达 35，000 个 Hyper-VM。
**项目限制** | 可以在 Azure 订阅中创建多个项目。 除了 Hyper-V VM 之外，项目还可以包括 VMware VM 和物理服务器，每个服务器都达到评估限制。
**发现** | Azure 迁移设备可以发现多达 5000 个超 V VM。<br/><br/> 设备可连接到多达 300 台 Hyper-V 主机。
**评估** | 单个组中最多可以添加 35，000 台计算机。<br/><br/> 您可以在单个评估组中评估多达 35，000 个 VM。

[了解有关评估的更多](concepts-assessment-calculation.md)详细信息。



## <a name="hyper-v-host-requirements"></a>Hyper-V 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **超 V 主机**       | Hyper-V 主机可以是独立的，也可以部署在群集中。<br/><br/> Hyper-V 主机可以运行 Windows 服务器 2019、Windows 服务器 2016 或 Windows 服务器 2012 R2。<br/> 不能访问运行 Windows Server 2012 的 Hyper-V 主机上的 VM。
| **权限**           | 您需要对 Hyper-V 主机授予管理员权限。 <br/> 如果不想分配管理员权限，请创建本地或域用户帐户，并将用户帐户添加到这些组 - 远程管理用户、Hyper-V 管理员和性能监视器用户。 |
| **PowerShell 远程处理**   | 必须在每个 Hyper-V 主机上启用[PowerShell 远程处理](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7)。 |
| **Hyper-V 副本**       | 如果使用 Hyper-V 副本（或者有多个 VM 具有相同的 VM 标识符），并且发现使用 Azure 迁移的原始 VM 和复制 VM，则 Azure 迁移生成的评估可能不准确。 |


## <a name="hyper-v-vm-requirements"></a>超 V VM 要求

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | 所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统。 |
| **集成服务**       | [Hyper-V 集成服务](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必须在您评估的 VM 上运行，以便捕获操作系统信息。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure 迁移使用[Azure 迁移设备](migrate-appliance.md)进行发现和评估。 您可以使用从门户下载的压缩超 VHd 或使用[PowerShell 脚本](deploy-appliance-script.md)部署设备。

- 了解 Hyper-V 的设备[要求](migrate-appliance.md#appliance---hyper-v)。
- 了解产品[在公共和政府云](migrate-appliance.md#public-cloud-urls)中需要访问的 URL。 [government](migrate-appliance.md#government-cloud-urls)
- 在 Azure 政府中，必须使用脚本部署设备。

## <a name="port-access"></a>端口访问

下表总结了评估的端口要求。

**设备** | **连接**
--- | ---
**应用** | TCP 端口 3389 上的入站连接，以允许远程桌面连接到设备。<br/><br/> 端口 44368 上的入站连接，用于使用 URL 远程访问设备管理应用：``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 端口 443 （HTTPS） 上的出站连接，用于向 Azure 迁移发送发现和性能元数据。
**Hyper-V 主机/群集** | WinRM 上的入站连接端口 5985 （HTTP） 和 5986 （HTTPS），以便使用通用信息模型 （CIM） 会话提取 Hyper-V VM 的元数据和性能数据。

## <a name="agent-based-dependency-analysis-requirements"></a>基于代理的依赖项分析要求

[依赖项分析](concepts-dependency-visualization.md)可帮助您确定要评估和迁移到 Azure 的本地计算机之间的依赖关系。 该表总结了设置基于代理的依赖项分析的要求。 Hyper-V 目前仅支持基于代理的依赖项可视化。 

**要求** | **详细信息** 
--- | --- 
**部署前** | 应设置 Azure 迁移项目，将服务器评估工具添加到项目中。<br/><br/>  设置 Azure 迁移设备以发现本地计算机后部署依赖项可视化<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)将评估工具添加到现有项目。<br/> 了解如何设置 Azure 迁移设备以评估[超 V VM。](how-to-set-up-appliance-hyper-v.md)
**Azure Government** | 依赖项可视化在 Azure 政府中不可用。
**Log Analytics** | Azure 迁移使用[Azure 监视器日志](../log-analytics/log-analytics-overview.md)中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案进行依赖项可视化。<br/><br/> 将新的或现有的日志分析工作区与 Azure 迁移项目相关联。 添加 Azure 迁移项目的工作区后无法对其进行修改。 <br/><br/> 工作区必须与 Azure 迁移项目处于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或西欧区域。 其他区域中的工作区不能与项目关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域。<br/><br/> 在日志分析中，与 Azure 迁移关联的工作区将使用迁移项目键和项目名称进行标记。
**必需的代理** | 在要分析的每台计算机上，安装以下代理：<br/><br/> [微软监控代理 （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [依赖项代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地计算机未连接到互联网，则需要在它们上下载并安装日志分析网关。<br/><br/> 了解有关安装[依赖项代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的更多详细信息。
**Log Analytics 工作区** | 工作区必须与 Azure 迁移项目处于同一订阅中。<br/><br/> Azure 迁移支持驻留在美国东部、东南亚和西欧区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域。<br/><br/> 添加 Azure 迁移项目的工作区后无法对其进行修改。
**成本** | 服务映射解决方案在前 180 天内不产生任何费用（从将日志分析工作区与 Azure 迁移项目关联的当天起）/<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 在关联的日志分析工作区中使用服务映射以外的任何解决方案将会产生日志分析[的标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射使用不是免费的，每个节点将按日志分析工作区的付费层收费/<br/><br/>如果在 Azure 迁移通用性（GA- 2018 年 2 月 28 日）之前创建的项目，则可能已产生额外的服务映射费用。 为了确保仅在 180 天后付款，我们建议您创建新项目，因为 GA 之前的现有工作区仍可收费。
**管理** | 将代理注册到工作区时，可以使用 Azure 迁移项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果删除关联的 Azure 迁移项目，则不会自动删除工作区。 [手动删除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非删除 Azure 迁移项目，否则不要删除 Azure 迁移创建的工作区。 否则，依赖项可视化功能将不会按预期工作。
**互联网连接** | 如果计算机未连接到互联网，则需要在它们上安装日志分析网关。
**Azure Government** | 不支持基于代理的依赖项分析。

## <a name="next-steps"></a>后续步骤

[准备超 V VM 评估](tutorial-prepare-hyper-v.md)
