---
title: Microsoft Azure 上的 Oracle 解决方案 | Microsoft Docs
description: 了解在 Microsoft Azure 上部署 Oracle 应用程序和解决方案的选项，包括完全在 Azure 基础结构上运行或使用与 Oracle 云基础结构 （OCI） 的跨云连接。
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: e6a2e7c646782f5ad874124e4a86809ed1d79eea
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687401"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure 上的 Oracle 应用程序和解决方案概述

本文介绍了使用 Azure 基础结构运行 Oracle 解决方案的功能。 另请参阅 Azure 应用商店中可用[Oracle VM 映像](oracle-vm-solutions.md)的详细介绍，以及将 Azure 与 Oracle[云基础结构 （OCI） 互连](oracle-oci-overview.md)的功能。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 基础结构上的 Oracle 数据库

使用 Azure 应用商店中提供的 Oracle Linux 映像上的 Oracle 数据库在 Azure 基础结构上运行 Oracle 数据库：

* Oracle 数据库 12.1、12.2 和 18.3 企业版 

* Oracle 数据库 12.1、12.2 和 18.3 标准版 

您还可以选择在 Azure 中可用的非 Oracle Linux 映像上设置 Oracle 数据库，将解决方案基于在 Azure 中从头开始创建的自定义映像，或者从本地环境上载自定义映像。

可选使用多个连接磁盘进行配置，并通过安装 Oracle 自动存储管理 （ASM） 提高数据库性能。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>甲骨文Linux和WebLogic服务器上的应用程序

在 Azure 中支持 Oracle 操作系统上运行企业应用程序。 Azure 应用商店中提供了以下映像：

* 甲骨文 WebLogic 服务器 12.1.2

* 甲骨文 Linux 与牢不可破的企业内核 （UEK） 6.8， 6.9， 6.10， 7.3， 7.4， 7.5 和 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和灾难恢复选项

* 在 Azure 基础架构上配置[Oracle 数据防护](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)、[使用 FSFO、](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)[分片](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)或[金门](https://www.oracle.com/middleware/technologies/goldengate.html)的主动数据防护，并结合[可用性区域](../../../availability-zones/az-overview.md)，在区域内提供高可用性。 您还可以跨多个 Azure 区域设置这些配置，以增加可用性和灾难恢复。

* 使用[Azure 站点恢复](../../../site-recovery/site-recovery-overview.md)来协调和管理 Azure 中的 Oracle Linux VM 以及本地或物理服务器的灾难恢复。 

* 使用[Azure VMWare 解决方案](https://docs.azure.cloudsimple.com/oracle-rac/)或[FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)在 Azure 中启用 Oracle 实际应用程序群集 （RAC）。

## <a name="backup-oracle-workloads"></a>备份 Oracle 工作负载

* 使用[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)备份 Oracle VM

* 使用 Oracle RMAN 备份 Oracle 数据库，并选择性地使用[Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)装载[高度红的 Azure Blob 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，并将 RMAN 备份写入该帐户，以增加恢复能力。

## <a name="integration-of-azure-with-oci"></a>将 Azure 与 OCI 集成

在 Azure 基础结构中运行 Oracle 应用程序，连接到 Oracle 云基础结构 （OCI） 中的后端数据库。 此解决方案使用以下功能： 

* **跨云网络**- 使用 Azure ExpressRoute 和 Oracle FastConnect 之间的直接互连在应用程序和数据库层之间建立高带宽、私有和低延迟连接。
* **集成标识**- 在 Azure AD 和 Oracle IDCS 之间设置联合标识，为解决方案创建单个标识源。 启用单一登录以跨 OCI 和 Azure 管理资源。

### <a name="deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 应用程序

使用 Terraform 模板设置 Azure 基础结构并安装 Oracle 应用程序。 

> [!IMPORTANT]
> 在 2020 年 5 月之前，Oracle 将在 Azure/Oracle 云互连解决方案中验证这些应用程序在 Azure 中运行。

* 电子商务套件
* JD Edwards EnterpriseOne
* PeopleSoft
* 甲骨文零售应用程序
* 甲骨文海龙财务管理

还在 Azure 中部署与 OCI 和其他 Azure 服务连接的自定义应用程序。

### <a name="set-up-oracle-databases-in-oci"></a>在 OCI 中设置 Oracle 数据库

将 Oracle 数据库云服务（自治数据库、RAC、Exadata、DBaaS、单节点）与在 Azure 中运行的 Oracle 应用程序结合使用。 了解有关[OCI 数据库选项](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)的详细信息。 
 

## <a name="licensing"></a>授权

在 Azure 中部署 Oracle 应用程序基于"自带许可证"模型。 假定您拥有使用 Oracle 软件的适当许可，并且您与 Oracle 有最新的支持协议。 Oracle 确保了从本地到 Azure 的许可移动性。 请参阅 Oracle-Azure[常见问题解答](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>后续步骤

* 了解有关在 Azure 基础结构中部署[Oracle VM 映像](oracle-vm-solutions.md)的详细信息。

* 详细了解如何将[Azure 与 OCI 互连](oracle-oci-overview.md)。

* 查看 2019 年 Ignite [Azure 概述会话中的 Oracle。](https://myignite.techcommunity.microsoft.com/sessions/82915) 
