---
title: 什么是安全虚拟中心？
description: 了解安全虚拟中心
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948062"
---
# <a name="what-is-a-secured-virtual-hub"></a>什么是安全虚拟中心？

虚拟中心是 Microsoft 托管的虚拟网络，支持来自其他资源的连接。 在 Azure 门户中基于虚拟 WAN 创建虚拟中心时，还将创建一个虚拟中心 VNet 和网关（可选）作为其组件。

安全虚拟中心是一个 [Azure 虚拟 WAN 中心](../virtual-wan/virtual-wan-about.md#resources)，其中包含通过 Azure 防火墙管理器配置的关联的安全和路由策略。 使用安全虚拟中心，可通过本机安全服务轻松创建中心辐射型可传递体系结构，实现流量管理和保护。 

可以使用受保护的虚拟中心来筛选虚拟网络之间的流量 (V2V) 、虚拟网络和分支机构 (B2V) 和流量发送到 Internet (B2I/V2I) 。 受保护的虚拟中心提供自动路由。 不需要配置自己的 Udr (用户定义的路由) 通过防火墙路由流量。

你可以选择所需的安全提供程序，以保护和管理网络流量，包括 Azure 防火墙、第三方安全作为服务 (SECaaS) 提供程序或这两者。 目前，受保护的中心不支持分支到分支 (B2B) 跨多个中心进行筛选和筛选。 若要了解详细信息，请参阅 [什么是 Azure 防火墙管理器？](overview.md#known-issues)。 

## <a name="create-a-secured-virtual-hub"></a>创建安全虚拟中心

使用 Azure 门户中的防火墙管理器，可以创建新的安全虚拟中心，也可使用 Azure 虚拟 WAN 转换之前创建的现有虚拟中心。

## <a name="next-steps"></a>后续步骤

要创建安全虚拟中心并使用它来保护和管理中心和分支网络，请参阅[教程：在 Azure 门户中使用 Azure 防火墙管理器保护云网络](secure-cloud-network.md)。