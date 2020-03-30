---
title: Azure VMware 解决方案（按云简单 ） 服务
description: 提供云简单服务和概念的概述。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024940"
---
# <a name="cloudsimple-service-overview"></a>云简单服务概述

云简单服务允许您使用 Azure VMware 解决方案（按云简单。  通过创建服务，您可以购买节点、保留节点和创建私有云。  您可以在云简单服务可用的每个 Azure 区域创建云简单服务。 该服务通过云简单定义 Azure VMware 解决方案的边缘网络。 边缘网络支持包括 VPN、ExpressRoute 和 Internet 连接到私有云的服务。

## <a name="gateway-subnet"></a>网关子网

每个 CloudSimple 服务都需要网关子网，并且对于创建网关子网的区域是唯一的。 创建边缘网络时使用网关子网，并且需要 /28 CIDR 块。  网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。  创建网关子网后无法删除该子网。  删除服务时，将删除网关子网。

## <a name="next-steps"></a>后续步骤

* 了解如何在[Azure 上创建云简单服务](quickstart-create-cloudsimple-service.md)。
