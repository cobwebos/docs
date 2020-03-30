---
title: 什么是 Azure 防火墙管理器体系结构选项？
description: 使用 Azure 防火墙管理器比较和对比使用中心虚拟网络或安全虚拟中心体系结构。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444571"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>什么是 Azure 防火墙管理器体系结构选项？

Azure 防火墙管理器可以为两种网络体系结构类型提供安全管理：

- **安全虚拟中心**

   [Azure 虚拟 WAN 中心](../virtual-wan/virtual-wan-about.md#resources)是一种 Microsoft 托管资源，可用于轻松创建中心辐射型体系结构。 当安全和路由策略与此类中心关联时，它称为*[安全虚拟中心](secured-virtual-hub.md)*。 
- **中心虚拟网络**

   这是你自己创建并管理的标准 Azure 虚拟网络。 安全策略在与此类中心关联后，将称为中心虚拟网络。** 目前仅支持 Azure 防火墙策略。 可将包含工作负荷服务器和服务的辐射虚拟网络对等互连。 还可以在未对等互连到任何辐射的独立虚拟网络中管理防火墙。

## <a name="comparison"></a>比较

下表比较了这两个体系结构选项，并可以帮助您确定哪个选项适合组织的安全要求：


|  |**中心虚拟网络**|**安全虚拟中心**  |
|---------|---------|---------|
|**基础资源**     |虚拟网络|虚拟广域网中心|
|**中心&分支**     |使用虚拟网络对等互连|使用集线器虚拟网络连接实现自动化|
|**上置连接**     |VPN 网关高达 10 Gbps 和 30 S2S 连接;快速路线|更可扩展的 VPN 网关高达 20 Gbps 和 1000 S2S 连接;快速路线|
|**使用 SDWAN 实现自动分支连接**      |不支持|支持|
|**每个区域的枢纽**     |每个区域有多个虚拟网络|每个区域的单个虚拟中心。 多个具有多个虚拟 WAN 的集线器|
|**Azure 防火墙 = 多个公共 IP 地址**      |由客户提供|自动生成。 由 GA 提供。|
|**Azure 防火墙可用性区域**     |支持|预览版不可用。 由 GA 提供|
|**以第三方安全作为服务合作伙伴的高级互联网安全性**     |客户建立和托管 VPN 连接，以合作伙伴服务为首选|通过受信任的安全合作伙伴流程和合作伙伴管理经验实现自动化|
|**集中路由管理，将流量路由到枢纽**     |客户管理的用户定义路由|支持使用 BGP|
|**应用程序网关上的 Web 应用程序防火墙** |虚拟网络中受支持|目前在分支网络中支持|
|**网络虚拟设备**|虚拟网络中受支持|目前在分支网络中支持|

## <a name="next-steps"></a>后续步骤

- 回顾 [Azure 防火墙管理器预览版部署概述](deployment-overview.md)
- 了解[安全虚拟中心](secured-virtual-hub.md)。