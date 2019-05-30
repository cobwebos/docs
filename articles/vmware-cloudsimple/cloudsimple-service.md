---
title: VMware 解决方案通过 CloudSimple-服务
description: 介绍 CloudSimple 服务和概念的概述。
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358103"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服务概述

CloudSimple 服务，可使用 Azure CloudSimple VMware 解决方案。  创建服务允许购买节点，保留节点，并创建私有云。  在提供 CloudSimple 服务的每个 Azure 区域中创建 CloudSimple 服务。  该服务定义 Azure VMware 解决方案通过 CloudSimple 边界网络。  边界网络支持包括 VPN、 ExpressRoute 和 internet 连接到私有云的服务。

## <a name="gateway-subnet"></a>网关子网

网关子网是每个 CloudSimple 服务必需的并仅适用于在其中创建的区域。 网关子网创建边界网络时使用，并且需要/28 CIDR 块。  网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境进行通信的网络重叠。 与 CloudSimple 通信的网络包括在本地网络和 Azure 虚拟网络。  在创建后，无法删除网关子网。  删除服务时，删除网关子网。

## <a name="next-steps"></a>后续步骤

* 了解如何[Azure 上创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)