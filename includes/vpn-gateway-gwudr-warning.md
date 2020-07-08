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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81274979"
---
**不支持**在 GatewaySubnet 上使用 0.0.0.0/0 Destination 和 nsg 的用户定义路由。 使用此配置创建的网关将被阻止创建。 网关需要访问管理控制器才能正常工作。 GatewaySubnet 上的 [BGP 路由传播](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol)应设置为“已启用”，以确保网关可用。 如果此项设置为“已禁用”，则网关将不起作用。
