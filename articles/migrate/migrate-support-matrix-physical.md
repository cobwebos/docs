---
title: 支持 Azure Migrate 的物理服务器评估
description: 了解支持 Azure Migrate 的物理服务器评估。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028774"
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


## <a name="next-steps"></a>后续步骤

[准备物理服务器评估](tutorial-prepare-physical.md)。
