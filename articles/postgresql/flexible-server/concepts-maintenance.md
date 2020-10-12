---
title: 计划的维护-Azure Database for PostgreSQL-灵活服务器
description: 本文介绍 Azure Database for PostgreSQL 灵活的服务器中的计划内维护功能。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336303"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的计划性维护
 
Azure Database for PostgreSQL 灵活的服务器执行定期维护，以确保托管数据库的安全、稳定和最新。 在维护期间，服务器将获取新功能、更新和修补程序。
 
> [!IMPORTANT]
> Azure Database for PostgreSQL-灵活的服务器处于预览阶段
 
## <a name="selecting-a-maintenance-window"></a>选择维护时段
 
您可以在一周的特定日期和该日期的时间范围内计划维护。 或者，你可以让系统自动为你选择一个日期和时间范围。 无论采用哪种方式，系统都将在运行任何维护前提醒您5天。 系统还会让你知道启动维护的时间以及成功完成的时间。
 
有关即将发生的计划维护的通知可以是：
 
* 通过电子邮件发送到特定地址
* 通过电子邮件发送到 Azure 资源管理器角色
* 在短信中发送 (SMS) 移动设备
* 作为通知推送到 Azure 应用
* 作为语音消息传递
 
为维护计划指定首选项时，可以选择一周中的某一天，然后选择一个时间范围。 如果未指定，系统将选择服务器区域时间中的晚上 11 点到早上 7 点之间的时间。 可以为 Azure 订阅中的每个可变服务器定义不同的计划。 
 
> [!IMPORTANT]
> 通常，服务器的成功计划性维护事件间隔至少为 30 天。
>
> 但如果发生关键紧急更新（如严重漏洞），通知时间范围可能会短于五天。 即使在过去 30 天内成功地执行了计划性维护，关键更新也可能会应用于服务器。

你随时可以更新计划设置。 如果为你的灵活服务器计划了维护，并且更新了计划首选项，则当前事件将按计划继续，计划设置更改会在其成功完成后生效。 

如果系统取消维护事件或无法成功完成，系统将分别创建有关已取消或失败维护事件的通知。 下一次执行维护的尝试将按当前计划设置进行安排，你会提前收到有关5天的通知。
 
## <a name="next-steps"></a>后续步骤
 
* 了解如何 [更改维护计划](how-to-maintenance-portal.md)
* 了解如何使用 Azure 服务运行状况 [获取有关即将](../../service-health/service-notifications.md) 发生的维护的通知
* 了解如何 [设置有关即将发生的计划维护事件的警报](../../service-health/resource-health-alert-monitor-guide.md)
