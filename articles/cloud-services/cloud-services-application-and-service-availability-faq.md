---
title: Microsoft Azure 云服务应用程序和服务可用性问题的常见问题解答 | Microsoft 文档
description: 本文列出了一些关于 Microsoft Azure 云服务配应用程序和服务可用性问题的常见问题解答。
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 49576aa99f6cd505648e33348b89e502bea9d5c4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068107"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 云服务应用程序和服务可用性问题：常见问题解答 (FAQ)

本文包括关于 [Microsoft Azure 云服务](https://azure.microsoft.com/services/cloud-services)应用程序和服务可用性问题的常见问题解答。 还可以参阅[云服务 VM 大小页面](cloud-services-sizes-specs.md)，了解大小信息。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>我的角色已回收。 有没有推出适用于我的云服务的任何更新？
Microsoft 大约每月一次为 Microsoft Azure PaaS VM 发布新的来宾 OS 版本。 来宾 OS 只不过是此类更新的其中一种。 每次发布都会受许多其他因素影响。 此外，Azure 在成千上万的计算机上运行。 因此，无法预测重新启动你的角色的准确日期和时间。 我们将使用最新信息更新来宾 OS 更新 RSS 源，但你应考虑到报告时间是一个近似值。 我们知道这对于客户来说是有问题的，因此，正在致力于限制重新启动或为重新启动精确定时的计划。

有关最近来宾 OS 更新的完整详细信息，请参阅 [Azure 来宾 OS 版本和 SDK 兼容性矩阵](cloud-services-guestos-update-matrix.md)。

有关重新启动和指针的有用信息以及有关来宾和主机操作系统更新的技术详情，请阅读 MSDN 博客文章[角色实例因操作系统升级而重新启动](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)。

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>在服务空闲一段时间之后，为什么到我的云服务的第一个请求所用时间比平常更长？
当 Web 服务器收到第一个请求时，它首先重新编译代码，然后处理该请求。 这就是为什么第一个请求所用时间比其他请求更长的原因。 默认情况下，在用户不活动时，应用池将关闭。 此外，应用池还将每 1,740 分钟（29 小时）默认回收一次。

Internet Information Services (IIS) 应用程序池可定期进行回收，以避免可能导致应用程序故障、挂起或内存泄漏的不稳定状态。

以下文档将帮助你了解并缓解此问题：
* [修复 IIS 初始加载很慢问题](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [在应用池回收后，IIS 7.5 Web 应用程序第一个请求速度很慢](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

如果要更改 IIS 的默认行为，则需要使用启动任务，因为如果手动将更改应用于 Web 角色实例，更改将最终丢失。

有关详细信息，请参阅[如何配置和运行云服务的启动任务](cloud-services-startup-tasks.md)。
