---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780156"
---
网关子网包含虚拟网络网关服务使用的保留 IP 地址。 创建网关子网。

1. 在门户中，导航到要为其创建虚拟网关的虚拟网络。
2. 在“虚拟网络”页上，单击“子网”以展开“VNet1 - 子网”页。
3. 单击顶部的“+网关子网”打开“添加子网”页。

   ![添加网关子网](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "添加网关子网")
4. 子网的“名称”会自动填充为所需值“GatewaySubnet”。 调整自动填充的**地址范围 (CIDR 块)** 以匹配以下值:

   **地址范围(CIDR 块)** ：10.1.255.0/27

   ![添加网关子网](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "添加网关子网")
5. 将其余设置保留为默认值 "**无**" 或**选择 "0**"。 然后单击 **"确定"** 创建网关子网。