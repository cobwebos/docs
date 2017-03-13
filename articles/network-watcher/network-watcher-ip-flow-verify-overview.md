---
title: "Azure 网络观察程序中的“IP 流验证”简介 | Microsoft 文档"
description: "此页概述了网络观察程序“IP 流验证”功能"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2c25854795b6c577dff38af26543d915f8482240
ms.openlocfilehash: 0ae12529c48976c7852c7a562cb3f165c29c9d5f
ms.lasthandoff: 02/22/2017

---

# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Azure 网络观察程序中的“IP 流验证”简介

“IP 流验证”基于 5 元组信息检查是允许还是拒绝进出虚拟机的数据包。 这些信息包括方向、协议、本地 IP、远程 IP、本地端口和远程端口。 如果数据包被安全组拒绝，则返回拒绝数据包的规则的名称。 虽然可以选择任何源或目标 IP，此功能可帮助管理员快速诊断与 Internet 的连接问题以及与本地环境的连接问题。

“IP 流验证”针对虚拟机的网络接口。 然后，将基于网络接口的配置设置验证流量流。 此功能可用于确认网络安全组中的规则是否正在阻止进出虚拟机的入口或出口流量。

需要在要运行“IP 流验证”的所有区域中创建网络观察程序实例。 网络观察程序是一个区域性服务，只能针对同一区域中的资源运行。 这不会影响“IP 流验证”的结果，因为仍将返回与 NIC 关联的路由。

> [!NOTE]
> 网络观察程序目前处于预览状态，若要使用网络观察程序的功能，[需要注册该功能](network-watcher-create.md#register-the-preview-capability)。

![1][1]

## <a name="next-steps"></a>后续步骤

请访问以下文章，通过门户了解是允许还是拒绝特定虚拟机的数据包。 [通过门户使用“IP 流验证”检查是否允许 VM 上的流量](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png













