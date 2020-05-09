---
title: 在仅报告模式下配置条件访问策略-Azure Active Directory
description: 在条件访问中使用仅报告模式，有助于采用
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb13eb699f770d4d7f896b8179888d1514b1c46d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690369"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>在仅报告模式下配置条件访问策略

若要在仅限报表模式下配置条件访问策略，请执行以下操作：

> [!IMPORTANT]
> 如果你的组织尚未这样做，请[设置与 Azure AD 的 Azure Monitor 集成](#set-up-azure-monitor-integration-with-azure-ad)。 必须先进行此过程，然后才能查看数据。

1. 以条件访问管理员、安全管理员或全局管理员身份登录到**Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 根据需要配置策略条件和所需的授权控制。
1. 在 "**启用策略**" 下，将切换设置为**仅报告**模式。
1. 选择“保存” 

> [!TIP]
> 你可以将中的现有策略的 "**启用" 策略**状态编辑为**仅报告** **，但**这样做将禁用策略强制实施。 

仅查看报表 Azure AD 登录日志中的结果。

查看特定登录的仅报告策略的结果：

1. 以报表读者、安全读者、安全管理员或全局管理员身份登录到**Azure 门户**。
1. 浏览到“Azure Active Directory”   > “登录”  。
1. 选择 "登录" 或添加筛选器以缩小结果范围。
1. 在 "**详细信息**" 抽屉中，选择 "**仅限报告**" 选项卡，以查看登录期间评估的策略。

> [!NOTE]
> 下载登录日志时，请选择 "JSON 格式" 以包含仅限条件访问报告结果数据。

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>设置与 Azure AD 的 Azure Monitor 集成

若要使用新的条件性访问见解工作簿来查看条件访问策略的聚合影响，必须将 Azure Monitor 与 Azure AD 集成并导出登录日志。 设置此集成的步骤有两个： 

1. [注册 Azure Monitor 订阅并创建工作区](/azure/azure-monitor/learn/quick-create-workspace)。
1. [将登录日志从 Azure AD 导出到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

有关 Azure Monitor 定价的详细信息，请参阅[Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)。 用于估算成本、设置每日上限或自定义数据保持期的资源，请参阅[使用 Azure Monitor 日志管理使用情况和成本](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)一文。

## <a name="view-conditional-access-insights-workbook"></a>查看条件性访问见解工作簿

将 Azure AD 日志与 Azure Monitor 集成后，可以使用新的条件性访问见解工作簿来监视条件访问策略的影响。

1. 以安全管理员或全局管理员身份登录到**Azure 门户**。
1. 浏览到**Azure Active Directory** > **工作簿**。
1. 选择 "**条件访问见解**"。
1. 从 "**条件性访问策略**" 下拉列表中选择一个或多个策略。 默认情况下，选择所有已启用的策略。
1. 选择时间范围（如果时间范围超过可用的数据集，则报表将显示所有可用数据）。 设置**条件访问策略**和**时间范围**参数后，报表将加载。
   1. （可选）搜索各个**用户**或**应用**以缩小报表范围。
1. 选择 "时间范围" 中的数据，按用户数或登录数查看。
1. 根据**数据视图**，**影响摘要**显示所选参数作用域中的用户或登录的数量，按总数量、**成功**、**失败**、**需要用户操作**和**不应用**。 选择一个磁贴来检查特定结果类型的登录。 
   1. 如果更改了工作簿参数，则可以选择保存副本以供将来使用。 选择报表顶部的 "保存" 图标，并提供要保存到的名称和位置。
1. 向下滚动以查看每个条件的登录细目。
1. 查看报表底部的**登录详细信息**，调查按上述选项筛选的单个登录事件。

> [!TIP] 
> 需要向下钻取特定查询或导出登录详细信息？ 选择任意查询右侧的按钮，在 Log Analytics 中打开查询。 选择 "导出" 导出到 CSV 或 Power BI。

## <a name="common-problems-and-solutions"></a>常见问题和解决方案

### <a name="why-are-the-queries-in-the-workbook-failing"></a>为什么工作簿中的查询未通过？

客户注意到，如果错误或多个工作区与工作簿相关联，查询有时会失败。 若要解决此问题，请单击工作簿顶部的 "**编辑**"，然后设置齿轮。 选择并删除与工作簿无关的工作区。 应该只有一个工作区与每个工作簿关联。

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>为什么条件访问策略下拉列表参数不包含我的策略？

"条件性访问策略" 下拉列表是通过在4小时内查询最新登录的。 如果租户在过去4小时内没有任何登录，则下拉列表可能为空。 如果此延迟是持久的问题（例如在具有很少登录的小型租户中），管理员可以编辑 "条件性访问策略" 下拉列表中的查询，并将查询时间延长到超过4小时的时间。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

有关 Azure AD 工作簿的详细信息，请参阅[如何使用 Azure Monitor 工作簿 Azure Active Directory 报表](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。
