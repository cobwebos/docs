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
ms.openlocfilehash: 10f723d5298e745520c4db41b994bd2b97aaa365
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
尽管使用系统路由可以自动加快通信以方便部署，但在某些情况下，需要通过虚拟设备来控制数据包的路由。 为此，可以创建用户定义的路由来指定下一跃点，方便数据包流向特定的子网并转到虚拟设备，并可为作为虚拟设备运行的 VM 启用 IP 转发。

可以使用虚拟设备的一些用例包括：

* 使用入侵检测系统 (IDS) 监视流量
* 使用防火墙控制流量

有关 UDR 和 IP 转发的详细信息，请访问[用户定义的路由和 IP 转发](../articles/virtual-network/virtual-networks-udr-overview.md)。

