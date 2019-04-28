---
title: include 文件
description: include 文件
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597056"
---
## <a name="scenario"></a>场景

为了说明如何创建 VNet 和子网，本文档使用以下方案：

![VNet 方案](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

在此方案中，将创建名为 **TestVNet** 的 VNet，其中有保留 CIDR 块 **192.168.0.0./16**。 该 VNet 包含以下子网： 

* **FrontEnd**，使用 **192.168.1.0/24** 作为其 CIDR 块。
* **BackEnd**，使用 **192.168.2.0/24** 作为其 CIDR 块。