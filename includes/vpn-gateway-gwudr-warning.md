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
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80502020"
---
**不支持**具有 0.0.0.0/0 目标的用户定义路由和网关子网上的 NSG。 使用此配置创建的网关将被阻止创建。 网关需要访问管理控制器才能正常工作。 [BGP 路由传播](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol])应设置为网关子网上的"已启用"，以确保网关的可用性。 如果设置为禁用，网关将不起作用。
