---
title: Azure Migrate 中的 Hyper-v 评估支持
description: 了解支持 Azure Migrate 的 Hyper-v 评估。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 9c1228992d71e56b9118e88967478e619c14959a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834461"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-v 评估的支持矩阵

本文总结了在[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)的情况下评估 hyper-v vm 的支持设置和限制。 如果正在寻找有关将 Hyper-v Vm 迁移到 Azure 的信息，请参阅[迁移支持矩阵](migrate-support-matrix-hyper-v-migration.md)。

## <a name="overview"></a>概述

若要使用本文评估要迁移到 Azure 的本地计算机，请将 Azure Migrate： Server 评估工具添加到 Azure Migrate 项目。 部署[Azure Migrate 设备](migrate-appliance.md)。 设备持续发现本地计算机，并将配置和性能数据发送到 Azure。 计算机发现后，你可以将发现的计算机收集到组中，并对组运行评估。


## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制**| 发现和评估单个[项目](migrate-support-matrix.md#azure-migrate-projects)中最多35000个 hyper-v vm。
**项目限制** | 可以在 Azure 订阅中创建多个项目。 项目可以包括 VMware Vm、Hyper-v Vm 和物理服务器，直至达到评估限制。
**发现** | Azure Migrate 设备最多可以发现5000个 Hyper-v Vm。<br/><br/> 设备最多可以连接到300个 Hyper-v 主机。
**评估** | 最多可以在一个组中添加35000台计算机。<br/><br/> 在单个评估中，最多可以评估 35000 Vm。

[了解](concepts-assessment-calculation.md)有关评估的详细信息。



## <a name="hyper-v-host-requirements"></a>Hyper-v 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **主机部署**       | Hyper-v 主机可以是独立的，也可以部署到群集中。 |
| **权限**           | 你需要在 Hyper-v 主机上具有管理员权限。 <br/> 或者，如果不想分配管理员权限，请创建本地或域用户帐户，并将用户添加到这些组-远程管理用户、Hyper-v 管理员和性能监视器用户。 |
| **主机操作系统** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/> 不能访问运行 Windows Server 2012 的 Hyper-V 主机上的 VM。 |
| **PowerShell 远程处理**   | 必须在每个主机上启用。 |
| **Hyper-v 副本**       | 如果使用 Hyper-v 副本（或具有具有相同 VM 标识符的多个 Vm），并使用 Azure Migrate 发现原始 Vm 和复制的 Vm，则 Azure Migrate 生成的评估可能不准确。 |


## <a name="hyper-v-vm-requirements"></a>Hyper-v VM 要求

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | Azure 支持的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统。 |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必须在你评估的 vm 上运行，才能捕获操作系统信息。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用[Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 适用于 Hyper-v 的设备在 Hyper-v VM 上运行，并使用从 Azure 门户下载的压缩 Hyper-v VHD 进行部署。 

- 了解 Hyper-v 的[设备要求](migrate-appliance.md#appliance---hyper-v)。
- 了解设备需要访问的[url](migrate-appliance.md#url-access) 。

## <a name="port-access"></a>端口访问

下表汇总了评估的端口要求。

**设备** | **Connection**
--- | ---
**本** | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/> 端口44368上的入站连接，使用以下 URL 远程访问设备管理应用： ``` https://<appliance-ip-or-name>:44368 ```<br/> 端口443（HTTPS）、5671和5672（AMQP）上的出站连接，以将发现和性能元数据发送到 Azure Migrate。
**Hyper-v 主机/群集** | WinRM 端口5985（HTTP）和5986（HTTPS）上的入站连接，使用通用信息模型（CIM）会话拉取 Hyper-v Vm 的配置和性能元数据。

## <a name="agent-based-dependency-visualization"></a>基于代理的依赖项可视化

[依赖关系可视化](concepts-dependency-visualization.md)可帮助你可视化要评估和迁移的计算机之间的依赖关系。 对于基于代理的可视化效果，下表总结了要求和限制


要求 | **详细信息**
--- | ---
**部署** | 在部署依赖项可视化之前，应准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。 将 Azure Migrate 设备设置为发现本地计算机后，部署依赖关系可视化。<br/><br/> 依赖关系可视化在 Azure 政府版中不可用。
**服务地图** | 基于代理的依赖项可视化使用[Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服务映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解决方案。<br/><br/> 若要部署，请将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和西欧区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域中。<br/><br/> 添加 Azure Migrate 项目后，不能修改该工作区的工作区。
**话费** | 服务映射解决方案不会在前180天（从 Log Analytics 工作区与 Azure Migrate 项目关联的那一天）产生任何费用。<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 使用关联 Log Analytics 工作区中服务映射以外的任何解决方案将产生标准 Log Analytics 费用。<br/><br/> 如果删除 Azure Migrate 项目，则工作区不会随之一起删除。 删除项目后，服务映射不可用，将按 Log Analytics 工作区的付费层对每个节点进行收费。
**代理** | 基于代理的依赖项可视化需要在要分析的每台计算机上安装两个代理。<br/><br/> - [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [依赖关系代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**Internet 连接** | 如果计算机未连接到 internet，则需要在其上安装 Log Analytics 网关。


## <a name="next-steps"></a>后续步骤

[准备 Hyper-v VM 评估](tutorial-prepare-hyper-v.md)
