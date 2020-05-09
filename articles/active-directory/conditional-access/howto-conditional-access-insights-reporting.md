---
title: 条件性访问见解和报表工作簿-Azure Active Directory
description: 使用 Azure AD 条件性访问见解和报表工作簿来排查策略问题
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4113328a5de02c36b7285c837bd5314d11e526b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690417"
---
# <a name="conditional-access-insights-and-reporting"></a>条件性访问见解和报告

使用条件性访问见解和报表工作簿，您可以了解条件访问策略在一段时间内的影响。 在登录过程中，可能会应用一个或多个条件性访问策略，如果满足某些授权控制或拒绝访问，则授予访问权限。 由于可以在每次登录期间评估多个条件性访问策略，因此，可以使用 insights 和报表工作簿来检查单个策略的影响或所有策略的子集。  

## <a name="prerequisites"></a>先决条件

若要启用 insights 和报表工作簿，你的租户必须具有 Log Analytics 工作区，以保留登录日志数据。 用户必须具有 Azure AD Premium P1 或 P2 许可证才能使用条件性访问。

以下角色可以访问 insights 和报告：  

- 条件访问管理员 
- 安全读取者 
- 安全管理员 
- 全局读取者 
- 全局管理员 

用户还需要下列 Log Analytics 工作区角色之一：  

- 读取器 
- 监视读取器 
- Log Analytics 读取器 
- 参与者  
- “所有者” 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>将 Azure AD 中的登录日志流式传输到 Azure Monitor 日志 

如果尚未将 Azure AD 日志与 Azure Monitor 日志相集成，则在加载工作簿之前，需要执行以下步骤：  

1. [在 Azure Monitor 中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
1. 将[Azure AD 日志与 Azure Monitor 日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

## <a name="how-it-works"></a>工作原理 

访问 insights 和报表工作簿：  

1. 登录 **Azure 门户**。
1. 浏览到**Azure Active Directory** > **安全** > **条件性访问** > **见解和报表**。

### <a name="get-started-select-parameters"></a>入门：选择参数 

通过 insights 和报告仪表板，你可以查看一个或多个条件性访问策略在指定时间段内的影响。 首先设置工作簿顶部的每个参数。 

![Azure 门户中的条件性访问见解和报告仪表板](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**条件性访问策略**：选择一个或多个条件性访问策略以查看其合并影响。 策略分为两组：启用和仅报告策略。 默认情况下，选择所有已启用的策略。 这些启用的策略是当前在租户中强制执行的策略。  

**时间范围**：选择一个时间范围，范围为4小时到最大为90天。 如果你选择的时间范围晚于将 Azure AD 日志与 Azure Monitor 集成时的时间范围，则只会显示集成时间之后的登录。  

**用户**：默认情况下，仪表板显示对所有用户所选策略的影响。 若要按单个用户进行筛选，请在 "文本" 字段中键入用户的名称。 若要按所有用户进行筛选，请在文本字段中键入 "所有用户" 或将参数保留为空。 

**应用**：默认情况下，仪表板显示对所有应用所选策略的影响。 若要按单个应用程序进行筛选，请在 "文本" 字段中键入应用程序的名称。 若要按所有应用进行筛选，请在文本字段中键入 "所有应用"，或将参数保留为空。 

"**数据视图**"：选择是否希望面板根据用户数或登录数来显示结果。单个用户在给定时间范围内，可能会有数百个登录到多个具有多个不同结果的应用。 如果你选择 "数据" 视图作为用户，则用户可以同时包含在 "成功" 和 "失败" 计数中（例如，如果有10个用户，其中8个用户可能在过去30天内获得成功结果，9个用户可能在过去30天内发生故障。）

## <a name="impact-summary"></a>影响摘要 

设置参数后，将会加载影响摘要。 此摘要显示在评估所选策略时，时间范围内有多少用户或登录导致 "成功"、"失败"、"需要用户操作" 或 "未应用"。  

![条件访问工作簿中的影响摘要](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**总计**：评估至少一个选定策略的时间段内的用户或登录数。

**成功**：在所选策略的合并结果为 "成功" 或 "仅报告：成功" 的时间段内，用户或登录的次数。

**失败**：在至少一个选定策略的结果为 "失败" 或 "仅报告：失败" 的时间段内，用户或登录的次数。

**需要用户操作**：所选策略的组合结果为 "仅报告：需要用户操作" 的时间段内的用户或登录数。 当交互式授权控件（例如仅限报表的条件性访问策略需要多重身份验证）时，用户操作是必需的。 由于仅限报表策略不会强制实施交互式授权控件，因此无法确定成功或失败。  

**未应用：未**应用任何选定策略的时间段内的用户或登录数。

### <a name="understanding-the-impact"></a>了解影响 

![每个条件和状态的工作簿细目](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

查看用户的细目或每个条件的登录。 可以通过选择工作簿顶部的 "摘要" 磁贴来筛选特定结果（例如 "成功" 或 "失败"）的登录。 可以查看每个条件访问条件的登录细目：设备状态、设备平台、客户端应用、位置、应用程序和登录风险。  

## <a name="sign-in-details"></a>登录详细信息 

![工作簿登录详细信息](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

还可以通过在仪表板底部搜索登录来调查特定用户的登录。 左侧的查询会显示最常见的用户。 选择用户会将查询筛选到右侧。  

## <a name="troubleshooting"></a>疑难解答

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>为什么工作簿需要很长时间才能加载？  

根据所选的时间范围和租户的大小，工作簿可能会评估数量极大的登录事件。 对于大型租户，登录量可能超出 Log Analytics 的查询能力。 请尝试缩短时间范围到4小时，以查看工作簿是否已加载。  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>加载几分钟后，为何工作簿返回零个结果？ 

当登录量超过 Log Analytics 的查询容量时，工作簿将返回零个结果。 请尝试缩短时间范围到4小时，以查看工作簿是否已加载。  

### <a name="can-i-save-my-parameter-selections"></a>是否可以保存参数选择？  

可以通过转到**Azure Active Directory** > **工作簿** > **条件性访问见解和报告**，将参数选择保存在工作簿的顶部。 在这里，你将找到工作簿模板，你可以在其中编辑工作簿并将副本保存到你的工作区，包括 "**我的报表**" 或 "**共享报表**" 中的参数选择。 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>是否可以通过其他查询来编辑和自定义工作簿？ 

可以通过转到**Azure Active Directory** > **工作簿** > 的**条件性访问见解和报告**来编辑和自定义工作簿。 在这里，你将找到工作簿模板，你可以在其中编辑工作簿并将副本保存到你的工作区，包括 "**我的报表**" 或 "**共享报表**" 中的参数选择。 若要开始编辑查询，请单击工作簿顶部的 "**编辑**"。  
 
## <a name="next-steps"></a>后续步骤

[仅限条件访问报表模式](concept-conditional-access-report-only.md)
