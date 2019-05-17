---
title: Azure Active Directory 门户中的报表使用情况和见解 |Microsoft Docs
description: Azure Active Directory 门户中的使用情况和见解的报表简介
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
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806351"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的使用情况和见解报告

与使用情况和见解的报表，可以获取登录数据的应用程序为中心的视图。 您可以找到以下问题的答案：

*   什么是顶部使用我的组织中的应用程序？
*   哪些应用程序具有最失败的登录名？ 
*   每个应用程序的顶部登录错误有哪些？

## <a name="prerequisites"></a>必备组件 

若要从使用情况和见解报表访问的数据，需要：

* Azure AD 租户
* Azure AD 高级版 (P1/P2) 许可证，若要查看登录数据
* 全局管理员、 安全管理员、 安全读者或报表读取器角色中的用户。 此外，任何用户 （非管理员） 可以访问其自己的登录名。 

## <a name="access-the-usage-and-insights-report"></a>访问使用情况和见解报告

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 选择正确的目录，然后选择**Azure Active Directory** ，然后选择**企业应用程序**。
3. 从**活动**部分中，选择**根据使用情况和见解**以打开该报表。 

![使用情况和见解的报表](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>使用报表

使用情况和见解报表显示具有一个或多个登录的应用程序的列表中尝试，并允许您要作为排序依据的成功登录、 失败的登录名、 和的成功率数。

单击负载更列表的底部，可在页上查看其他应用程序。 可以选择要查看范围内使用过的所有应用程序的日期范围。

此外可以在特定的应用程序上设置焦点。 选择**查看登录活动**若要查看登录活动中不同的应用程序，以及最常发生的错误的时间。  

当应用程序使用情况图中选择某一天时，你会获取应用程序的登录活动的详细的列表。  

![使用情况和见解的报表](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>后续步骤

* [登录报告](concept-sign-ins.md)