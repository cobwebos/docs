---
title: 条件访问见解和报告工作簿 - Azure Active Directory
description: 使用 Azure AD 条件访问见解和报告工作簿来排除策略故障
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c2364eae0d04da8f8e6fe38ae80db7adb8666ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049411"
---
# <a name="conditional-access-insights-and-reporting"></a>条件访问见解和报告

使用条件访问见解和报告工作簿，可以了解条件访问策略在一定时间段内在组织中的影响。 在登录期间，可以应用一个或多个条件访问策略；如果满足某些授权控制，就会授予访问权限，否则就会拒绝访问。 由于在每次登录期间可能会评估多个条件访问策略，因此见解和报告工作簿可便于检查单个策略或所有策略的子集的影响。  

## <a name="prerequisites"></a>先决条件

你的租户必须有 Log Analytics 工作区来保留登录日志数据，才能启用见解和报告工作簿。 用户必须有 Azure AD Premium P1 或 P2 许可证，才能使用条件访问。

以下角色可以访问见解和报告：  

- 条件访问管理员 
- 安全读取者 
- 安全管理员 
- 全局读取者 
- 全局管理员 

用户还必须具有下列 Log Analytics 工作区角色之一：  

- 参与者  
- “所有者” 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>将 Azure AD 中的登录日志流式传输到 Azure Monitor 日志 

如果还没有将 Azure AD 日志与 Azure Monitor 日志集成，则需要在加载工作簿之前按照以下步骤操作：  

1. [在 Azure Monitor 中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
1. [将 Azure AD 日志与 Azure Monitor 日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

## <a name="how-it-works"></a>工作原理 

若要访问见解和报告工作簿，请执行以下操作：  

1. 登录 **Azure 门户**。
1. 依次转到“Azure Active Directory” > “安全性” > “条件访问” > “见解和报告”。

### <a name="get-started-select-parameters"></a>入门：选择参数 

通过见解和报告仪表板，可以查看一个或多个条件访问策略在指定时间段内的影响。 首先，设置工作簿顶部的每个参数。 

![Azure 门户中的条件访问见解和报告仪表板](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

条件访问策略：选择一个或多个条件访问策略，以查看其综合影响。 策略分为两组：“已启用”和“仅限报告”策略。 默认选择的是所有“已启用”策略。 这些“已启用”策略是当前在租户中强制执行的策略。  

时间范围：选择要追溯的时间范围，介于 4 个小时到 90 天之间。 如果你选择的时间范围早于你将 Azure AD 日志与 Azure Monitor 集成的时间，则只会显示在集成时间后发生的登录。  

**用户**：默认情况下，仪表板显示所选策略对全部用户的影响。 若要按单个用户进行筛选，请在文本字段中键入用户名。 若要按所有用户进行筛选，请在文本字段中键入“所有用户”或将参数留空。 

应用：默认情况下，仪表板显示所选策略对所有应用的影响。 若要按单个应用进行筛选，请在文本字段中键入应用名称。 若要按所有应用进行筛选，请在文本字段中键入“所有应用”或将参数留空。 

数据视图：选择是否希望仪表板依据用户数或登录数来显示结果。在给定的时间范围内，单个用户可能有数百个不同结果的应用登录。 如果选择依据用户数的数据视图，则可以在“成功”和“失败”计数中同时包含一个用户（例如，如果有 10 个用户，其中 8 个可能在过去 30 天内成功登录，其中 9 个可能在过去 30 天内登录失败。）

## <a name="impact-summary"></a>影响摘要 

一旦设置了参数，影响摘要就会加载。 此摘要显示在评估所选策略时，在时间范围内有多少用户或登录的结果为“成功”、“失败”、“需要用户操作”或“未应用”。  

![条件访问工作簿中的影响摘要](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**：在评估至少一个所选策略的时间段内的用户数或登录数。

**成功**：在时间段内所选策略的合并结果为“成功”或“仅限报告:成功”的用户数或登录数。

**失败**：在时间段内至少一个所选策略的结果为“失败”或“仅限报告:失败”的用户数或登录数。

需要用户操作：在时间段内所选策略的合并结果为“仅限报告:需要用户操作”的用户数或登录数。 当“仅限报告”条件访问策略需要交互式授权控制（如多重身份验证）时，需要用户操作。 由于交互式授权控制不是由“仅限报告”策略强制执行的，因此无法确定成功或失败。  

**未应用**：在时间段内未应用任何所选策略的用户数或登录数。

### <a name="understanding-the-impact"></a>了解影响 

![按条件和状态的工作簿明细](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

查看每个条件对应的用户或登录明细。 可以通过选择工作簿顶部的摘要磁贴之一来筛选特定登录结果（例如，“成功”或“失败”）。 可以查看每个条件访问条件对应的登录明细：设备状态、设备平台、客户端应用、位置、应用和登录风险。  

## <a name="sign-in-details"></a>登录详细信息 

![工作簿登录详细信息](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

也可以通过在仪表板底部搜索登录来调查特定用户的登录。 左侧的查询显示最常登录的用户。 选择用户会向右筛选查询。  

> [!NOTE]
> 下载登录日志时，请选择 "JSON 格式" 以包含仅限条件访问报告结果数据。

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>在仅报告模式下配置条件访问策略

若要在仅限报表模式下配置条件访问策略，请执行以下操作：

1. 以条件访问管理员、安全管理员或全局管理员身份登录到 **Azure 门户** 。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择现有策略或创建新策略。
1. 在 " **启用策略** " 下，将切换设置为 **仅报告** 模式。
1. 选择“保存”

> [!TIP]
> 将中现有策略的 " **启用" 策略** 状态 **编辑为 "** **仅限报表** " 将禁用现有策略强制。 

## <a name="troubleshooting"></a>疑难解答

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>由于权限错误导致查询失败的原因是什么？

若要访问工作簿，需要有正确的 Azure AD 权限以及 Log Analytics 工作区权限。 若要通过运行示例 Log Analytics 查询来测试你是否有适当的工作区权限，请完成以下步骤：

1. 登录 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “日志”。
1. 将 `SigninLogs` 键入查询框中，然后选择“运行”。
1. 如果查询没有返回任何结果，则工作区可能配置不正确。 

![排查查询失败的问题](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

若要详细了解如何将 Azure AD 登录日志流式传输到 Log Analytics 工作区，请参阅[将 Azure AD 日志与 Azure Monitor 日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)一文。

### <a name="why-are-the-queries-in-the-workbook-failing"></a>为什么工作簿中的查询未通过？

客户注意到，如果错误或多个工作区与工作簿相关联，查询有时会失败。 若要解决此问题，请单击工作簿顶部的 " **编辑** "，然后设置齿轮。 选择并删除与工作簿无关的工作区。 应该只有一个工作区与每个工作簿关联。

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>为什么条件访问策略参数为空？

策略列表是通过查看针对最新登录事件进行评估的策略来生成的。 如果租户中没有最新的登录，则可能需要等待几分钟，以便工作簿加载条件访问策略的列表。 这可能会在配置 Log Analytics 之后立即发生，也可能需要更长的时间（如果租户没有最新的登录活动）。

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>为什么工作簿需要很长时间才能加载？  

工作簿可能会评估数量极大的登录事件，具体视所选的时间范围和租户大小而定。 对于大型租户，登录量可能会超出 Log Analytics 的查询容量。 请尝试将时间范围缩短至 4 个小时，看看工作簿是否加载。  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>加载几分钟后，为什么工作簿不返回任何结果？ 

如果登录量超出 Log Analytics 的查询容量，工作簿就不会返回任何结果。 请尝试将时间范围缩短至 4 个小时，看看工作簿是否加载。  

### <a name="can-i-save-my-parameter-selections"></a>我能否保存所选的参数？  

若要保存你在工作簿顶部选择的参数，可以依次转到“Azure Active Directory” > “工作簿” > “条件访问见解和报告”。 在这里，你将找到工作簿模板，可以在其中编辑工作簿，并将副本（包括所选的参数）保存到“我的报告”或“共享报告”中的工作区。 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>我能否通过其他查询来编辑和自定义工作簿？ 

若要编辑和自定义工作簿，可以依次转到“Azure Active Directory” > “工作簿” > “条件访问见解和报告”。 在这里，你将找到工作簿模板，可以在其中编辑工作簿，并将副本（包括所选的参数）保存到“我的报告”或“共享报告”中的工作区。 若要开始编辑查询，请单击工作簿顶部的“编辑”。  
 
## <a name="next-steps"></a>后续步骤

- [条件访问“仅限报告”模式](concept-conditional-access-report-only.md)

- 有关 Azure AD 工作簿的详细信息，请参阅 [如何使用 Azure Monitor 工作簿 Azure Active Directory 报表](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。

- [条件访问常见策略](concept-conditional-access-policy-common.md)
