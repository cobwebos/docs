---
title: include 文件
description: include 文件
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854107"
---
| 资源                                | 限制        |
|-----------------------------------------|------------------------------|
| VNet 地址前缀                   | 每个 VPN 网关600          |
| 聚合 BGP 路由                    | 每个 VPN 网关4000        |
| 本地网络网关地址前缀  | 每个本地网络网关1000               |
| S2S 连接                         | [取决于网关 SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| P2S 连接                         | [取决于网关 SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| P2S 路由限制-IKEv2                 | 对于非 Windows **/** 25 For windows，为256           |
| P2S route 限制-OpenVPN               | 1000                         |
| 最大 flows                              | 10万 VpnGw1/AZ  **/**  512k For VpnGw2-4/az|