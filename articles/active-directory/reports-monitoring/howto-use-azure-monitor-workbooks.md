---
title: Azure 监视器报表工作簿 |微软文档
description: 了解如何将 Azure 活动目录报表的 Azure 监视器工作簿使用。
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
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014362"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何使用 Azure 活动目录报表的 Azure 监视器工作簿

> [!IMPORTANT]
> 为了优化此工作簿中的基础查询，请单击"编辑"，单击"设置"图标并选择要运行这些查询的工作区。 默认情况下，工作簿将选择路由 Azure AD 日志的所有工作区。 

是否要：

- 了解[条件访问策略](../conditional-access/overview.md)对用户登录体验的影响吗？

- 排除登录失败，以便更好地了解组织的登录运行状况并快速解决问题？

- 知道谁使用旧版身份验证登录到您的环境？ （通过[阻止旧版身份验证](../conditional-access/block-legacy-authentication.md)，可以改进租户的保护。

- 您是否需要了解条件访问策略对租户的影响？

- 您是否希望能够查看：登录日志查询、工作簿报告有多少用户被授予或拒绝访问，以及有多少用户在访问资源时绕过条件访问策略？

- 有兴趣深入了解：每个条件的工作簿详细信息，以便策略的影响可以按条件进行上下文化，包括设备平台、设备状态、客户端应用、登录风险、位置和应用程序？

- 深入了解登录日志查询，工作簿报告有多少用户被授予或拒绝访问，以及访问资源时绕过条件访问策略的用户数量。

- 为了帮助您解决这些问题，Active Directory 提供了用于监视的工作簿。 [Azure 监视器工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)将文本、分析查询、指标和参数合并为丰富的交互式报表。



本文：

- 假设您熟悉[如何使用 Monitor 工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 说明如何使用 Monitor 工作簿了解条件访问策略的效果、排除登录失败的问题以及标识旧版身份验证。
 


## <a name="prerequisites"></a>先决条件

要使用 Monitor 工作簿，您需要：

- 具有高级 （P1 或 P2） 许可证的活动目录租户。 了解如何[获得高级许可证](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- [日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

- [访问](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions)日志分析工作区
- Azure 活动目录中的角色（如果通过 Azure 活动目录门户访问日志分析）
    - 安全管理员
    - 安全读取者
    - 报告阅读器
    - 全局管理员

## <a name="roles"></a>角色
您必须担任以下角色之一，并[有权访问基础日志分析](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)工作区来管理工作簿：
-   全局管理员
-   安全管理员
-   安全读取者
-   报告阅读器
-   应用程序管理员

## <a name="workbook-access"></a>工作簿访问 

要访问工作簿：

1. 登录到 Azure[门户](https://portal.azure.com)。

1. 导航到**Azure 活动目录** > **监视** > **工作簿**。 

1. 选择报表或模板，或在工具栏上选择 **"打开**"。 

![在 Azure AD 中查找 Azure 监视器工作簿](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>登录分析

要访问登录分析工作簿，请在 **"使用情况**"部分中选择 **"登录**"。 

此工作簿显示以下登录趋势：

- 所有登录

- Success

- 挂起的用户操作

- 失败

您可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![登录分析](./media/howto-use-azure-monitor-workbooks/43.png)


对于每种趋势，您可以按以下类别进行细分：

- 位置

    ![按位置登录](./media/howto-use-azure-monitor-workbooks/45.png)

- 设备

    ![按设备登录](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用旧版身份验证登录 


要访问使用[旧版身份验证](../conditional-access/block-legacy-authentication.md)的登录工作簿，请在 **"使用"** 部分中选择**使用旧身份验证的登录**。 

此工作簿显示以下登录趋势：

- 所有登录

- Success


您可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

- 协议

![按旧版身份验证登录](./media/howto-use-azure-monitor-workbooks/47.png)


对于每种趋势，您都会按应用和协议进行细分。

![按应用和协议进行旧版身份验证登录](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>按条件访问登录 


要通过[条件访问策略](../conditional-access/overview.md)访问登录工作簿，请在 **"条件访问**"部分中，**选择"条件访问"中的登录**。 

此工作簿显示禁用登录的趋势。您可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![使用条件性访问登录](./media/howto-use-azure-monitor-workbooks/49.png)


对于禁用的登录，您可以按条件访问状态进行细分。

![条件访问状态](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>条件访问见解

### <a name="overview"></a>概述

工作簿包含登录日志查询，可帮助 IT 管理员监视其租户中条件访问策略的影响。 您可以报告有多少用户被授予或拒绝访问权限。 工作簿包含有关在登录时有多少用户会根据这些用户的属性绕过条件访问策略的见解。 它包含每个条件的详细信息，以便策略的影响可以按条件进行上下文化，包括设备平台、设备状态、客户端应用、登录风险、位置和应用程序。

### <a name="instructions"></a>Instructions 
要访问条件访问见解的工作簿，请在"条件访问"部分中选择 **"条件访问见解**"工作簿。 此工作簿显示租户中每个条件访问策略的预期影响。 从下拉列表中选择一个或多个条件访问策略，并通过应用以下筛选器缩小工作簿的范围： 

- **时间范围**

- **用户**

- **应用**

- **数据视图**

![条件访问状态](./media/howto-use-azure-monitor-workbooks/access-insights.png)


影响摘要显示所选策略具有特定结果的用户或登录数。 总计是在所选时间范围内为其计算所选策略的用户或登录数。 单击磁贴可按该结果类型筛选工作簿中的数据。 

![条件访问状态](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

此工作簿还显示按六个条件细分的选定策略的影响： 
- **设备状态**
- **设备平台**
- **客户端应用**
- **登录风险**
- **位置**
- **应用程序**

![条件访问状态](./media/howto-use-azure-monitor-workbooks/device-platform.png)

您还可以调查各个登录，由工作簿中选择的参数筛选。 搜索按登录频率排序的单个用户，并查看相应的登录事件。 

![条件访问状态](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>通过授予控件登录

要通过[授予控件](../conditional-access/controls.md)访问登录工作簿，请在 **"条件访问"** 部分中，**选择"通过授予控件登录**"。 

此工作簿显示以下禁用登录趋势：

- 要求 MFA
 
- 需要使用条款

- 需要隐私声明

- 其他


您可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![通过授予控件登录](./media/howto-use-azure-monitor-workbooks/50.png)


对于每种趋势，您都会按应用和协议进行细分。

![最近登录的细目](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登录失败分析

使用**登录失败分析**工作簿解决错误：

- 登录
- 条件访问策略
- 旧式身份验证 


要按条件访问数据访问登录，请在 **"疑难解答**"部分中，**选择使用旧身份验证的登录**。 

此工作簿显示以下登录趋势：

- 所有登录

- Success

- 挂起的操作

- 失败


您可以按以下类别筛选每个趋势：

- 时间范围

- 应用

- 用户

![故障排除登录](./media/howto-use-azure-monitor-workbooks/52.png)


为了帮助解决登录问题，Azure 监视器按以下类别提供细分：

- 热门错误

    ![顶级错误的摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 等待用户操作的登录

    ![等待用户操作的登录摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>后续步骤

[使用监视器工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。
