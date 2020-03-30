---
title: 在仅报告模式下配置条件访问策略 - Azure 活动目录
description: 在条件访问中使用仅报告模式来帮助采用
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295116"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>在仅报告模式下配置条件访问策略（预览）

要在仅报告模式下配置条件访问策略，请执行以下操作：

> [!IMPORTANT]
> 如果组织尚未，[请设置 Azure 监视器与 Azure AD 的集成](#set-up-azure-monitor-integration-with-azure-ad)。 在数据可供审查之前，必须进行此过程。

1. 以条件访问管理员、安全管理员或全局管理员的身份登录到**Azure 门户**。
1. 浏览到**Azure 活动目录** > **安全** > **条件访问**。
1. 选择“新策略”****。
1. 根据需要配置策略条件和所需的授予控制。
1. 在 **"启用"策略**下，将切换设置为**仅报告**模式。
1. 选择 **"保存"**

> [!TIP]
> 您可以将现有策略的**启用策略**状态从 **"打开**"编辑为**仅报告**，但这样做将禁用策略实施。 

查看仅查看 Azure AD 登录日志的结果。

要查看特定登录的仅报告策略的结果，请：

1. 以报表读取器、安全读取器、安全管理员或全局管理员的身份登录到**Azure 门户**。
1. 浏览到**Azure 活动目录** > **登录**。
1. 选择登录或添加筛选器以缩小结果范围。
1. 在 **"详细信息**"抽屉中，选择 **"仅报告（预览）"** 选项卡以查看登录期间评估的策略。

> [!NOTE]
> 下载登录日志时，请选择 JSON 格式以包括仅条件访问报告结果数据。

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>设置 Azure 监视器与 Azure AD 的集成

为了使用新的条件访问见解工作簿查看条件访问策略的聚合影响，必须将 Azure 监视器与 Azure AD 集成并导出登录日志。 设置此集成有两个步骤： 

1. [注册 Azure 监视器订阅并创建工作区](/azure/azure-monitor/learn/quick-create-workspace)。
1. [将登录日志从 Azure AD 导出到 Azure 监视器](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

有关 Azure 监视器定价的详细信息，请参阅[Azure 监视器定价页](https://azure.microsoft.com/pricing/details/monitor/)。 用于估计成本、设置每日上限或自定义数据保留期的资源，请参阅"[使用 Azure 监视器日志管理使用情况和成本](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)"一文。

## <a name="view-conditional-access-insights-workbook"></a>查看条件访问见解工作簿

将 Azure AD 日志与 Azure 监视器集成后，可以使用新的条件访问见解工作簿监视条件访问策略的影响。

1. 以安全管理员或全局管理员身份登录到**Azure 门户**。
1. 浏览到**Azure 活动目录** > **工作簿**。
1. 选择**条件访问见解（预览）。**
1. 从**条件访问策略**下拉列表中选择一个或多个策略。 默认情况下，将选择所有启用的策略。
1. 选择时间范围（如果时间范围超过可用数据集，报表将显示所有可用数据）。 设置**条件访问策略**和**时间范围**参数后，将加载报表。
   1. 可选，搜索单个**用户**或**应用**以缩小报表的范围。
1. 在按用户数或登录数查看时间范围内的数据之间进行选择。
1. 根据 **"数据"视图**，"**影响摘要**"显示所选参数范围内的用户数或登录数，按总数、**成功**、**失败**、**用户操作和****未应用**显示的参数进行分组。 选择磁贴以检查特定结果类型的登录。 
   1. 如果更改了工作簿参数，可以选择保存副本以供将来使用。 选择报表顶部的保存图标，并提供要保存到的名称和位置。
1. 向下滚动以查看每个条件的登录细目。
1. 查看报表底部的**登录详细信息**，以调查按上述选择筛选的单个登录事件。

> [!TIP] 
> 需要向下钻取特定查询或导出登录详细信息？ 选择任何查询右侧的按钮，以在日志分析中打开查询。 选择"导出以导出"以导出到 CSV 或 Power BI。

## <a name="common-problems-and-solutions"></a>常见问题和解决方案

### <a name="why-are-the-queries-in-the-workbook-failing"></a>为什么工作簿中的查询失败？

客户注意到，如果与工作簿关联了错误或多个工作区，查询有时会失败。 要解决此问题，请单击工作簿顶部的 **"编辑"，** 然后单击"设置"齿轮。 选择并删除未与工作簿关联的工作区。 应该只有一个工作区与每个工作簿相关联。

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>为什么条件访问策略下拉参数不包含我的策略？

通过查询 4 小时内的最新登录，填充条件访问策略下拉列表。 如果租户在过去 4 小时内没有任何登录，则下拉列表可能为空。 如果此延迟是一个持续的问题，例如在登录不频繁的小租户中，管理员可以编辑条件访问策略下拉的查询，并将查询时间延长到 4 小时以上。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

有关 Azure AD 工作簿的详细信息，请参阅文章"[如何使用 Azure 活动目录报表的 Azure 监视器工作簿](../reports-monitoring/howto-use-azure-monitor-workbooks.md)"。
