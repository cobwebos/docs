---
title: "如何在 Azure 门户中查找活动报表 | Microsoft 文档"
description: "了解如何在 Azure 门户中查找活动报表"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 800397efdf2e3e3cb95c77bc90f45bb8852e1e42
ms.openlocfilehash: d05cf7dc62518ea5519f319a2502e1d261b148f3
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>如何在 Azure 门户中查找活动报表

通过从 Azure 经典门户迁移到 Azure 门户，我们为 Azure Active Directory 活动日志提供了新的外观。 我们在几个月前已经发布了一篇[博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)来说明我们如何在你正在处理的资源的上下文中提供活动日志。 本文介绍了我们如何将 Azure 经典门户中的现有报表转换成了 Azure 门户中的新报表。

## <a name="what-is-new"></a>有哪些新内容？

Azure 经典门户中的报表划分为各种类别：

1.    安全报表
2.    活动报表
3.    集成的应用报表

### <a name="activity-and-integrated-app-reports"></a>活动和集成的应用报表

发展到 Azure 门户中基于上下文的报告，我们已将现有报表合并到单个视图中，并使用单个基础 API 向此视图提供数据。 在 Azure 门户（左侧导航栏）中，可以在“活动” >“审核日志”下找到此视图。 下面是已合并到此视图中的报表。

-    审核报表

-     密码重置活动

-     密码重置注册活动

-     自助服务组活动

-     Office365 组名称更改

-     帐户设置活动

-     密码滚动更新状态
-     帐户设置错误


“应用程序使用情况”报表已增强并包括在名为“活动”->“登录”的视图中（在左侧导航栏中），并且其中包括所有用户的登录，然后可以根据这些登录信息推断应用程序使用情况信息。 另外，还可以通过“企业应用程序”概述屏幕查找应用使用情况信息。

## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>如何访问此单一视图中的特定报表？

### <a name="audit-logs"></a>审核日志

许多客户的关键诉求之一是要求能够使用多个筛选器选项来访问 Azure AD 中的活动日志。 我们已取代了此功能，并为你提供了高级筛选机制来筛选你需要的数据。 我们提供的其中一个筛选器名为“活动类别”，它可以列出 Azure AD 提供的各种类型的活动日志。 通过选择所需的类别，你可以将活动日志的结果范围缩小为你希望查看的几个。 

例如，如果仅希望获取与**自助服务密码重置**相关的活动，则可以选择“自助服务密码管理”类别。 你可以看到的类别取决于你正在处理的资源。  


![审核日志](./media/active-directory-reporting-migration/06.png "审核日志")

 
目前提供的各种类别包括：

- 核心目录

- 自助服务密码管理

- 自助服务组管理

- 帐户预配

### <a name="application-usage"></a>应用程序使用情况

可以通过“活动”->“登录”视图查看所有应用或单个应用的应用程序使用情况。 如下所示，可以针对所有应用程序或单个应用程序呈现此视图。 如果希望缩小结果范围，可以按用户名或应用程序名称进行筛选。
 

![审核日志](./media/active-directory-reporting-migration/07.png "审核日志")


### <a name="security-reports"></a>安全报表

安全报表已合并到一个视图中，此视图中提供了 Azure Active Directory 可以检测并报告的所有风险信号。

此合并的视图提供以下方面的数据：

- 有风险的用户
- 有风险的登录 


![审核日志](./media/active-directory-reporting-migration/04.png "审核日志")



## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Azure 经典门户中的与 Azure 门户中的活动报表之对比

本部分列出了 Azure 经典门户中的现有报表并说明了在 Azure 门户中如何获取该信息。

**审核日志：**

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“核心目录” 

**密码重置活动：**

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“自助服务密码管理” 

**密码重置注册活动：**    

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“自助服务密码管理”。

**自助服务组活动：**    

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“自助服务组管理”

**Office365 组名称更改：**

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“自助服务密码管理”
3. 对于“活动资源类型”，选择“组” 
4. 对于“活动源”，选择“O365 组” 

**帐户预配活动：**    

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“帐户用户预配”

**密码滚动更新状态：**    

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“自动化应用密码滚动更新”

**帐户预配错误：**

1. 在左侧导航窗格中，单击“活动”，然后单击“审核日志”
2. 对于“活动类别”，选择“帐户用户预配”

**应用程序使用情况：**

- 在左侧导航窗格中，单击“企业应用程序”，然后单击“登录”



