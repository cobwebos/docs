---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150275"
---
1. 在[Azure 门户](http://portal.azure.com)中，选择要为其创建虚拟网关的资源管理器虚拟网络。

2. 在虚拟网络页的“设置”部分中，选择“子网”展开“子网”页    。

3. 在“子网”页中，选择“网关子网”打开“添加子网”页    。

   ![添加网关子网](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "添加网关子网")

4. 子网的“名称”自动填充为值“GatewaySubnet”   。 Azure 需要此值才能识别作为网关子网的子网。 根据配置要求调整自动填充的“地址范围”值，然后选择“确定”以创建该子网   。

   ![添加子网](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "添加子网")