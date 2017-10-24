---
title: "游戏应用的 Azure Mobile Engagement 实现"
description: "实现 Azure Mobile Engagement 的游戏应用方案"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0ca35a3d634db8eb5c63afacba046a35b8a3e7ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>使用游戏应用实现 Mobile Engagement
## <a name="overview"></a>概述
基于角色扮演/战略游戏应用，游戏启动已启动了一种新的钓鱼技术。 该游戏已启动并运行长达 6 个月的时间。 该游戏取得了巨大的成功，其下载量达到数百万，而且与其他启动游戏应用相比，它的保留期非常高。 在季度评审会议上，利益干系人同意他们需要增加每用户平均收入 (ARPU)。 高级游戏中程序包会提供特别优惠。 使用这些游戏包，用户可以升级其钓鱼线的外观和性能，并在游戏中进行引诱或处理。 但是，程序包销量非常低。 因此，他们决定首先使用分析工具来分析客户体验，然后使用高级细分开发一个用户参与计划以提高销量。

基于 [Azure Mobile Engagement - 入门指南及最佳实践](mobile-engagement-getting-started-best-practices.md)，他们构建了一个参与策略。

## <a name="objectives-and-kpis"></a>目标和 KPI
游戏的关键利益干系人齐聚商讨。 所有干系人就此达成一个主要目标：将高级程序包的销量提高 15%。 他们创建了以下业务关键绩效指标 (KPI) 来度量和推动此目标

* 这些程序包是在游戏的哪个级别上购买的？
* 什么是每用户收入、每会话收入、每周收入和每月收入？
* 最喜欢的购买类型有哪些？

[入门指南](mobile-engagement-getting-started-best-practices.md)的第 1 部分说明如何定义目标和 KPI。 

在定义好业务 KPI 之后，移动产品经理创建了用户参与 KPI 以确定新的用户趋势和保留期。

* 监视保留期并使用以下间隔︰每日、每 2 天、每周、每月和每 3 个月
* 活动用户计数
* 应用在应用商店中的评级

根据 IT 团队的建议，他增加了以下技术 KPI 来回答下列问题：

* 我的用户路径（访问过哪个页面，用户在其上花费了多少时间）如何？
* 每个会话遇到的崩溃次数或 bug 数量
* 我的用户正在运行哪些 OS 版本？
* 我的用户屏幕尺寸平均大小是多少？
* 我的用户拥有哪种 Internet 连接？

对于每个 KPI，移动产品经理会详述她需要的数据以及数据在其操作手册中的位置。

## <a name="engagement-program-and-integration"></a>用户参与计划和集成
在构建高级用户参与计划之前，负责项目的移动项目总监应该对用户如何使用产品以及何时使用产品有深入的了解。

3 个月之后，移动项目总监已收集了足够的数据来增强其应用内推送通知的销售量。 他了解到︰

* 第一次购买通常发生在 14 级上。 那些案例中 90% 的用户购买的是 3 美元的新式传奇武器。
* 那些案例中 80% 的用户已购买过产品，他们会继续使用产品并进行更多的购买。
* 超过 20 级的用户花费开始超过每周 10 美元。
* 用户倾向于购买处于 16 级、24 级和 32 级的高级程序包。

通过这种分析，移动项目总监决定创建特定的推送通知序列来提高应用内的销售量。 他创建了三个推送序列，并将它们称为：欢迎计划、销售计划和非活动计划。 有关详细信息，请参阅[操作手册](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
