---
title: 应用程序代理应用程序加载耗时过长 | Microsoft Docs
description: 对 Azure AD 应用程序代理的页面加载性能问题进行故障排除
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 1c32015cb5addcb693c1f7b746f26a6373979c14
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589927"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>应用程序代理应用程序加载耗时过长

本文可帮助了解 Azure AD 应用程序代理应用程序加载耗时过长的原因。 另外，还介绍了可以执行哪些操作来解决此问题。

## <a name="overview"></a>概述
尽管你的应用程序正在运行，也可能会发生长时间延迟。 你可能需要进行网络拓扑调整以提高速度。 有关不同拓扑的评估，请参阅[网络注意事项文档](manage-apps/application-proxy-network-topology.md)。

除了网络拓扑之外，目前还没有关于性能调整的更多建议。 随着应用程序代理服务的扩展，可能会有物理上更为接近的数据中心。 距离上的接近可能有助于缓解延迟。 有关 Azure 数据中心列表，请参阅[延迟测试页](http://www.azurespeed.com/Azure/Latency)。 

具有应用程序代理服务的数据中心可以在[连接器端口测试工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)上找到。 

## <a name="feedback-on-application-proxy-data-center-locations"></a>有关应用程序代理数据中心位置的反馈 
可能有的 Azure 数据中心尚未包含应用程序代理，但会为你带来很大的延迟改进。 将数据中心位置发送到 aadapfeedback@microsoft.com。 Microsoft 将根据你的反馈来扩展计划。

Microsoft 正在致力研究改进延迟的附加功能。 这些改进一旦推出，就会及时更新文档。

## <a name="next-steps"></a>后续步骤
[使用现有的本地代理服务器](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
