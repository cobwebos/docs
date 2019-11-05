---
title: 什么是受保护的虚拟中心？
description: 了解安全虚拟中心
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518432"
---
# <a name="what-is-a-secured-virtual-hub"></a>什么是受保护的虚拟中心？

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

虚拟中心是一种 Microsoft 托管的虚拟网络，可实现来自其他资源的连接。 在 Azure 门户中的虚拟 WAN 上创建虚拟中心时，会将虚拟中心 VNet 和网关（可选）作为其组件创建。

*受保护*的虚拟中心是一种[Azure 虚拟 WAN 集线器](../virtual-wan/virtual-wan-about.md#resources)，其中包含 azure 防火墙管理器配置的关联安全和路由策略。 使用受保护的虚拟中心轻松创建具有本机安全服务的中心辐射型体系结构，实现流量管理和保护。 

可以使用受保护的虚拟中心作为托管中心 VNet，无本地连接。 它取代了之前 Azure 防火墙部署所需的中心 VNet。 由于受保护的虚拟中心提供自动路由，因此无需配置你自己的 Udr （用户定义的路由）来通过防火墙路由流量。

还可以使用受保护的虚拟中心作为完整虚拟 WAN 体系结构的一部分。 此体系结构提供与 Azure 建立的安全、经过优化的自动分支连接。 你可以选择用于保护和管理网络流量的服务，包括 Azure 防火墙和其他第三方安全即服务（SECaaS）提供程序。

## <a name="create-a-secured-virtual-hub"></a>创建安全的虚拟中心

使用 Azure 门户中的防火墙管理器，可以创建新的受保护虚拟中心，或使用 Azure 虚拟 WAN 转换之前创建的现有虚拟中心。

## <a name="next-steps"></a>后续步骤

若要创建受保护的虚拟中心并使用它来保护和控制中心和辐射网络，请参阅[教程：使用 Azure 防火墙管理器保护云网络使用 Azure 门户](secure-cloud-network.md)。