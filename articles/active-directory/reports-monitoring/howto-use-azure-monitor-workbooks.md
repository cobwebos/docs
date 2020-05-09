---
title: 报表 Azure Monitor 工作簿 |Microsoft Docs
description: 了解如何使用 Azure Active Directory 报表 Azure Monitor 工作簿。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 094939ee62046c72f07ac46f0781e687034f731f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690618"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表

> [!IMPORTANT]
> 为了优化此工作簿中的基础查询，请单击 "编辑"，单击 "设置" 图标，然后选择要在其中运行这些查询的工作区。 默认情况下，工作簿将选择要在其中路由 Azure AD 日志的所有工作区。 

是否要：

- 了解[条件访问策略](../conditional-access/overview.md)对用户登录体验的影响？

- 排查登录失败问题以更好地了解你的组织的登录运行状况并快速解决问题？

- 知道谁在使用旧身份验证登录到你的环境？ （通过[阻止旧身份验证](../conditional-access/block-legacy-authentication.md)，你可以改进租户的保护。）

- 是否需要了解租户中条件访问策略的影响？

- 你是否希望能够查看：登录日志查询，工作簿报告授予或拒绝了多少用户的访问权限，以及在访问资源时，有多少用户跳过了条件性访问策略？

- 对开发更深入了解的内容感兴趣：工作簿每个条件的详细信息，以便每个条件都可以过程策略的影响，包括设备平台、设备状态、客户端应用、登录风险、位置和应用程序？

- 更深入地了解登录日志查询，该工作簿会报告被授予或拒绝了多少用户的访问权限，以及在访问资源时有多少用户跳过了条件性访问策略。

- 为了帮助解决这些问题，Azure Active Directory 提供了用于监视的工作簿。 [Azure Monitor 工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)将文本、分析查询、指标和参数合并到丰富的交互式报表中。



本文：

- 假设你熟悉如何[使用监视器工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 介绍如何使用监视工作簿来了解条件访问策略的效果，排除登录失败的问题，并识别旧身份验证。
 


## <a name="prerequisites"></a>先决条件

若要使用监视器工作簿，需要：

- 具有高级（P1 或 P2）许可证的 Azure Active Directory 租户。 了解如何[获取高级许可证](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- [Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

- [访问](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions)log analytics 工作区
- Azure Active Directory 中的以下角色（如果你要通过 Azure Active Directory 门户访问 Log Analytics）
    - 安全管理员
    - 安全读取者
    - 报表读取器
    - 全局管理员

## <a name="roles"></a>角色
你必须是以下角色之一，并且有[权访问基础 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)工作区来管理工作簿：
-   全局管理员
-   安全管理员
-   安全读取者
-   报表读取器
-   应用程序管理员

## <a name="workbook-access"></a>工作簿访问 

访问工作簿：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到**Azure Active Directory** > **监视** > **工作簿**。 

1. 选择报表或模板，或在工具栏上选择 "**打开**"。 

![在 Azure AD 中查找 Azure Monitor 工作簿](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>登录分析

若要访问登录分析工作簿，请在 "**使用情况**" 部分中，选择 "**登录**"。 

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


对于每个趋势，你会按以下类别获得细目：

- 位置

    ![按位置登录](./media/howto-use-azure-monitor-workbooks/45.png)

- 设备

    ![通过设备登录](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用旧身份验证登录 


若要访问使用[旧身份验证](../conditional-access/block-legacy-authentication.md)的登录工作簿，请在 "**使用情况**" 部分中，选择 "**使用旧身份验证登录**"。 

此工作簿显示以下登录趋势：

- 所有登录

- Success


可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

- 协议

![通过旧身份验证登录](./media/howto-use-azure-monitor-workbooks/47.png)


对于每个趋势，你会按应用和协议获取细目。

![按应用和协议进行的旧式身份验证登录](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>通过条件访问登录 


若要通过[条件访问策略](../conditional-access/overview.md)访问用于登录的工作簿，请在 "**条件性访问**" 部分中，选择 "**通过条件访问登录**"。 

此工作簿显示禁用登录的趋势。可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![使用条件性访问登录](./media/howto-use-azure-monitor-workbooks/49.png)


对于已禁用的登录，你将获得条件性访问状态的细分。

![条件访问状态](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>条件访问见解

### <a name="overview"></a>概述

工作簿包含可帮助 IT 管理员监视其租户中的条件访问策略影响的登录日志查询。 您可以报告被授予或拒绝了多少用户的访问权限。 此工作簿包含了有关在登录时将基于这些用户的属性绕过条件性访问策略的用户的见解。 它包含每个条件的详细信息，以便每个条件都可以过程策略的影响，包括设备平台、设备状态、客户端应用、登录风险、位置和应用程序。

### <a name="instructions"></a>说明 
若要访问条件性访问见解的工作簿，请在 "条件性访问" 部分中选择**条件性访问见解**工作簿。 此工作簿显示你的租户中每个条件访问策略的预期影响。 从下拉列表中选择一个或多个条件性访问策略，并通过应用以下筛选器来缩小工作簿范围： 

- **时间范围**

- **用户**

- **应用**

- **数据视图**

![条件访问状态](./media/howto-use-azure-monitor-workbooks/access-insights.png)


影响摘要显示所选策略具有特定结果的用户或登录的数目。 Total 是在所选时间范围内评估所选策略的用户或登录数。 单击磁贴可以按该结果类型筛选工作簿中的数据。 

![条件访问状态](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

此工作簿还显示按六个条件划分的选定策略的影响： 
- **设备状态**
- **设备平台**
- **客户端应用**
- **登录风险**
- **位置**
- **应用程序**

![条件访问状态](./media/howto-use-azure-monitor-workbooks/device-platform.png)

你还可以调查单个登录，并按工作簿中选定的参数进行筛选。 搜索各个用户，按登录频率排序，并查看其相应的登录事件。 

![条件访问状态](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>通过授予控件登录

若要通过[授权控件](../conditional-access/controls.md)访问工作簿以进行登录，请在 "**条件性访问**" 部分中，选择 "**通过授予控件登录**"。 

此工作簿显示了以下已禁用的登录趋势：

- 需要进行 MFA
 
- 需要使用条款

- 需要隐私声明

- 其他


可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![通过授予控件登录](./media/howto-use-azure-monitor-workbooks/50.png)


对于每个趋势，你会按应用和协议获取细目。

![最近登录的细目](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登录失败分析

使用**登录失败分析**工作簿排查错误：

- 登录
- 条件访问策略
- 旧式身份验证 


若要通过条件访问数据访问登录，请在 "**故障排除**" 部分中，选择 "**使用旧身份验证登录**"。 

此工作簿显示以下登录趋势：

- 所有登录

- Success

- 挂起的操作

- 失败


可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![登录疑难解答](./media/howto-use-azure-monitor-workbooks/52.png)


为帮助您解决登录问题，Azure Monitor 提供以下类别的细目：

- 最常见错误

    ![顶部错误摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 正在等待用户操作的登录

    ![等待用户操作的登录摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>后续步骤

[使用 "监视工作簿" 创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。
