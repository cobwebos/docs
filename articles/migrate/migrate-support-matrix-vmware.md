---
title: Azure Migrate 中的 VMware 评估支持
description: 了解 Azure Migrate 中的 VMware 评估支持。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029004"
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
发现 | 发现无代理，使用计算机来宾凭据，并使用 WMI 和 SSH 调用远程访问计算机。
支持的计算机 | 本地 VMware Vm。
计算机操作系统 | 所有 Windows 和 Linux 版本
凭据 | 目前支持所有 Windows 服务器使用一个凭据，并支持为所有 Linux 服务器使用一个凭据。<br/><br/> 为 Windows Vm 创建来宾用户帐户，为所有 Linux Vm 创建常规/普通用户帐户（非 sudo 访问权限）。
限制 | 对于应用发现，最多可以发现每个设备10000。 

## <a name="vmware-requirements"></a>VMware 要求

**VMware** | **详细信息**
--- | ---
**vCenter Server** | 要发现和评估的计算机必须由 vCenter Server 5.5、6.0、6.5 或6.7 版本来管理。
**权限（评估）** | vCenter Server 只读帐户。
**权限（应用程序发现）** | vCenter Server 具有只读访问权限的帐户以及为虚拟机启用的特权 > 来宾操作。
**权限（依赖项可视化）** | 中心服务器帐户具有只读访问权限，并且为**虚拟机**启用了特权 > **来宾操作**。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用[Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 适用于 VMware 的设备使用 .OVA 模板进行部署，该模板导入到 vCenter Server 中。 

- 了解 VMware 的[设备要求](migrate-appliance.md#appliance---vmware)。
- 了解设备需要访问的[url](migrate-appliance.md#url-access) 。

## <a name="port-access"></a>端口访问

**设备** | **Connection**
--- | ---
家用电器 | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/><br/> 端口44368上的入站连接，使用以下 URL 远程访问设备管理应用： ```https://<appliance-ip-or-name>:44368``` <br/><br/>端口443、5671和5672上的出站连接将发现和性能元数据发送到 Azure Migrate。
vCenter 服务器 | TCP 端口443上的入站连接，使设备能够收集配置和性能元数据以进行评估。 <br/><br/> 默认情况下，设备会在端口443上连接到 vCenter。 如果 vCenter 服务器侦听其他端口，则可以在设置发现时修改端口。

## <a name="dependency-visualization"></a>依赖项可视化

依赖关系可视化可帮助你可视化要评估和迁移的计算机之间的依赖关系。 当您想要评估具有更高置信度的计算机时，通常使用依赖关系映射。 对于 VMware Vm，支持依赖项可视化，如下所示：

- **无代理依赖项可视化**：此选项当前为预览版。 不需要在计算机上安装任何代理。
    - 它的工作原理是从启用了它的计算机捕获 TCP 连接数据。 启动依赖项发现后，设备会按5分钟的轮询间隔从计算机收集数据。
    - 收集以下数据：
        - TCP 连接
        - 具有活动连接的进程的名称
        - 运行上述进程的已安装应用程序的名称
        - 不。 每个轮询间隔检测到的连接
- **基于代理的依赖项可视化**：若要使用基于代理的依赖项可视化，需要在要分析的每台本地计算机上下载并安装以下代理。
    - 在每台计算机上安装 Microsoft Monitoring agent （MMA）。 [了解](how-to-create-group-machine-dependencies.md#install-the-mma)有关如何安装 MMA 代理的详细信息。
    - 在每台计算机上安装依赖关系代理。 [了解](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)有关如何安装依赖关系代理的详细信息。
    - 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 Log Analytics 网关。


## <a name="next-steps"></a>后续步骤

- [查看](best-practices-assessment.md)创建评估的最佳实践。
- [准备 VMware](tutorial-prepare-vmware.md)评估。
