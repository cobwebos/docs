---
title: Azure VMware 解决方案 (按 CloudSimple)
description: 提供 CloudSimple 服务和概念的概述。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877665"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服务概述

CloudSimple 服务允许 CloudSimple 使用 Azure VMware 解决方案。  通过创建服务, 可以购买节点、预留节点并创建私有云。  在 CloudSimple 服务可用的每个 Azure 区域中创建 CloudSimple 服务。 该服务通过 CloudSimple 定义 Azure VMware 解决方案的边缘网络。 边缘网络支持包含 VPN、ExpressRoute 和 internet 连接到私有云的服务。

## <a name="gateway-subnet"></a>网关子网

网关子网是每个 CloudSimple 服务所必需的, 并且对其创建的区域是唯一的。 网关子网是在创建边缘网络时使用的, 需要一个/28 个 CIDR 块。  网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。  创建网关子网后, 无法将其删除。  删除服务后, 会删除网关子网。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 上创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)。
