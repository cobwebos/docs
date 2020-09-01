---
title: 用于报表的 Azure Monitor 工作簿 | Microsoft Docs
description: 了解如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 510991c1afba9a5ffbfd77e855ee25d8b641bdd0
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226975"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表

> [!IMPORTANT]
> 为优化此工作簿中的基础查询，请单击“编辑”，单击“设置”图标，然后选择要在其中运行这些查询的工作区。 默认情况下，工作簿将选择要在其中路由 Azure AD 日志的所有工作区。 

是否希望：

- 了解[条件访问策略](../conditional-access/overview.md)对用户登录体验的影响？

- 排查登录失败问题，以更好地了解组织的登录运行状况并快速解决问题？

- 知道谁在使用旧式身份验证登录环境？ （通过[阻止旧式身份验证](../conditional-access/block-legacy-authentication.md)，可以改进对租户的保护。）

- 是否需要了解条件访问策略在租户中所造成的影响？

- 是否希望能够查看以下内容：登录日志查询、显示获得或未获得访问权限用户数的工作簿报表，以及访问资源时绕过条件访问策略的用户数？

- 是否需要深入了解：每个条件的工作簿详细信息，以便根据条件将策略的影响置于背景中考虑，包括设备平台、设备状态、客户端应用、登录风险、位置和应用程序？

- 深入了解登录日志查询、显示获得或未获得访问权限用户数的工作簿报表，以及访问资源时绕过条件访问策略的用户数。

- 为帮助解决这些问题，Azure Active Directory 提供了用于监视的工作簿。 [Azure Monitor 工作簿](../../azure-monitor/platform/workbooks-overview.md)可将文本、分析查询、指标和参数合并到丰富的交互式报表中。



本文：

- 假设你熟悉如何[使用 Monitor 工作簿创建交互式报表](../../azure-monitor/platform/workbooks-overview.md)。

- 介绍如何使用 Monitor 工作簿了解条件访问策略的效果，以排查登录失败并识别旧式身份验证。
 


## <a name="prerequisites"></a>先决条件

要使用 Monitor 工作簿，需要：

- 拥有高级 (P1 或 P2) 许可证的 Azure Active Directory 租户。 了解如何[获得高级许可证](../fundamentals/active-directory-get-started-premium.md)。

- [Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。

- 对 Log Analytics 工作区的[访问权限](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)
- Azure Active Directory 中的以下角色（如果要通过 Azure Active Directory 门户访问 Log Analytics）
    - 安全管理员
    - 安全读取者
    - 报告读取者
    - 全局管理员

## <a name="roles"></a>角色
必须是以下角色之一，并且可以[访问基础 Log Analytics](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) 工作区以管理工作簿：
-   全局管理员
-   安全管理员
-   安全读取者
-   报告读取者
-   应用程序管理员

## <a name="workbook-access"></a>工作簿访问 

要访问工作簿，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到 Azure Active Directory > “监视” > “工作簿”  。 

1. 选择报表或模板，或在工具栏上选择“打开”。 

![在 Azure AD 中查找 Azure Monitor 工作簿](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>登录分析

要访问登录分析工作簿，请选择“使用情况”部分的“登录” 。 

此工作簿显示以下登录趋势：

- 所有登录

- Success

- 挂起的用户操作

- 失败

可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![登录分析](./media/howto-use-azure-monitor-workbooks/43.png)


对于每个趋势，可以按以下类别进行细分：

- 位置

    ![登录情况（按位置）](./media/howto-use-azure-monitor-workbooks/45.png)

- 设备

    ![登录情况（按设备）](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用旧式身份验证进行的登录 


要通过访问工作簿来了解使用[旧式身份验证](../conditional-access/block-legacy-authentication.md)的登录情况，请在“使用情况”部分，选择“使用旧式身份验证登录” 。 

此工作簿显示以下登录趋势：

- 所有登录

- Success


可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

- 协议

![登录情况（按旧式身份验证）](./media/howto-use-azure-monitor-workbooks/47.png)


对于每个趋势，可以按应用和协议进行细分。

![旧式身份验证登录情况（按应用和协议）](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>登录情况（按条件访问） 


要通过访问工作簿来了解按[条件访问策略](../conditional-access/overview.md)划分的登录情况，请在“条件访问”部分，选择“登录情况(按条件访问)” 。 

此工作簿显示已禁用登录的趋势。可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![使用条件性访问登录](./media/howto-use-azure-monitor-workbooks/49.png)


对于已禁用登录，可以按条件访问状态进行细分。

![条件访问状态](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>条件访问见解

### <a name="overview"></a>概述

工作簿包含登录日志查询，可帮助 IT 管理员监视条件访问策略在租户中所造成的影响。 你可以报告已获得或未获得访问权限的用户数。 该工作簿记录了在登录时根据这些用户属性绕过条件访问策略的用户数。 它包含每个条件的详细信息，以便根据条件将策略的影响置于背景中考虑，包括设备平台、设备状态、客户端应用、登录风险、位置和应用程序。

### <a name="instructions"></a>Instructions 
要访问工作簿以获取条件访问见解，请在“条件访问”部分，选择“条件访问见解”工作簿。 该工作簿显示每个条件访问策略在租户中所造成的预期影响。 从下拉列表中选择一个或多个条件访问策略，并应用以下筛选器缩小工作簿的范围： 

- **时间范围**

- **用户**

- 应用

- **数据视图**

![条件访问状态](./media/howto-use-azure-monitor-workbooks/access-insights.png)


影响摘要显示所选策略对其产生特定结果的用户或登录数。 总计显示在选定时间范围内针对选定策略计算的用户或登录数。 单击磁贴可以按该结果类型筛选工作簿中的数据。 

![条件访问状态](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

该工作簿还按以下六项条件细分显示所选策略造成的影响： 
- **设备状态**
- **设备平台**
- **客户端应用**
- **登录风险**
- **位置**
- **应用程序**

![条件访问状态](./media/howto-use-azure-monitor-workbooks/device-platform.png)

你还可以调查按工作簿中所选参数进行筛选的单个登录。 搜索按登录频率排序的单个用户，并查看其相应的登录事件。 

![条件访问状态](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>登录情况（按授权控制）

要通过访问工作簿来了解按[授权控制](../conditional-access/controls.md)划分的登录情况，请在“条件访问”部分，选择“登录情况(按授权控制)” 。 

此工作簿显示以下已禁用登录趋势：

- 要求 MFA
 
- 需要使用条款

- 需要隐私声明

- 其他


可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![登录情况（按授权控制）](./media/howto-use-azure-monitor-workbooks/50.png)


对于每个趋势，可以按应用和协议进行细分。

![最近登录情况的细分](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登录失败分析

使用“登录失败分析”工作簿解决以下情况中的错误：

- 登录
- 条件访问策略
- 旧式身份验证 


要访问按条件访问数据划分的登录情况，请在“排除故障”部分，选择“使用旧式身份验证登录” 。 

此工作簿显示以下登录趋势：

- 所有登录

- Success

- 挂起的操作

- 失败


可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![登录故障排除](./media/howto-use-azure-monitor-workbooks/52.png)


为帮助对登录进行故障排除，Azure Monitor 按以下类别进行了细分：

- 最常见的错误

    ![最常见错误摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 用户操作时登录等待

    ![用户操作时登录等待的摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>后续步骤

[使用 Monitor 工作簿创建交互式报表](../../azure-monitor/platform/workbooks-overview.md)。