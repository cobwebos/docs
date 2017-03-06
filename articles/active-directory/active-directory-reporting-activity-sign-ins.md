---
title: "Azure Active Directory 门户中的“登录活动”报表 - 预览 | Microsoft Docs"
description: "Azure Active Directory 门户中的“登录活动”报表简介 - 预览"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 78617dc7e3a4b6eb4fc32d32b6850b8c0832d6d8
ms.openlocfilehash: 9819c5f6a3aea53664d86e3a23b25946c0f2b731
ms.lasthandoff: 02/22/2017


---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal---preview"></a>Azure Active Directory 门户中的“登录活动”报表 - 预览

通过 Azure Active Directory [预览](active-directory-preview-explainer.md)中的报表，可以获取确定环境运行状况所需的所有信息。

Azure Active Directory 中的报表体系结构由以下部分组成：

- **活动** 
    - **登录活动** — 有关托管应用程序的使用和用户登录活动的信息
    - **审核日志** - 有关用户和组管理、托管应用程序和目录活动的系统活动信息。
- **安全性** 
    - **风险登录** - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 有关详细信息，请参阅“有风险的登录”。
    - **已标记为存在风险的用户** - 风险用户是指可能已泄露的用户帐户。 有关详细信息，请参阅“已标记为存在风险的用户”。

本主题概述登录活动。

## <a name="signs-in-activities"></a>登录活动

通过用户登录报表提供的信息，可以找到一些问题的答案，例如：

* 什么是用户的登录模式？
* 多少用户超过一周都有登录行为？
* 这些登录的状态怎样？

该数据的入口点为“概览”部分的“用户和组”下面的用户登录图。

 ![报告](./media/active-directory-reporting-activity-sign-ins/05.png "报告")

用户登录图显示指定时间内所有用户的按周汇总的登录信息。 默认时间为 30 天。

![报告](./media/active-directory-reporting-activity-sign-ins/02.png "报告")

单击登录图中的某一天时，可以获取登录活动的详细列表。

![报告](./media/active-directory-reporting-activity-sign-ins/03.png "报告")

登录活动列表中的每一行提供了有关所选登录的详细信息，例如：

* 登录者是谁？
* 相关的 UPN 是什么？
* 登录的目标应用程序是哪个？
* 登录的 IP 地址是多少？
* 登录的状态怎样？

## <a name="usage-of-managed-applications"></a>托管应用程序的使用情况

通过登录数据的以应用程序为中心的视图，可以回答如下问题：

* 谁正在使用我的应用程序？
* 组织中最常用的 3 个应用程序是哪些？
* 我最近推出了一个应用程序。 它用起来怎样？

该数据的入口点为“概览”部分的“企业应用程序”下面的组织过去 30 天的报告中最常用的 3 个应用程序。

 ![报告](./media/active-directory-reporting-activity-sign-ins/06.png "报告")

应用程序使用情况图显示指定时间内最常用的 3 个应用程序的按周汇总的登录情况。 默认时间为 30 天。

![报告](./media/active-directory-reporting-activity-sign-ins/78.png "报告")

如果需要，可以将焦点设置在特定应用程序上。

![报告](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "报告")

单击应用程序使用情况图中的某一天时，可以获取登录活动的详细列表。

![报告](./media/active-directory-reporting-activity-sign-ins/top_app_sign_ins.png "报告")

**登录** 选项可提供应用程序的所有登录事件的完整概览。

![报告](./media/active-directory-reporting-activity-sign-ins/85.png "报告")

使用列选择器可以选择想要显示的数据字段。

![报告](./media/active-directory-reporting-activity-sign-ins/column_chooser.png "报告")

## <a name="filtering-sign-ins"></a>筛选登录信息
可以使用以下字段筛选登录信息，以限制显示的数据量：

* 日期和时间 
* 用户的用户主体名称
* 应用程序名称
* 客户端名称
* 登录状态

![报告](./media/active-directory-reporting-activity-sign-ins/293.png "报告")

用于筛选登录活动的条目的另一种方法是搜索特定条目。
搜索方法能够将登录信息的范围限定在特定的**用户**、**组**或**应用程序**。

![报告](./media/active-directory-reporting-activity-sign-ins/84.png "报告")


## <a name="next-steps"></a>后续步骤
请参阅 [Azure Active Directory 报表指南](active-directory-reporting-guide.md)。


