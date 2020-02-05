---
title: Azure VMware 解决方案（AVS）-服务
description: 提供了 AVS 服务和概念的概述。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024940"
---
# <a name="avs-service-overview"></a>AVS 服务概述

使用 AVS 服务，可以通过 AVS 使用 Azure VMware 解决方案。 通过创建服务，可以购买节点、预留节点并创建 AVS 私有云。 可在每个 Azure 区域创建 avs 服务，其中 AVS 服务可用。 服务通过 AVS 定义 Azure VMware 解决方案的边缘网络。 边缘网络支持包含 VPN、ExpressRoute 和 internet 连接到 AVS 私有云的服务。

## <a name="gateway-subnet"></a>网关子网

网关子网是每个 AVS 服务所必需的，并且是创建它的区域独有的。 网关子网是在创建边缘网络时使用的，需要一个/28 个 CIDR 块。 网关子网地址空间必须是唯一的。 它不得与任何与 AVS 环境通信的网络重叠。 与 AVS 通信的网络包括本地网络和 Azure 虚拟网络。 创建网关子网后，无法将其删除。 删除服务后，会删除网关子网。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 上创建 AVS 服务](quickstart-create-cloudsimple-service.md)。
