---
title: Azure Migrate 中的 VMware 评估支持
description: 了解 Azure Migrate 中的 VMware 评估支持。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2fab94b66e09d3923e481326b3650f1beb621dc4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048762"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 评估的支持矩阵 

本文汇总了对 VMware Vm 进行[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-migration-tool)的评估的支持设置和限制。 如果正在寻找有关将 VMware Vm 迁移到 Azure 的信息，请参阅[迁移支持矩阵](migrate-support-matrix-vmware-migration.md)。

## <a name="overview"></a>概述

若要使用本文评估要迁移到 Azure 的本地计算机，请将 Azure Migrate： Server 评估工具添加到 Azure Migrate 项目。 部署[Azure Migrate 设备](migrate-appliance.md)。 设备持续发现本地计算机，并将配置和性能数据发送到 Azure。 计算机发现后，你可以将发现的计算机收集到组中，并对组运行评估。


## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制**| 在单个[项目](migrate-support-matrix.md#azure-migrate-projects)中发现并评估多达35000的 VMware vm。
**项目限制** | 可以在 Azure 订阅中创建多个项目。 项目可以包括 VMware Vm、Hyper-v Vm 和物理服务器，直至达到评估限制。
**发现** | Azure Migrate 设备在 vCenter Server 上最多可以发现 10000 VMware Vm。
**评估** | 最多可以在一个组中添加35000台计算机。<br/><br/> 在单个评估中，最多可以评估 35000 Vm。

[了解](concepts-assessment-calculation.md)有关评估的详细信息。


## <a name="application-discovery"></a>应用程序发现

除了发现计算机外，Azure Migrate：服务器评估可以发现计算机上运行的应用、角色和功能。 发现您的应用程序清单后，您可以确定和规划为本地工作负荷定制的迁移路径。 

**支持** | **详细信息**
--- | ---
**发现** | 发现无代理，使用计算机来宾凭据，并使用 WMI 和 SSH 调用远程访问计算机。
**支持的计算机** | 本地 VMware Vm。
**计算机操作系统** | 所有 Windows 和 Linux 版本。
**vCenter 凭据** | 具有只读访问权限的 vCenter Server 帐户，以及为虚拟机启用 > 来宾操作的特权。
**VM 凭据** | 目前支持所有 Windows 服务器使用一个凭据，并支持为所有 Linux 服务器使用一个凭据。<br/><br/> 为 Windows Vm 创建来宾用户帐户，为所有 Linux Vm 创建常规/普通用户帐户（非 sudo 访问权限）。
**VMware 工具** | 必须在要发现的 Vm 上安装并运行 VMware 工具。
**端口访问** | 在运行要发现的 Vm 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口443。
**限制** | 对于应用发现，最多可以发现每个设备10000。 

## <a name="vmware-requirements"></a>VMware 要求

**VMware** | **详细信息**
--- | ---
**vCenter Server** | 要发现和评估的计算机必须由 vCenter Server 5.5、6.0、6.5 或6.7 版本来管理。
**权限（评估）** | vCenter Server 只读帐户。
**权限（应用程序发现）** | vCenter Server 具有只读访问权限的帐户以及为虚拟机启用的特权 **> 来宾操作**。
**权限（依赖项可视化）** | 中心服务器帐户具有只读访问权限，并且为**虚拟机**启用了特权 > **来宾操作**。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用[Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 适用于 VMware 的设备使用 .OVA 模板进行部署，该模板导入到 vCenter Server 中。 

- 了解 VMware 的[设备要求](migrate-appliance.md#appliance---vmware)。
- 了解设备需要访问的[url](migrate-appliance.md#url-access) 。

## <a name="port-access"></a>端口访问

**设备** | **Connection**
--- | ---
本 | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/><br/> 端口44368上的入站连接，使用以下 URL 远程访问设备管理应用： ```https://<appliance-ip-or-name>:44368``` <br/><br/>端口443（HTTPS）、5671和5672（AMQP）上的出站连接，以将发现和性能元数据发送到 Azure Migrate。
vCenter 服务器 | TCP 端口443上的入站连接，使设备能够收集配置和性能元数据以进行评估。 <br/><br/> 默认情况下，设备会在端口443上连接到 vCenter。 如果 vCenter 服务器侦听其他端口，则可以在设置发现时修改端口。
ESXi 主机 | **仅[应用程序发现](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery)和[无代理依赖项可视化项](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)是必需的** <br/><br/> 设备连接到 TCP 端口443上的 ESXi 主机，以发现应用程序，并在主机上运行的 Vm 上运行无代理依赖项可视化。

## <a name="agent-based-dependency-visualization"></a>基于代理的依赖项可视化

[依赖关系可视化](concepts-dependency-visualization.md)可帮助你可视化要评估和迁移的计算机之间的依赖关系。 对于基于代理的可视化效果，下表总结了要求和限制


**要求** | **详细信息**
--- | ---
**部署** | 在部署依赖项可视化之前，应准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。 将 Azure Migrate 设备设置为发现本地计算机后，部署依赖关系可视化。<br/><br/> 依赖关系可视化在 Azure 政府版中不可用。
**服务地图** | 基于代理的依赖项可视化使用[Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服务映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解决方案。<br/><br/> 若要部署，请将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和西欧区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的区域中。<br/><br/> 添加 Azure Migrate 项目后，不能修改该工作区的工作区。
**话费** | 服务映射解决方案不会在前180天（从 Log Analytics 工作区与 Azure Migrate 项目关联的那一天）产生任何费用。<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 使用关联 Log Analytics 工作区中服务映射以外的任何解决方案将产生标准 Log Analytics 费用。<br/><br/> 如果删除 Azure Migrate 项目，则工作区不会随之一起删除。 删除项目后，服务映射不可用，将按 Log Analytics 工作区的付费层对每个节点进行收费。
**代理** | 基于代理的依赖项可视化需要在要分析的每台计算机上安装两个代理。<br/><br/> - [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [依赖关系代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**Internet 连接** | 如果计算机未连接到 internet，则需要在其上安装 Log Analytics 网关。


## <a name="agentless-dependency-visualization"></a>无代理依赖项可视化

此选项当前处于预览状态。 [了解详细信息](how-to-create-group-machine-dependencies-agentless.md)。 下表总结了要求。

**要求** | **详细信息**
--- | ---
**部署** | 在部署依赖项可视化之前，应准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。 将 Azure Migrate 设备设置为发现本地计算机后，部署依赖关系可视化。
**VM 支持** | 目前仅支持 VMware Vm。
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2（64 位）
**Linux VM** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。
**Windows 帐户** |  可视化需要本地或域管理员帐户。
**Linux 帐户** | 可视化需要具有 Root 权限的用户帐户。<br/><br/> 或者，用户帐户需要对/bin/netstat 和/bin/ls 文件的这些权限： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。
**VM 代理** | Vm 无需代理。
**VMware 工具** | 必须在要分析的 Vm 上安装并运行 VMware 工具。
**vCenter 凭据** | 具有只读访问权限的 vCenter Server 帐户，以及为虚拟机启用 > 来宾操作的特权。
**端口访问** | 在运行要分析的 Vm 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口443。



## <a name="next-steps"></a>后续步骤

- [查看](best-practices-assessment.md)创建评估的最佳实践。
- [准备 VMware](tutorial-prepare-vmware.md)评估。
