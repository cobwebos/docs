---
title: include 文件
description: include 文件
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>场景

为了说明如何创建 VNet 和子网，本文档使用以下方案：

![VNet 方案](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

在此方案中，将创建名为 **TestVNet** 的 VNet，其中有保留 CIDR 块 **192.168.0.0./16**。 该 VNet 包含以下子网： 

* **FrontEnd**，使用 **192.168.1.0/24** 作为其 CIDR 块。
* **BackEnd**，使用 **192.168.2.0/24** 作为其 CIDR 块。

