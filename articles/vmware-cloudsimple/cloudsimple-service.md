---
title: VMware CloudSimple 服务解决方案
description: 介绍 CloudSimple 服务和概念的概述。
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595314"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服务概述

使用 CloudSimple 服务时，可以使用 Azure CloudSimple VMware 解决方案。 创建服务后，可以预配节点、 保留节点，并创建私有云。 在提供 CloudSimple 服务的每个 Azure 区域中创建 CloudSimple 服务。 该服务定义 Azure VMware 解决方案通过 CloudSimple 边界网络。 边界网络支持包括 VPN、 Azure ExpressRoute 和 internet 连接到私有云的服务。

## <a name="gateway-subnet"></a>网关子网

网关子网是每个 CloudSimple 服务必需的并仅适用于在其中创建的区域。 网关子网创建边界网络时使用，并且需要/28 CIDR 块。 网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境进行通信的网络重叠。 与 CloudSimple 通信的网络包括在本地网络和 Azure 虚拟网络。 在创建后，无法删除网关子网。 删除服务时，删除网关子网。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 上创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)。
