---
title: Azure Migrate 中的 Hyper-v 评估支持
description: 了解支持 Azure Migrate 的 Hyper-v 评估。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028783"
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
**本** | TCP 端口3389上的入站连接，允许到设备的远程桌面连接。<br/> 端口44368上的入站连接，使用以下 URL 远程访问设备管理应用： ``` https://<appliance-ip-or-name>:44368 ```<br/> 端口443、5671和5672上的出站连接将发现和性能元数据发送到 Azure Migrate。
**Hyper-v 主机/群集** | WinRM 端口5985（HTTP）和5986（HTTPS）上的入站连接，使用通用信息模型（CIM）会话拉取 Hyper-v Vm 的配置和性能元数据。



## <a name="next-steps"></a>后续步骤

[准备 Hyper-v VM 评估](tutorial-prepare-hyper-v.md)
