---
title: Azure 上的 SAP HANA（大型实例）的基础结构和连接 | Microsoft 文档
description: 配置所需的连接基础结构，以使用 Azure 上的 SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161350"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA（大型实例）部署 

在你与 Microsoft 企业帐户团队之间达成“Azure 上的 SAP HANA（大型实例）”的采购协议后，Microsoft 会要求提供以下信息来部署 HANA 大型实例单元：

- 客户名称
- 业务联系人信息（包括电子邮件地址和电话号码）
- 技术联系人信息（包括电子邮件地址和电话号码）
- 技术网络联系人信息（包括电子邮件地址和电话号码）
- Azure 部署区域（截至 2017 年 7 月发布的区域为美国西部、美国东部、澳大利亚东部、澳大利亚东南部、西欧和北欧）
- 确认“Azure 上的 SAP HANA 配置（大型实例）”的 SKU（配置）
- 如 HANA 大型实例的概述和体系结构文档中详述，对于要部署该解决方案的每个 Azure 区域，需要提供：
    - 用于将 Azure VNet 连接到 HANA 大型实例的 ER-P2P 连接的 /29 IP 地址范围
    - 用于 HANA 大型实例服务器 IP 池的 /24 CIDR 块
- 对于连接到 HANA 大型实例的每个 Azure VNet，VNet 地址空间属性中使用的 IP 地址范围值
- 对于每个 HANA 大型实例系统的数据，需要提供：
  - 所需主机名 - 最好是使用完全限定的域名。
  - 超出服务器 IP 池地址范围的 HANA 大型实例单元的相应 IP 地址 - 请注意，服务器 IP 池地址范围中的前 30 个 IP 地址被保留，以供 HANA 大型实例内部使用
  - SAP HANA 实例的 SAP HANA SID 名称（创建必要的 SAP HANA 相关磁盘卷时需要用到）。 需要使用 HANA SID 在要附加到 HANA 大型实例单元的 NFS 卷上创建 sidadm 权限。 它还用作已装载磁盘卷的名称组件之一。 若要在单元上运行多个 HANA 实例，需要列出多个 HANA SID。 每个都会分配有一组单独的卷。
  - 为了创建所需的 SAP HANA 相关磁盘卷，需要获取 sidadm 用户在 Linux OS 中的 groupid。 SAP HANA 安装通常使用组 ID 1001 创建 sapsys 组。 sidadm 用户属于该组
  - 为了创建所需的 SAP HANA 相关磁盘卷，需要获取 sidadm 用户在 Linux OS 中的 userid。 若要在单元上运行多个 HANA 实例，需要列出所有 <sid>adm 用户 
- Azure 上的 SAP HANA（大型实例）将直接连接到的 Azure 订阅的 Azure 订阅 ID。 此订阅 ID 指代将按 HANA 大型实例单元收费的 Azure 订阅。

提供这些信息后，Microsoft 将预配 Azure 上的 SAP HANA（大型实例），并返回所需的信息，以便将 Azure VNet 链接到 HANA 大型实例并访问 HANA 大型实例单元。

在阅读本文之前，请熟悉 [HANA 大型实例常用术语](hana-know-terms.md)和 [HANA 大型实例 SKU](hana-available-skus.md)。

在 Microsoft 部署 HANA 大型实例后，可以使用以下序列连接到它：

1. [将 Azure VM 连接到 HANA 大型实例](hana-connect-azure-vm-large-instances.md)
2. [将 VNet 连接到 HANA 大型实例 ExpressRoute](hana-connect-vnet-express-route.md)
3. [其他网络要求（可选）](hana-additional-network-requirements.md)

**后续步骤**

- 参阅[将 Azure VM 连接到 HANA 大型实例](hana-connect-azure-vm-large-instances.md)。
- 参阅[将 VNet 连接到 HANA 大型实例 ExpressRoute](hana-connect-vnet-express-route.md)。