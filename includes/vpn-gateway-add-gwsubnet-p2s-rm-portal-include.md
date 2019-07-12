---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b044912fd8f52f3f4fdbe1b3a74b64f9b565ddf0
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673253"
---
1. 在[门户](https://portal.azure.com)中，导航到要为其创建虚拟网关的 Resource Manager 虚拟网络。
2. 在 VNet 页的“设置”部分单击“子网”，展开“子网”页    。
3. 在“子网”页中，单击“+网关子网”打开“添加子网”页    。 

   ![添加网关子网](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "添加网关子网")
4. 子网的“名称”  自动填充为值“GatewaySubnet”。 Azure 需要此值才能识别作为网关子网的子网。 调整自动填充的**地址范围**值，使其匹配配置要求。 请不要配置路由表或服务终结点。

   ![添加子网](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "添加子网")
5. 单击页面底部的“确定”  以创建子网。
