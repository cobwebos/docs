---
title: 使用情况和见解报表 |Microsoft Docs
description: Azure Active Directory 门户中的使用情况和见解报表简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008269"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的使用情况和见解报告

使用 "使用情况和 insights" 报表，你可以获取以应用程序为中心的登录数据的视图。 你可以找到以下问题的答案：

*   我的组织中使用最多的应用程序是什么？
*   哪些应用程序的登录失败最多？ 
*   每个应用程序的最常见登录错误是什么？

## <a name="prerequisites"></a>先决条件 

若要访问使用情况报表和 insights 报表中的数据，需要：

* Azure AD 租户
* 用于查看登录数据的 Azure AD 高级（P1/P2）许可证
* 全局管理员、安全管理员、安全读者或报表读者角色中的用户。 此外，任何用户（非管理员）都可以访问自己的登录。 

## <a name="access-the-usage-and-insights-report"></a>访问使用情况和见解报告

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 选择正确的目录，然后选择 " **Azure Active Directory** "，然后选择 "**企业应用程序**"。
3. 从 "**活动**" 部分中，选择 "**使用情况 & insights** " 以打开报表。 

![使用情况和见解报表](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>使用报表

使用情况和 insights 报表显示具有一次或多次尝试登录的应用程序列表，并允许你按成功登录的次数、失败的登录次数和成功率进行排序。

单击列表底部的 "加载更多"，可以在页面上查看其他应用程序。 您可以选择日期范围来查看在该范围内使用的所有应用程序。

你还可以在特定应用程序上设置焦点。 选择 "**查看登录活动**" 可查看一段时间内应用程序的登录活动，以及最常见的错误。  

当你在 "应用程序使用情况" 图中选择一天时，将获取该应用程序的登录活动的详细列表。  

![使用情况和见解报表](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>后续步骤

* [登录报告](concept-sign-ins.md)