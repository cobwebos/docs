---
title: Microsoft Azure 上的 Oracle 解决方案 | Microsoft Docs
description: 了解用于 Oracle 应用程序和 Microsoft Azure，包括完全在 Azure 基础结构上运行或使用跨云连接 Oracle 云基础结构 (OCI) 上的解决方案部署选项。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b8bfa0dfa82f73cad010a608150eac48c7f3d4c8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707452"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Oracle 应用程序和 Azure 上的解决方案概述

本文介绍了功能可用于运行 Oracle 解决方案使用 Azure 基础结构。 另请参阅详细的介绍可用[Oracle VM 映像](oracle-vm-solutions.md)在 Azure Marketplace 和的预览功能[互连 Azure Oracle 云基础结构 (OCI)](oracle-oci-overview.md)。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 基础结构上的 oracle 数据库

在 Azure Marketplace 中使用 Linux 映像可用的 Azure 基础结构上运行 Oracle 数据库：

* Oracle 数据库 12.1、 12.2 和 18.3 企业版 

* Oracle 数据库 12.1、 12.2 和 18.3 标准版 

您还可以选择一种解决方案的基础自定义映像在 Azure 中从头开始创建或从你的本地环境上传自定义映像。

（可选） 配置多个附加的磁盘，并通过安装 Oracle 自动存储管理 (ASM) 来提高数据库性能。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>在 Oracle Linux 和 WebLogic Server 上的应用程序

在 Azure 中运行企业应用程序支持 Oracle 的操作系统上。 Azure Marketplace 中提供了以下映像：

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8、 6.9、 6.10、 7.3、 7.4、 7.5 和 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和灾难恢复选项

* 结合 Azure 基础结构上配置 Oracle 数据防护、 活动数据防护或 GoldenGate[可用性区域](../../../availability-zones/az-overview.md)以实现高可用性。

* 使用[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)来安排和管理 Azure 和你的本地或物理服务器中的 Oracle Linux Vm 的灾难恢复。 

* 在 Azure 中使用启用 Oracle Real Application Clusters (RAC) [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="integration-of-azure-with-oci-preview"></a>Azure 中使用 OCI （预览版） 的集成

在 Azure 基础结构，连接到后端数据库在 Oracle 云基础结构 (OCI) 中运行 Oracle 应用程序。 此解决方案使用以下功能： 

* **跨云网络**-使用直接互连 Azure ExpressRoute 和 Oracle FastConnect 建立应用程序和数据库层之间的高带宽、 专用和低延迟连接之间可用。
* **集成标识**-设置 Azure AD 之间的联合身份标识和 Oracle IDC，若要创建解决方案的单一标识源。 启用单一登录以管理 OCI 和 Azure 间的资源。

### <a name="deploy-oracle-applications-on-azure"></a>部署 Azure 上的 Oracle 应用程序

使用 Terraform 模板设置 Azure 基础结构和安装 Oracle 应用程序验证并支持在跨云配置中运行：

* 电子商务套件
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售应用程序
* Oracle Hyperion 财务管理

此外将部署在 Azure 中的自定义应用程序使用 OCI 和其他 Azure 服务连接。

### <a name="set-up-oracle-databases-in-oci"></a>设置 Oracle OCI 中的数据库

以 Oracle 在 Azure 中运行的应用程序结合使用 Oracle 数据库云服务 （自治数据库，RAC、 Exadata、 DBaaS，单个节点）。 详细了解如何[OCI 数据库选项](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)。 
 

## <a name="licensing"></a>许可

在 Azure 中的 Oracle 应用程序的部署取决于"自带许可"模型。 假定您已获得适当使用 Oracle 软件和在具有 Oracle 位置拥有最新支持协议。 Oracle 确保了从本地到 Azure 的许可移动性。 请参阅 Oracle Azure[常见问题解答](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何部署[Oracle VM 映像](oracle-vm-solutions.md)Azure 基础结构中。

* 了解如何[互连 Azure 中的使用 OCI](oracle-oci-overview.md)。