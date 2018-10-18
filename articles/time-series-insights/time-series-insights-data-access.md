---
title: 配置安全性以访问和管理 Azure 时序见解 | Microsoft Docs
description: 本文介绍如何将安全性和权限配置为管理访问策略和数据访问策略以保护 Azure 时序见解。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423368"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>使用 Azure 门户授予对时序见解环境的数据访问权限

本文讨论了两种类型的时序见解访问策略。

## <a name="video"></a>视频： 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>在本视频中，我们将介绍如何创建和管理时序见解中的访问策略。 </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

Time Series Insights 环境有两个独立的访问策略类型：

* 管理访问策略
* 数据访问策略

两类策略都授予 Azure Active Directory 主体（用户和应用）对特定环境的各种权限。 主体（用户和应用）必须属于与包含环境的订阅关联的 Active Directory（称为 Azure 租户）。

管理访问策略授予的权限与环境配置相关，例如
*   创建和删除环境、事件源、引用数据集，以及
*   管理数据访问策略。

数据访问策略授予的权限适用于：发出数据查询、操作环境中的引用数据，以及共享已保存的与环境关联的查询和透视。

两类策略都可以清楚地区分环境管理访问权限和环境中数据的访问权限。 例如，可以对环境进行设置，从数据访问权限中删除环境的所有者/创建者。 此外，用户和服务可以读取环境中的数据，但可能无权访问环境的配置。

## <a name="grant-data-access"></a>授予数据访问权限
遵循以下步骤向用户主体授予数据访问权限：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 查找时序见解环境。 在“搜索”框中，键入“时序见解”。 在搜索结果中选择“时序环境”。 

3. 从列表中选择时序见解环境。
   
4. 选择“数据访问策略”，然后选择“+ 添加”。
  ![管理时序见解源 - 环境](media/data-access/getstarted-grant-data-access1.png)

5. 选择“选择用户”。  搜索用户名称或电子邮件地址，查找要添加的用户。 单击“选择”确认选择。 

   ![管理时序见解源 - 添加](media/data-access/getstarted-grant-data-access2.png)

6. 选择“选择角色”。 为用户选择相应的访问角色：
   - 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。 
   - 否则请选择“读取者”，允许用户查询环境中的数据，以及在环境中保存个人（非共享）查询。

   选择“确定”确认角色选择。

   ![管理时序见解源 - 选择用户](media/data-access/getstarted-grant-data-access3.png)

8. 在“选择用户角色”页中，选择“确定”。

   ![管理时序见解源 - 选择角色](media/data-access/getstarted-grant-data-access4.png)

9. “数据访问策略”页列出了用户和每个用户的角色。

   ![管理时序见解源 - 结果](media/data-access/getstarted-grant-data-access5.png)

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>向另一个 AAD 租户的用户提供来宾访问权限

“来宾”不是一个管理角色而是一个术语，是指从一个租户被邀请到另一个租户的帐户。 在将来宾帐户邀请到租户目录后，它就可以像任何其他帐户一样拥有相同的访问控制，可以使用访问控制 (IAM) 边栏选项卡授予对 TSI 环境的管理访问权限，或者通过数据访问策略边栏选项卡授予对环境中数据的访问权限。 有关 AAD 租户来宾访问权限的详细信息，请参阅此[文档](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

请按照以下步骤将来宾对时序见解环境的访问权限授予另一个租户的 AAD 用户：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 查找时序见解环境。 在“搜索”框中，键入“时序见解”。 在搜索结果中选择“时序环境”。

3. 从列表中选择时序见解环境。

4. 选择“数据访问策略”，然后选择 +“邀请”。

    ![管理时序见解源 - 邀请用户](media/data-access/getstarted-grant-data-access6.png)

5. 提供想要邀请的用户的电子邮件地址。 注意，此电子邮件地址应该是与 AAD 关联的电子邮件地址。 你可以根据情况在邀请中包括个人消息。

    ![管理时序见解源 - 选择用户](media/data-access/getstarted-grant-data-access7.png)

6. 应该可以看到屏幕上显示的确认气泡。

    ![管理时序见解源 - 确认用户](media/data-access/getstarted-grant-data-access8.png)

7. 选择“选择用户”。 搜索刚邀请的来宾用户的电子邮件地址，找到要添加的用户。 单击“选择”确认选择。
  
    ![管理时序见解源 - 确认用户](media/data-access/getstarted-grant-data-access9.png)

8. 选择“选择角色”。 为来宾用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。

    * 否则请选择“读取者”，允许用户查询环境中的数据，以及在环境中保存个人（非共享）查询。

    选择“确定”确认角色选择。

    ![管理时序见解源 - 选择角色](media/data-access/getstarted-grant-data-access10.png)

9. 在“选择用户角色”页中，选择“确定”。

10. “数据访问策略”页现在列出了来宾用户和每个来宾用户的角色。

    ![管理时序见解源 - 确认角色](media/data-access/getstarted-grant-data-access11.png)

11. 现在，来宾用户需要采取某些步骤才能访问他们刚被邀请到的位于 Azure 租户中的环境。 首先，他们需要接受你刚发送给他们的邀请。 这份邀请通过电子邮件发送到你在步骤 5 中邀请的电子邮件地址。 他们应单击“开始”来接受。

    ![管理时序见解源 - 邀请用户](media/data-access/getstarted-grant-data-access12.png)

12. 接下来，来宾用户需要接受与管理员组织关联的权限。

    ![管理时序见解源 - 接受权限](media/data-access/getstarted-grant-data-access13.png)

13. 当来宾用户登录到你邀请的电子邮件地址后，接受邀请，并前往 insights.azure.com。 在那里，他们需要单击屏幕右上角的电子邮件地址旁的头像。 

    ![管理时序见解源 - 接受权限](media/data-access/getstarted-grant-data-access14.png)

14. 接下来，来宾用户将从目录下拉菜单中选择你的 Azure 租户。 这是你将他们邀请到的租户。 

    ![管理时序见解源 - 接受权限](media/data-access/getstarted-grant-data-access15.png)

15. 最后，当来宾用户选择你的租户时，他们将看到你刚刚为他们提供访问权限的时序见解环境。 现在，他们应该拥有了与你在步骤 8 中提供的角色相关联的所有功能。

## <a name="next-steps"></a>后续步骤
* 了解[如何向 Azure 时序见解环境添加事件中心事件源](time-series-insights-how-to-add-an-event-source-eventhub.md)。
* [发送事件](time-series-insights-send-events.md)到事件源。
* 在[时序见解资源管理器](https://insights.timeseries.azure.com)中查看环境。
