---
title: include 文件
description: include 文件
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>场景
为了更好地说明如何创建 NSG，本文档使用以下方案：

![VNet 方案](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

在此方案中，需要为 **TestVNet** 虚拟网络中的每个子网创建 NSG，如下所述： 

* **NSG-FrontEnd**。 前端 NSG 应用于 *FrontEnd* 子网，并且包含以下两个规则：    
  * **rdp-rule**。 允许将 RDP 流量传输到 *FrontEnd* 子网。
  * **web-rule**。 允许将 HTTP 流量传输到 *FrontEnd* 子网。
* **NSG-BackEnd**。 后端 NSG 应用于 *BackEnd* 子网，并且包含以下两个规则：    
  * **sql-rule**。 仅允许来自 *FrontEnd* 子网的 SQL 流量。
  * **web-rule**。 拒绝来自 *BackEnd* 子网的所有 Internet 入站流量。

将这些规则组合起来可创建一个与外围网络类似的方案，其中后端子网只能接收来自前端子网的 SQL 的传入流量且不能访问 Internet，而前端子网可以与 Internet 通信并只接收传入 HTTP 请求。

