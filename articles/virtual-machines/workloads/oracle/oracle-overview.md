---
title: Microsoft Azure 上的 Oracle 解决方案 | Microsoft Docs
description: 了解有关在 Microsoft Azure 上部署 Oracle 应用程序和解决方案的选项，包括完全在 Azure 基础结构上运行，或使用与 Oracle 云基础结构（OCI）之间的跨云连接。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: d8fed68d9b830df359f8129d55f1b9911f69e8f1
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802253"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure 上的 Oracle 应用程序和解决方案概述

本文介绍如何使用 Azure 基础结构运行 Oracle 解决方案。 另请参阅 Azure Marketplace 中可用[ORACLE VM 映像](oracle-vm-solutions.md)的详细介绍，以及用于将[Azure 与 Oracle 云基础结构互连的预览功能（OCI）](oracle-oci-overview.md)。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 基础结构上的 Oracle 数据库

使用 Azure Marketplace 中提供的 Oracle Linux 映像上的 Oracle Database 在 Azure 基础结构上运行 Oracle 数据库：

* Oracle Database 12.1、12.2 和 18.3 Enterprise Edition 

* Oracle Database 12.1、12.2 和 18.3 Standard Edition 

你还可以选择在 Azure 中提供的非 Oracle Linux 映像上安装 Oracle Database，基于你在 Azure 中从头创建的自定义映像创建解决方案，或从本地环境上传自定义映像。

（可选）通过安装 Oracle 自动存储管理（ASM）来配置多个附加磁盘并提高数据库性能。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 和 WebLogic Server 上的应用程序

在受支持的 Oracle 操作系统上运行 Azure 中的企业应用程序。 Azure Marketplace 中提供以下映像：

* Oracle WebLogic Server 12.1。2

* Oracle Linux （UEK）6.8、6.9、6.10、7.3、7.4、7.5 和7。6

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和灾难恢复选项

* 在 Azure 基础结构上，通过 FSFO、[分片](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)或[黄金入口](https://www.oracle.com/middleware/technologies/goldengate.html)配置[Oracle Data guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)、 [Active Data guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)，并结合[可用性区域](../../../availability-zones/az-overview.md)来实现在区域内实现高可用性。 你还可以在多个 Azure 区域中设置这些配置，以提高可用性和灾难恢复能力。

* 使用[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)来协调和管理 Azure 中的 Oracle Linux vm 以及本地或物理服务器的灾难恢复。 

* 使用[Azure VMWare 解决方案](https://docs.azure.cloudsimple.com/oracle-rac/)或[FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)在 Azure 中启用 Oracle 真正应用程序群集（RAC）。

## <a name="backup-oracle-workloads"></a>备份 Oracle 工作负荷

* 使用[Azure 备份](https://docs.microsoft.com/en-us/azure/backup/backup-overview)备份 Oracle vm

* 使用 Oracle RMAN 备份 Oracle Database，并选择性地使用[Azure Blob 熔断器](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-how-to-mount-container-linux)装载[高度 Redudant 的 azure blob 存储帐户](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy)，并向其写入 RMAN 备份以增加复原能力。

## <a name="integration-of-azure-with-oci-preview"></a>Azure 与 OCI 的集成（预览版）

在 Azure 基础结构中运行 Oracle 应用程序，连接到 Oracle 云基础结构中的后端数据库（OCI）。 此解决方案使用以下功能： 

* **跨云网络**-使用 Azure ExpressRoute 和 Oracle FastConnect 之间可用的直接互连，建立应用程序和数据库层之间的高带宽、专用和低延迟连接。
* **集成标识**-在 Azure AD 和 Oracle IDCS 之间设置联合标识，以便为解决方案创建单个标识源。 启用单一登录以跨 OCI 和 Azure 管理资源。

### <a name="deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 应用程序

使用 Terraform 模板设置 Azure 基础结构，并安装经过验证和支持的 Oracle 应用程序以在跨云配置中运行：

* 电子商务套件
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售应用程序
* Oracle Hyperion 金融管理

还会在 Azure 中部署与 OCI 和其他 Azure 服务连接的自定义应用程序。

### <a name="set-up-oracle-databases-in-oci"></a>在 OCI 中设置 Oracle 数据库

结合在 Azure 中运行的 Oracle 应用程序，使用 Oracle Database 云服务（自主数据库、RAC、Exadata、DBaaS、单一节点）。 了解有关[OCI 数据库选项](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)的详细信息。 
 

## <a name="licensing"></a>授权

Azure 中 Oracle 应用程序的部署基于 "自带许可" 模型。 假设您已获得使用 Oracle 软件的适当授权，并且您有与 Oracle 签订的当前支持协议。 Oracle 确保了从本地到 Azure 的许可移动性。 请参阅 Oracle-Azure[常见问题解答](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何在 Azure 基础结构中部署[ORACLE VM 映像](oracle-vm-solutions.md)。

* 详细了解如何将[Azure 与 OCI 互连](oracle-oci-overview.md)。
