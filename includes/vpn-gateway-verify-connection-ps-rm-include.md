---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/29/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 508685954e23a357fa5fc48b0e89c5e7daedb5c6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
可以验证连接是否成功，方法是使用“Get-AzureRmVirtualNetworkGatewayConnection”cmdlet，带或不带“-Debug”。 

1. 使用以下 cmdlet 示例，配置符合自己需要的值。 如果出现提示，请选择“A”运行“所有”。 在此示例中，“ -Name”是指要测试的连接的名称。

  ```azurepowershell-interactive
  Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. cmdlet 运行完毕后，查看该值。 在以下示例中，连接状态显示为“已连接”，且可以看到入口和出口字节数。
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```