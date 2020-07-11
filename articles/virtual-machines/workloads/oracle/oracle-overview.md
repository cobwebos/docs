---
title: Microsoft Azure 上的 Oracle 解决方案 | Microsoft Docs
description: 了解有关在 Microsoft Azure 上部署 Oracle 应用程序和解决方案的选项，包括完全在 Azure 基础结构上运行，或与 Oracle 云基础结构 (OCI) 跨云连接。
services: virtual-machines-linux
documentationcenter: ''
author: rgardler
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: rogardle
ms.openlocfilehash: e9a61c631e8a74bbbd58bb991848fcf60f81ff6a
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224428"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure 上的 Oracle 应用程序和解决方案概述

本文介绍使用 Azure 基础结构运行 Oracle 解决方案的功能。 另请参阅 Azure 市场中的可用 [WebLogic Server Azure 应用程序](oracle-weblogic.md)、[Oracle VM 映像](oracle-vm-solutions.md)的详细介绍，以及 [Azure 与 Oracle 云基础结构 (OCI) 互连](oracle-oci-overview.md)的功能。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 基础结构上的 Oracle 数据库

使用 Azure 市场中提供的 Oracle Linux 映像上的 Oracle 数据库在 Azure 基础结构上运行 Oracle 数据库：

* Oracle Database 12.1、12.2 和 18.3 Enterprise Edition 

* Oracle Database 12.1、12.2 和 18.3 Standard Edition

* Oracle Database 19。3

你还可以选择在 Azure 中提供的非 Oracle Linux 映像上安装 Oracle Database，解决方案要基于你在 Azure 中从头创建的自定义映像解决方案，或从本地环境上传自定义映像。

（可选）通过安装 Oracle 自动存储管理 (ASM) 来配置多个附加磁盘并提高数据库性能。

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic Server 与 Azure 服务集成

从各种 WebLogic Server Azure 应用程序中进行选择，以加快云旅程。  提供了几个预配置的 Azure 服务集成，其中包括数据库、Azure 应用程序网关和 Azure Active Directory。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 和 WebLogic Server 上的应用程序

在 Azure 上运行支持 Oracle Linux 映像的企业应用程序。 Azure 市场中提供以下虚拟机映像：

* Oracle WebLogic Server 12.1.2

* 使用 Unbreakable Enterprise 内核 (UEK) 6.8、6.9、6.10、7.3 到7.7、8.0、8.1 Oracle Linux。 

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和灾难恢复选项

* 将 Azure 基础结构上的 [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)、[使用 FSFO 激活 Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)、[分片](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)或[Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) 配置为与[可用性区域](../../../availability-zones/az-overview.md)结合，以获得区域内高可用性。 你还可以跨多个 Azure 区域设置这些配置，以提高可用性和灾难恢复能力。

* 使用[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)来协调和管理 Azure 中的 Oracle Linux vm 和物理服务器的灾难恢复。 

* 使用[Azure VMware 解决方案](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/)或[FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)在 azure 中 (RAC) 启用 Oracle 真实应用程序群集。

## <a name="backup-oracle-workloads"></a>备份 Oracle 工作负载

* 使用 [Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)来备份 Oracle VM

* 使用 Oracle RMAN 备份 Oracle Database，并选择性地使用 [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)，以装载[高度冗余的 Azure Blob 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，并将 RMAN 备份写入其中以提高复原能力。

## <a name="integration-of-azure-with-oci"></a>Azure 与 OCI 集成

在 Azure 基础结构中运行 Oracle 应用程序，连接到 Oracle 云基础结构 (OCI) 中的后端数据库。 此解决方案使用以下功能： 

* 跨云网络 - 使用 Azure ExpressRoute 和 Oracle FastConnect 之间可用的直接互连在应用程序和数据库层之间建立高带宽、专用和低延迟连接。
* 集成身份 - 在 Azure AD 和 Oracle IDCS 之间设置联合身份，为解决方案创建单个标识源。 启用单一登录以跨 OCI 和 Azure 管理资源。

### <a name="deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 应用程序

使用 Terraform 模板设置 Azure 基础结构并安装 Oracle 应用程序。 

当通过 Azure/Oracle 云互连解决方案连接到 Oracle 数据库时，oracle 已通过认证，可在 Azure 中运行这些应用程序：

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail 应用程序
* Oracle Hyperion 财务管理

还会在 Azure 中部署与 OCI 和其他 Azure 服务连接的自定义应用程序。

### <a name="set-up-oracle-databases-in-oci"></a>在 OCI 中设置 Oracle 数据库

使用 Oracle Database 云服务 (自治数据库、RAC、Exadata、DBaaS、单一节点) 与 Azure 中运行的 Oracle 软件结合使用。 详细了解 [OCI 数据库选项](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)。 
 

## <a name="licensing"></a>授权

在 Azure 中部署 Oracle 应用程序基于“自带许可”模型。 假定已获得使用 Oracle 软件的相关许可，且拥有 Oracle 最新支持协议。 Oracle 确保了从本地到 Azure 的许可移动性。 请参阅 Oracle-Azure [常见问题解答](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [WebLogic Server Azure 应用程序](oracle-weblogic.md)及其支持的 Azure 服务集成。

* 详细了解如何在 Azure 基础结构中部署 [Oracle VM 映像](oracle-vm-solutions.md)。

* 详细了解如何[将 Azure 与 OCI 互连](oracle-oci-overview.md)。

* 查看 Ignite 2019 中的 [“Azure 上的 Oracle”概述课程](https://myignite.techcommunity.microsoft.com/sessions/82915)。 
