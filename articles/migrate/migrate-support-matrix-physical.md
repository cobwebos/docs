---
title: 支持 Azure Migrate 的物理服务器评估
description: 了解支持 Azure Migrate 的物理服务器评估。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 057d384c14328deca2853e891f23250aa1d61702
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154782"
---
# <a name="support-matrix-for-physical-server-assessment"></a>物理服务器评估的支持矩阵 

你可以使用[Azure Migrate 服务](migrate-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文总结了用于评估和迁移本地物理服务器的支持设置和限制。


## <a name="overview"></a>概述

若要使用本文评估要迁移到 Azure 的本地计算机，请将 Azure Migrate： Server 评估工具添加到 Azure Migrate 项目。 部署[Azure Migrate 设备](migrate-appliance.md)。 设备持续发现本地计算机，并将配置和性能数据发送到 Azure。 计算机发现后，你可以将发现的计算机收集到组中，并对组运行评估

## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制**| 发现和评估单个[项目](migrate-support-matrix.md#azure-migrate-projects)中最多35000个物理服务器。
**项目限制** | 可以在 Azure 订阅中创建多个项目。 项目可以包括 VMware Vm、Hyper-v Vm 和物理服务器，直至达到评估限制。
**发现** | Azure Migrate 设备最多可以发现250物理服务器。
**评估** | 最多可以在一个组中添加35000台计算机。<br/><br/> 在单个评估中，最多可以评估35000台计算机。

[了解](concepts-assessment-calculation.md)有关评估的详细信息。




## <a name="physical-server-requirements"></a>物理服务器要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **物理服务器部署**       | 物理服务器可以是独立服务器，也可以部署到群集中。 |
| **权限**           | **Windows：** 在要包括在发现中的所有 Windows 服务器上设置本地用户帐户。 需要将用户帐户添加到这些组-远程桌面用户、性能监视器用户和性能日志用户。 <br/> **Linux：** 需要在要发现的 Linux 服务器上使用根帐户。 |
| **操作系统** | 支持所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统，但以下情况除外：<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用[Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 适用于物理服务器的设备可在 VM 或物理计算机上运行。 使用从 Azure 门户下载的 PowerShell 脚本来设置它。

- 了解物理服务器的[设备要求](migrate-appliance.md#appliance---physical)。
- 了解设备需要访问的[url](migrate-appliance.md#url-access) 。

## <a name="port-access"></a>端口访问

下表汇总了评估的端口要求。

**设备** | **Connection**
--- | ---
**本** | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/> 端口44368上的入站连接，使用以下 URL 远程访问设备管理应用： ``` https://<appliance-ip-or-name>:44368 ```<br/> 端口443、5671和5672上的出站连接将发现和性能元数据发送到 Azure Migrate。
**物理服务器** | **Windows：** 端口443、WinRM 端口5985（HTTP）和5986（HTTPS）上的入站连接，用于从 Windows server 拉取配置和性能元数据。 <br/> **Linux：** 端口22（UDP）上的入站连接，用于从 Linux 服务器拉取配置和性能元数据。 |

## <a name="agent-based-dependency-visualization"></a>基于代理的依赖项可视化

[依赖关系可视化](concepts-dependency-visualization.md)可帮助你可视化要评估和迁移的计算机之间的依赖关系。 对于基于代理的可视化效果，下表总结了要求和限制。


要求 | **详细信息**
--- | ---
**部署** | 在部署依赖项可视化之前，应准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。 将 Azure Migrate 设备设置为发现本地计算机后，部署依赖关系可视化。<br/><br/> 依赖关系可视化在 Azure 政府版中不可用。
**服务地图** | 基于代理的依赖项可视化使用[Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服务映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解决方案。<br/><br/> 若要部署，请将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和西欧区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域中。<br/><br/> 添加 Azure Migrate 项目后，不能修改该工作区的工作区。
**费用** | 服务映射解决方案不会在前180天（从 Log Analytics 工作区与 Azure Migrate 项目关联的那一天）产生任何费用。<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 使用关联 Log Analytics 工作区中服务映射以外的任何解决方案将产生标准 Log Analytics 费用。<br/><br/> 如果删除 Azure Migrate 项目，则工作区不会随之一起删除。 删除项目后，服务映射不可用，将按 Log Analytics 工作区的付费层对每个节点进行收费。
**代理** | 基于代理的依赖项可视化需要在要分析的每台计算机上安装两个代理。<br/><br/> - [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [依赖关系代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**Internet 连接** | 如果计算机未连接到 internet，则需要在其上安装 Log Analytics 网关。

## <a name="next-steps"></a>后续步骤

[准备物理服务器评估](tutorial-prepare-physical.md)。
