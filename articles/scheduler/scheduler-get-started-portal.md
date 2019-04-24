---
title: 使用 Azure 计划程序创建计划的作业 - Azure 门户 | Microsoft Docs
description: 了解如何在 Azure 门户中使用 Azure 计划程序创建、计划和运行第一个自动化作业
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 3b2cfc932c6322df8237ec7cdf820fc4242bfa72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532012"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>使用 Azure 计划程序创建和计划第一个作业 - Azure 门户

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)将替换将即将停用的 Azure 计划程序。 若要计划作业，请[改为试用 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

本教程演示了如何轻松创建和计划作业，然后监视并管理该作业。 

如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

## <a name="create-job"></a>创建作业

1. 登录到 [Azure 门户](https://portal.azure.com/)。  

1. 在 Azure 主菜单上选择“创建资源”。 在搜索框中输入“计划程序”。 在结果列表中，选择“计划程序”，然后选择“创建”。

   ![创建计划程序资源](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   现在，创建一个将 GET 请求发送到此 URL 的作业：`https://www.microsoft.com/` 

1. 在“计划程序作业”下，输入这些信息：

   | 属性 | 示例值 | 描述 |
   |----------|---------------|-------------| 
   | **名称** | getMicrosoft | 作业的名称 | 
   | **作业集合** | <job-collection-name> | 创建一个作业集合，或选择现有的集合。 | 
   | **订阅** | <*Azure-subscription-name*> | Azure 订阅的名称 | 
   |||| 

1. 选择“操作设置 - 配置”，提供此信息，完成后选择“确定”：

   | 属性 | 示例值 | 描述 |
   |----------|---------------|-------------| 
   | **Action** | Http | 要运行的操作类型 | 
   | **方法** | Get | 要调用的方法 | 
   | **URL** | **https://www.microsoft.com** | 目标 URL | 
   |||| 
   
   ![定义作业](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. 选择“日程安排 - 配置”，定义计划，完成后选择“确定”：

   尽管可以创建一次性作业，但此示例设置的是定期计划。

   | 属性 | 示例值 | 描述 |
   |----------|---------------|-------------| 
   | **定期** | 定期 | 一次性或定期作业 | 
   | **Start on** | <今天的日期> | 作业的开始日期 | 
   | **Recur every** | 1 小时 | 执行间隔和频率 | 
   | **End** | 结束日期：从今天起两天后的日期 | 作业的结束日期 | 
   | **UTC offset** | UTC +08:00 | 协调世界时 (UTC) 与所在位置的观察时间之间的时差 | 
   |||| 

   ![定义计划](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. 准备就绪后，选择“创建”。

   创建作业后，Azure 会部署作业，该作业将显示在 Azure 仪表板上。 

1. 当 Azure 显示部署成功的通知时，请选择“固定到仪表板”。 或者，请在 Azure 工具栏上选择“通知”图标（铃铛），然后选择“固定到仪表板”。

## <a name="monitor-and-manage-jobs"></a>监视和管理作业

要查看、监视和管理工作，请在 Azure 仪表板上选择作业。 在“设置”下，可以查看和管理作业的区域如下：

![作业设置](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

有关这些区域的详细信息，请选择一个区域：

* [**属性**](#properties)
* [**操作设置**](#action-settings)
* [**计划**](#schedule)
* [**历史记录**](#history)
* [**用户**](#users)

<a name="properties"></a>

### <a name="properties"></a>属性

要查看描述作业的管理元数据的只读属性，请选择“属性”。

![查看作业属性](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>操作设置

要更改作业的高级设置，请选择“操作设置”。 

![查看操作设置](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| 操作类型 | 描述 | 
|-------------|-------------| 
| 所有类型 | 可以更改“重试策略”和“错误操作”设置。 | 
| HTTP 和 HTTPS | 可以将“方法”更改为任何允许的方法。 还可以添加、删除或更改标头及基本身份验证信息。 | 
| 存储队列| 可以更改存储帐户、队列名称、SAS 令牌和正文。 | 
| 服务总线 | 可以更改命名空间、主题或队列路径、身份验证设置、传输类型、消息属性和消息正文。 | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>计划

如果通过作业向导设置了计划，则可以更改该计划，例如开始日期和时间、定期计划以及定期作业的结束日期和时间。
可以构建更多[复杂的计划和高级的定期计划](scheduler-advanced-complexity.md)。

要更改视图，或更改作业的计划，请选择“计划”：

![查看作业计划](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>历史记录

要查看有关所选作业每次运行的指标，请选择“历史记录”。 这些指标能提供有关作业健康状况的实时值，例如状态、重试次数、发生次数、开始时间和结束时间。

![查看作业历史记录和指标](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

要查看每次运行的历史记录详细信息，例如每次运行的完整响应，请在“历史记录”下选择每次运行。 

![查看作业历史记录详细信息](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>用户

可以使用 Azure 基于角色的访问控制 (RBAC) 在粒度级别管理每个用户对 Azure 计划程序的访问。 要了解如何根据角色设置访问权限，请参阅[使用 RBAC 管理访问权限](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>后续步骤

* 了解[概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [构建复杂的计划和高级的定期计划](scheduler-advanced-complexity.md)
* 了解[计划程序的高可用性和可靠性](scheduler-high-availability-reliability.md)
* 了解[限制、配额、默认值和错误代码](scheduler-limits-defaults-errors.md)
