---
title: 将 Azure Monitor 工作簿用于 Azure Active Directory 报表 |Microsoft Docs
description: 了解如何使用 Azure Active Directory 报表 Azure Monitor 工作簿。
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: c1ecdb80263efda4cbbb43caaa4e27a04b261f81
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989801"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表

是否要:

- 了解[条件访问策略](../conditional-access/overview.md)对用户登录体验的影响？

- 排查登录失败问题以更好地了解你的组织的登录运行状况并快速解决问题？

- 知道谁在使用旧身份验证登录到你的环境？ (通过[阻止旧身份验证](../conditional-access/block-legacy-authentication.md), 你可以改进租户的保护。)

为了帮助解决这些问题, Active Directory 提供了用于监视的工作簿。 [Azure Monitor 工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)将文本、分析查询、指标和参数合并到丰富的交互式报表中。 

本文：

- 假设你熟悉如何[使用监视器工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 介绍如何使用监视工作簿来了解条件访问策略的效果, 排除登录失败的问题, 并识别旧身份验证。
 


## <a name="prerequisites"></a>先决条件

若要使用监视器工作簿, 需要:

- 具有高级 (P1 或 P2) 许可证的 Active Directory 租户。 了解如何[获取高级许可证](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- [Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="workbook-access"></a>工作簿访问 

访问工作簿:

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左侧导航窗格中, 选择 " **Azure Active Directory**"。

3. 在 "**监视**" 部分, 选择 "**工作簿**"。 

    ![选择见解](./media/howto-use-azure-monitor-workbooks/41.png)

4. 选择报表或模板, 或在工具栏上选择 "**打开**"。 

    ![选择打开](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>登录分析

若要访问登录分析工作簿, 请在 "**使用情况**" 部分中, 选择 "**登录**"。 

此工作簿显示以下登录趋势:

- 所有登录

- Success

- 挂起的用户操作

- 失败

可以按以下类别筛选每个趋势:

- 时间范围

- 应用程序

- 位用户

![登录分析](./media/howto-use-azure-monitor-workbooks/43.png)


对于每个趋势, 你会按以下类别获得细目:

- Location

    ![按位置登录](./media/howto-use-azure-monitor-workbooks/45.png)

- 设备

    ![通过设备登录](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用旧身份验证登录 


若要访问使用[旧身份验证](../conditional-access/block-legacy-authentication.md)的登录工作簿, 请在 "**使用情况**" 部分中, 选择 "**使用旧身份验证登录**"。 

此工作簿显示以下登录趋势:

- 所有登录

- Success


可以按以下类别筛选每个趋势:

- 时间范围

- 应用程序

- 位用户

- 协议

![通过旧身份验证登录](./media/howto-use-azure-monitor-workbooks/47.png)


对于每个趋势, 你会按应用和协议获取细目。

![按应用和协议进行的旧式身份验证登录](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>通过条件访问登录 


若要通过[条件访问策略](../conditional-access/overview.md)访问用于登录的工作簿, 请在 "**条件性访问**" 部分中, 选择 "**通过条件访问登录**"。 

此工作簿显示禁用登录的趋势。可以按以下类别筛选每个趋势:

- 时间范围

- 应用程序

- 位用户

![使用条件性访问登录](./media/howto-use-azure-monitor-workbooks/49.png)


对于已禁用的登录, 你将获得条件性访问状态的细分。

![条件访问状态](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>通过授予控件登录

若要通过[授权控件](../conditional-access/controls.md)访问工作簿以进行登录, 请在 "**条件性访问**" 部分中, 选择 "**通过授予控件登录**"。 

此工作簿显示了以下已禁用的登录趋势:

- 要求 MFA
 
- 需要使用条款

- 需要隐私声明

- 其他


可以按以下类别筛选每个趋势:

- 时间范围

- 应用程序

- 位用户

![通过授予控件登录](./media/howto-use-azure-monitor-workbooks/50.png)


对于每个趋势, 你会按应用和协议获取细目。

![最近登录的细目](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登录失败分析

使用**登录失败分析**工作簿解决以下错误:

- 登录
- 条件访问策略
- 旧身份验证 


若要通过条件访问数据访问登录, 请在 "**故障排除**" 部分中, 选择 "**使用旧身份验证登录**"。 

此工作簿显示以下登录趋势:

- 所有登录

- Success

- 挂起的操作

- 失败


可以按以下类别筛选每个趋势:

- 时间范围

- 应用程序

- 位用户

![登录疑难解答](./media/howto-use-azure-monitor-workbooks/52.png)


为帮助您解决登录问题, Azure Monitor 提供以下类别的细目:

- 最常见错误

    ![顶部错误摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 正在等待用户操作的登录

    ![等待用户操作的登录摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>后续步骤

[使用 "监视工作簿" 创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。
