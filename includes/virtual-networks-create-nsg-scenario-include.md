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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309910"
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
