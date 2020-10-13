---
title: 什么是 Azure 防火墙管理器体系结构选项？
description: 对通过 Azure 防火墙管理器使用中心虚拟网络或安全虚拟中心体系结构进行比较和对比。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563142"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>什么是 Azure 防火墙管理器体系结构选项？

Azure 防火墙管理器可为两种网络体系结构类型提供安全管理：

- **安全虚拟中心**

   [Azure 虚拟 WAN 中心](../virtual-wan/virtual-wan-about.md#resources)是一种 Microsoft 托管资源，可用于轻松创建中心辐射型体系结构。 当安全和路由策略与此类中心相关联时，它被称为[安全虚拟中心](secured-virtual-hub.md)。 
- **中心虚拟网络**

   这是你自己创建并管理的标准 Azure 虚拟网络。 安全策略在与此类中心关联后，将称为中心虚拟网络。 目前仅支持 Azure 防火墙策略。 可将包含工作负荷服务器和服务的辐射虚拟网络对等互连。 还可以在未对等互连到任何辐射的独立虚拟网络中管理防火墙。

## <a name="comparison"></a>比较

下表比较了这两种体系结构选项，可帮助你决定哪一个选项适合你组织的安全性要求：


|  |中心虚拟网络|安全虚拟中心  |
|---------|---------|---------|
|**基础资源**     |虚拟网络|虚拟 WAN 中心|
|**中心和分支**     |使用虚拟网络对等互连|自动使用中心虚拟网络连接|
|**本地连接**     |VPN 网关，最多支持 10 Gbps 和 30 个 S2S 连接；ExpressRoute|更具可缩放性的 VPN 网关，最多支持 20 Gbps 和 1000 个 S2S 连接；快速路由|
|**使用 SDWAN 进行自动分支连接**      |不支持|支持|
|**每个区域的中心数**     |每个区域有多个虚拟网络|每个区域只有一个虚拟中心。 多个中心可能有多个虚拟 WAN|
|**Azure 防火墙 – 多个公共 IP 地址**      |由客户提供|自动生成|
|**Azure 防火墙可用性区域**     |支持|目前不可用|
|**使用第三方安全即服务合作伙伴的高级 Internet 安全性**     |客户建立并管理与所选合作伙伴服务的 VPN 连接|通过安全合作伙伴提供程序流和合作伙伴管理体验自动连接|
|**集中式路由管理以将流量路由到中心**     |客户托管的用户定义的路由|支持使用 BGP|
|**多个安全提供程序支持**|支持手动配置到第三方防火墙的强制隧道|自动支持两个安全提供程序：用于专用流量筛选的 Azure 防火墙和用于 Internet 筛选的第三方|
|**应用程序网关上的 Web 应用程序防火墙** |在虚拟网络中受支持|目前在分支网络中受支持|
|**网络虚拟设备**|在虚拟网络中受支持|目前在分支网络中受支持|
|**Azure DDoS 保护标准支持**|是|否|

## <a name="next-steps"></a>后续步骤

- 查看 [Azure 防火墙管理器部署概述](deployment-overview.md)
- 了解[安全虚拟中心](secured-virtual-hub.md)。