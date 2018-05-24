---
title: Azure 网络观察程序中的“IP 流验证”简介 | Microsoft 文档
description: 此页概述了网络观察程序“IP 流验证”功能
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8a59047a586f3d7ad7c1f29b218396bd688caafd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181593"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Azure 网络观察程序中的“IP 流验证”简介

“IP 流验证”检查是允许还是拒绝进出虚拟机的数据包。 这些信息包括方向、协议、本地 IP、远程 IP、本地端口和远程端口。 如果数据包被安全组拒绝，则返回拒绝数据包的规则的名称。 虽然可以选择任何源或目标 IP，“IP 流验证”功能可帮助管理员快速诊断与 Internet 的连接问题，以及与本地环境的连接问题。

“IP 流验证”针对虚拟机的网络接口。 然后，将基于网络接口的配置设置验证流量流。 “IP 流验证”功能可用于确认网络安全组中的规则是否正在阻止进出虚拟机的入口或出口流量。

需要在要运行“IP 流验证”的所有区域中创建网络观察程序实例。 网络观察程序是一个区域性服务，只能针对同一区域中的资源运行。 所使用的实例不会影响“IP 流验证”的结果，因为仍将返回与 NIC 关联的路由。

![1][1]

## <a name="next-steps"></a>后续步骤

请访问以下文章，通过门户了解是允许还是拒绝特定虚拟机的数据包。 [通过门户使用“IP 流验证”检查是否允许 VM 上的流量](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png












