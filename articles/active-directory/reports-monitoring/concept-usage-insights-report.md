---
title: 使用情况和见解报告 |微软文档
description: Azure 活动目录门户中的使用情况和见解报告简介
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008269"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure 活动目录门户中的使用情况和见解报告

使用使用情况和见解报告，您可以获取以应用程序为中心的登录数据视图。 您可以找到以下问题的答案：

*   组织中最常用的应用程序是什么？
*   哪些应用程序的登录失败最多？ 
*   每个应用程序的顶级登录错误是什么？

## <a name="prerequisites"></a>先决条件 

要从使用情况和见解报告中访问数据，您需要：

* Azure AD 租户
* Azure AD 高级版 （P1/P2） 许可证，用于查看登录数据
* 全局管理员、安全管理员、安全读取器或报表读取器角色中的用户。 此外，任何用户（非管理员）都可以访问自己的登录。 

## <a name="access-the-usage-and-insights-report"></a>访问使用情况和见解报告

1. 导航到[Azure 门户](https://portal.azure.com)。
2. 选择正确的目录，然后选择**Azure 活动目录**并选择**企业应用程序**。
3. 在 **"活动"** 部分中，选择 **"使用情况&见解**以打开报表。 

![使用情况和见解报表](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>使用报表

使用情况和见解报告显示尝试一个或多个登录的应用程序列表，并允许您按成功登录数、登录失败次数和成功率进行排序。

单击列表底部的更多加载允许您查看页面上的其他应用程序。 您可以选择日期范围以查看已在范围内使用的所有应用程序。

您还可以将焦点设置为特定应用程序。 选择**视图登录活动**以查看应用程序随时间的变化登录活动以及顶部错误。  

在应用程序使用图中选择一天时，您将获得应用程序登录活动的详细列表。  

![使用情况和见解报表](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>后续步骤

* [登录报告](concept-sign-ins.md)