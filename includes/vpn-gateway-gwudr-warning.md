---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81274979"
---
不支持 GatewaySubnet 上具有 0.0.0.0/0 目标和 NSG 的用户定义的路由。 使用此配置创建的网关将被阻止创建。 网关需要访问管理控制器才能正常工作。 GatewaySubnet 上的 [BGP 路由传播](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol)应设置为“已启用”，以确保网关可用。 如果此项设置为“已禁用”，则网关将不起作用。
