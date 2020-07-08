---
title: Azure 防火墙管理器体系结构有哪些选项？
description: 使用集线器虚拟网络或受保护的虚拟中心体系结构与 Azure 防火墙管理器进行比较和对比。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7a06111fbe38f167ddf3512fdb312d7de754a738
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563577"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure 防火墙管理器体系结构有哪些选项？

Azure 防火墙管理器可为两种网络体系结构类型提供安全管理：

- **安全虚拟中心**

   [Azure 虚拟 WAN 中心](../virtual-wan/virtual-wan-about.md#resources)是一种 Microsoft 托管资源，可用于轻松创建中心辐射型体系结构。 当安全和路由策略与此类中心相关联时，这称为*[安全虚拟中心](secured-virtual-hub.md)*。 
- **中心虚拟网络**

   这是你自己创建并管理的标准 Azure 虚拟网络。 安全策略在与此类中心关联后，将称为中心虚拟网络。 目前仅支持 Azure 防火墙策略。 可将包含工作负荷服务器和服务的辐射虚拟网络对等互连。 还可以在未对等互连到任何辐射的独立虚拟网络中管理防火墙。

## <a name="comparison"></a>比较

下表比较了这两种体系结构选项，并可帮助您决定哪种方法适合您组织的安全要求：


|  |中心虚拟网络|安全虚拟中心  |
|---------|---------|---------|
|**基础资源**     |虚拟网络|虚拟 WAN 集线器|
|**中心 & 辐射**     |使用虚拟网络对等互连|使用中心虚拟网络连接自动执行|
|**本地连接**     |VPN 网关，最多支持 10 Gbps 和30个 S2S 连接;ExpressRoute|更可伸缩的 VPN 网关，具有 20 Gbps 和 1000 S2S 连接;快速路由|
|**使用 SDWAN 的自动分支连接**      |不支持|支持|
|**每个区域的集线器**     |每个区域的多个虚拟网络|每个区域一个虚拟中心。 多个中心可能具有多个虚拟 Wan|
|**Azure 防火墙–多个公共 IP 地址**      |由客户提供|自动生成|
|**Azure 防火墙可用性区域**     |支持|目前不可用|
|**使用第三方安全作为服务合作伙伴的高级 Internet 安全性**     |与所选伙伴服务建立的客户和托管 VPN 连接|通过安全合作伙伴提供商流和合作伙伴管理体验自动执行|
|**集中路由管理，以将流量路由到中心**     |客户托管的用户定义的路由|使用 BGP 支持|
|**多个安全提供程序支持**|与手动配置的、强制隧道到第三方防火墙支持|自动支持两个安全提供程序：用于专用流量筛选的 Azure 防火墙和用于 Internet 筛选的第三方|
|**应用程序网关上的 Web 应用程序防火墙** |在虚拟网络中受支持|分支网络当前支持|
|**网络虚拟设备**|在虚拟网络中受支持|分支网络当前支持|

## <a name="next-steps"></a>后续步骤

- 查看[Azure 防火墙管理器部署概述](deployment-overview.md)
- 了解[安全虚拟中心](secured-virtual-hub.md)。