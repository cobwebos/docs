---
title: 使用 Azure Monitor 的工作簿的报表的 Azure Active Directory |Microsoft Docs
description: 了解如何使用 Azure Monitor 工作簿获取 Azure Active Directory 报告
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287137"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何：使用 Azure Monitor 工作簿获取 Azure Active Directory 报告

想：

- 了解影响您[条件性访问策略](../conditional-access/overview.md)上用户的登录体验？

- 对登录失败以更好地了解组织登录运行状况并快速解决问题进行故障排除？

- 知道谁在使用旧的身份验证登录到你的环境？ 通过[阻止旧式身份验证](../conditional-access/block-legacy-authentication.md)，可以改进你的租户的保护。


[Azure 监视器工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)将文本、 Analytics 查询、 Azure 度量值和参数组合到丰富的交互式报表。 Azure Active Directory 为你提供帮助您找到上述问题的答案进行监视的工作簿。

本文：

- 假定你熟悉如何[使用 Azure Monitor 工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 介绍了如何使用 Azure Monitor 的工作簿有关监视回答上述问题。
 


## <a name="prerequisites"></a>必备组件

若要使用此功能，需满足以下条件：

- Azure Active Directory 租户，与高级 (P1/P2) 许可证。 了解如何[获得高级许可证](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- 一个[Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="access-workbooks"></a>访问工作簿 

若要访问工作簿：

1. 登录到您[Azure 门户](https://portal.azure.com)。

2. 在左侧导航栏中，单击“Azure Active Directory”。

3. 在中**监视**部分中，单击**Insights**。 

    ![洞察力](./media/howto-use-azure-monitor-workbooks/41.png)

4. 单击报表或模板，或单击**打开**工具栏中。 

    ![库](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>在登录分析

若要访问登录 analysis 工作簿，请单击**登录名**中**用法**部分。 

此工作簿显示了以下登录趋势：

- 所有登录

- Success

- 挂起的用户操作

- 失败

您可以筛选按每个趋势：

- 时间范围

- 应用

- 用户

![库](./media/howto-use-azure-monitor-workbooks/43.png)


每个趋势，通过获取细分：

- Location

    ![库](./media/howto-use-azure-monitor-workbooks/45.png)

- 设备

    ![库](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用传统的身份验证登录名 


访问登录名使用[旧式身份验证](../conditional-access/block-legacy-authentication.md)工作簿中，单击**登录名使用传统的身份验证**中**使用情况**部分。 

此工作簿显示了以下登录趋势：

- 所有登录

- Success


您可以筛选按每个趋势：

- 时间范围

- 应用

- 用户

- 协议 

![库](./media/howto-use-azure-monitor-workbooks/47.png)


对于每个趋势，您获取按应用程序和协议细分。

![库](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>通过条件性访问的登录名 


若要访问的登录名[条件性访问策略](../conditional-access/overview.md)工作簿中，单击**条件访问的登录名**中**条件性访问**部分。 

此工作簿显示了已禁用的登录名的趋势。

您可以筛选按每个趋势：

- 时间范围

- 应用

- 用户

![库](./media/howto-use-azure-monitor-workbooks/49.png)


为已禁用的登录名，可获取细分的条件访问状态。

![条件性访问状态](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>通过登录授权控件

若要访问的登录名[授权控件](../conditional-access/controls.md)工作簿中，单击**登录名授予控制通过**中**条件性访问**部分。 

此工作簿显示下列已禁用登录趋势：

- 要求 MFA
 
- 需要使用条款

- 需要隐私声明

- 其他


您可以筛选按每个趋势：

- 时间范围

- 应用

- 用户

![库](./media/howto-use-azure-monitor-workbooks/50.png)


对于每个趋势，您获取按应用程序和协议细分。

![库](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登录失败分析

使用**登录名失败分析**工作簿以进行故障排除的错误：

- 登录
- 条件访问策略
- 旧式身份验证。 


若要访问条件性访问的数据登录名，请单击**登录名使用旧版身份验证**中**疑难解答**部分。 

此工作簿显示了以下登录趋势：

- 所有登录

- Success

- 挂起的操作

- 失败


您可以筛选按每个趋势：

- 时间范围

- 应用

- 用户

![库](./media/howto-use-azure-monitor-workbooks/52.png)


若要对登录名进行故障排除，请获取细分：

- 最常见的错误

    ![库](./media/howto-use-azure-monitor-workbooks/53.png)

- 等待用户操作的登录名

    ![库](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)