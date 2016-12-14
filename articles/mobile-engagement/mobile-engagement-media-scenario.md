---
title: "针对媒体应用的 Azure Mobile Engagement 实现"
description: "实现 Azure Mobile Engagement 的媒体应用方案"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1e828e4a24a1a320deaf7c0ef3a0f53ba722565b


---
# <a name="implement-mobile-engagement-with-media-app"></a>对媒体应用实现 Mobile Engagement
## <a name="overview"></a>概述
John 是一家大型媒体公司的移动项目经理。 他最近推出了一款新应用，并获得非常高的下载次数。 他已达到自己的下载数目标，但从每位用户身上获得的投资回报率 (ROI) 却未达到其要求。 

John 已经查明他的 ROI 过低的原因。 用户经常仅在 2 周之后就停止使用他的应用，而且其中大多数人永远也不再回来使用。 他想要增加应用的保留期。

在进行了一些初步测试后他发现，当他利用推送通知吸引客户的注意时，他们往往会继续使用他的这个应用。 此外，原来处于非活动状态的用户通常也会因为某些发送给他们的通知继续回来使用。 John 决定针为他的应用投资某种类型的用户参与计划，以便通过推送通知来使用高级的目标锁定功能。

John 最近阅读了 [Azure Mobile Engagement - 入门指南及最佳实践](mobile-engagement-getting-started-best-practices.md)一文，并且决定实施该指南中提出的一些建议。

## <a name="objectives-and-kpis"></a>目标和 KPI
John 与应用所涉及的关键利益干系人聚集在一起。 在用户使用他的媒体时，其中的广告便会为他们带来业务。 所以，通过增加每个用户使用的内容，John 的收入就会得到增加。 所有干系人就此达成一个主要目标：将广告所带来的销量增加 25%。 他们创建了以下业务关键绩效指标 (KPI) 来度量和推动此目标

* 每个用户点击的广告数
* （每个用户/每个会话/每周/每月...）访问过多少个文章页面
* 最喜欢的类别有哪些

John 根据与之见面的关键利益干系人的会面结果，定义了自己的业务 KPI。 他按照 [Azure Mobile Engagement - 入门指南及最佳实践](mobile-engagement-getting-started-best-practices.md)中的第 1 部分进行。 

接下来，他创建以下用户参与 KPI，以确保达到目标：

* 监视以下时间间隔内的保留期：每日、每周、每两周和每月。
* 活动用户计数
* 应用在应用商店中的评级

根据 IT 团队的建议，他增加了以下技术 KPI 来回答下列问题：

* 我的用户路径（访问过哪个页面，用户在其上花费了多少时间）如何？
* 每个会话遇到的崩溃次数或 bug 数量是多少？
* 我的用户正在运行哪些 OS 版本？
* 我的用户屏幕尺寸平均大小是多少？
* 我的用户拥有哪种 Internet 连接？

他针对每个 KPI 对所需的数据进行分类，并将其记录在操作手册的适当位置。

## <a name="engagement-program-and-integration"></a>用户参与计划和集成
至此，John 已确定了自己的 KPI，接下来他开始进行他的用户参与战略阶段，他定义了以下 4 个参与计划及对应目标：    ![][1]

然后，John 更进一步地详细说明每个计划的推送通知。 推送通知按照以下五个元素进行定义：

1. 目标：通知的目标是什么
2. 将如何达到目标
3. 对象：谁将收到通知？
4. 内容：通知的措辞和格式是什么（应用内/应用外）
5. 时间：发送此推送通知的最佳时刻是什么
   
    ![][2]

有关详细信息，请参阅[操作手册](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)。

根据该白皮书的第 2 部分，John 使用目标部分来定义他必须收集哪些数据，并联合 IT 团队共同编写其“标记计划”来实现解决方案。 在实现 1 周和用户接受度测试后，John 终于可以启动他的计划了。

## <a name="program-results"></a>计划结果
4 个月后，John 查看各项计划的绩效。 “欢迎使用计划”和“每周计划”均达到了他设置的目标。 仅具有一个会话的用户数减少，越来越多的应用功能得到了使用，而且每周的连接数已增加一倍。

**非活动客户计划**帮助 John 了解用户的喜好倾向。 由此看来，15% 的非活动用户会回来继续使用该应用。 但是，其中大部分用户保持活动状态的时间不会超过 1 个月。 John 预测在通过其他通知并扩展内容的选项后，应该能够带来的一系列潜在优化。

**发现计划**效果不佳。 虽然这增加了交叉销售量，但不足以实现他设定的目标。 John 发现自己没有足够的数据来实现相关目标和建议适当的内容。 于是，他会停止此计划，并将重点放到使用 Azure Mobile Engagement 发送“编辑推送通知”。 他下面的记者已经有一个 CMS 解决方案来发送推送通知，所以不想更换。

John 决定使用 Reach API，这是一个无需使用 AZME Web 界面即可管理市场宣传活动的 HTTP REST API。 使用此方法，John 可以收集所需的数据，并让其撰稿人继续使用 CMS 解决方案。

为了确保该功能正常运行，John 要求 IT 团队特别注意以下几点：

1. **操作系统**：它们都具有其自己的规则来管理推送通知，因此，John 决定列出所有情况，并检查 API 是否能够进行处理。
   例如：Android 推送系统允许大图片，而 iOS 不允许这种情况。
2. **期限**：John 需要一个 API，能够设置宣传活动的期限及结束时间。 他不希望用户接二连三的不停接收通知而受到打扰。
3. **类别**：营销团队为每种警报准备了一个模板。 John 要求 IT 团队在 API 内部设置类别。

进行一些测试后，John 非常满意。 多亏了这个 API，记者们仍可以使用其 CMS 发送推送通知，而且 Azure Mobile Engagement 可为其收集所有行为数据

前 4 个月过后，结果反映总体绩效良好，这让 John 及其董事会倍感自信，每个用户的 ROI 增长了 15%，移动销售量占总销售量的 17.5%，仅仅 4 个月就增长了 7.5%。

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[媒体操作手册链接]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks



<!--HONumber=Nov16_HO3-->


