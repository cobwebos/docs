---
title: "Azure 应用服务：缩放应用服务应用程序"
description: "了解在应用服务中缩放应用程序的复杂细节。"
keywords: "应用服务, azure 应用服务, 缩放, 可缩放, 应用服务计划, 应用服务成本"
services: app-service
documentationcenter: 
author: btardif
manager: wpickett
editor: 
ms.assetid: f403c971-4450-432b-8cea-3eeb426c0147
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec043a9e01aca2e843e50cef8b90328ba1939ea5


---
# <a name="azure-app-service-scaling-app-service-applications"></a>Azure 应用服务：缩放应用服务应用程序
在 Azure App Service 中托管的应用程序可以[达到很大的规模](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/)。
但是，缩放应用程序是一个很复杂的问题，没有“一刀切”解决方案。 若要正确地缩放应用程序，需注意 3 大领域的问题，这对于应用程序的成功缩放至关重要：

1. 了解应用程序体系结构及其弱点。
   * 应用程序是否有状态？ 是否无状态？
   * 应用程序的所有组件有哪些？
     * 应用程序的瓶颈在哪里？
   * 对应用施加负载时，哪里会首先出问题？
2. 了解预期的负载和性能要求。
   * 应用程序需要为一千个用户服务，还是为一百万个用户服务？
   * 流量来自单个地理位置还是来自全球？
   * 是否存在季节变化？是否存在流量高峰？
   * 应用的响应速度应该多快？ 1 秒？ 1 毫秒？
3. 了解并正确利用托管应用的平台。
   * 应利用哪些功能来实现缩放目标？

此部分有助于用户了解缩放过程的所有因素，通过策略充分利用必需的应用服务功能，实现缩放目标。

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]




<!--HONumber=Nov16_HO3-->


