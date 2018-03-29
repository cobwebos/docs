---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a8dec00373d991a7aeaf11f1a4d95463ab71d9a2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
1. 在[门户](http://portal.azure.com)中，导航到要为其创建虚拟网关的 Resource Manager 虚拟网络。
2. 在 VNet 页的“设置”部分单击“子网”，展开“子网”页。
3. 在“子网”页中，单击“+网关子网”打开“添加子网”页。 

  ![添加网关子网](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "添加网关子网")
4. 子网的“名称”自动填充为值“GatewaySubnet”。 Azure 需要此值才能识别作为网关子网的子网。 根据配置要求调整自动填充的“地址范围”值，然后单击页面底部的“确定”以创建该子网。

  ![添加子网](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "添加子网")