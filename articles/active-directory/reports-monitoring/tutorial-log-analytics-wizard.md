---
title: 在 Azure AD 中配置 Log Analytics 向导 | Microsoft Docs
description: 了解如何配置 Log Analytics。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 337b75412751fb15e3fc1746666a8efb385a8939
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853771"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>教程：配置 Log Analytics 向导


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 为审核和登录日志配置 Log Analytics 工作区
> * 使用 Kusto 查询语言 (KQL) 运行查询
> * 在使用特定帐户时创建发送警报的警报规则
> * 使用快速启动模板创建自定义工作簿
> * 将查询添加到现有工作簿模板

## <a name="prerequisites"></a>先决条件

- 至少包含一个 P1 许可管理员的 Azure 订阅。如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。

- Azure AD 租户。

- 一个是 Azure AD 租户的全局管理员或安全管理员的用户。 


通过以下文章熟悉相关知识：

- [教程：从 Azure 资源收集和分析资源日志](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs)

- [如何将活动日志与 Log Analytics 集成](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [在 Azure AD 中管理紧急访问帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

- [KQL 快速参考](https://docs.microsoft.com/azure/data-explorer/kql-quick-reference)

- [Azure Monitor 工作簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)



## <a name="configure-a-workspace"></a>配置工作区 

此过程概述了如何为审核和登录日志配置 Log Analytics 工作区。
配置 Log Analytics 工作区包括两个主要步骤：
 
1. 创建 Log Analytics 工作区
2. 设置诊断设置

配置工作区： 


1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索 Log Analytics 工作区。

    ![搜索资源服务和文档](./media/tutorial-log-analytics-wizard/search-services.png)

3. 在“Log Analytics 工作区”页上，单击“添加”。

    ![添加](./media/tutorial-log-analytics-wizard/add.png)

4.  在“创建 Log Analytics 工作区”页上，执行以下步骤：

    ![创建 Log Analytics 工作区](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. 选择订阅。

    2. 选一个择资源组。
 
    3. 在“名称”文本框中，键入名称（例如：MytestWorkspace1）。

    4. 选择你的区域。

5. 单击“查看 + 创建”。

    ![查看 + 创建](./media/tutorial-log-analytics-wizard/review-create.png)

6. 单击“创建”并等待部署成功。 可能需要刷新页面才能看到新的工作区。

    ![创建](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. 搜索“Azure Active Directory”。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. 在“监视”部分，单击“诊断设置” 。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. 在“诊断设置”页上，单击“添加诊断设置” 。

    ![添加诊断设置](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. 在“诊断设置”页上，执行以下步骤：

    ![选择诊断设置](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. 在“类别详细信息”下，选择“AuditLogs”和“SigninLogs”  。

    2. 在“目标详细信息”下，选择“发送到 Log Analytics”，然后选择新的 Log Analytics 工作区 。 
   
    3. 单击“ **保存**”。 

## <a name="run-queries"></a>运行查询  

此过程显示了如何使用 Kusto 查询语言 (KQL) 运行查询。


运行查询：


1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“Azure Active Directory”。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在“监视”部分，单击“日志” 。

4. 在“日志”页上，单击“入门” 。

5. 在“搜索”文本框中，键入查询。

6. 单击 **“运行”** 。  


### <a name="kql-query-examples"></a>KQL 查询示例

从输入数据中随机选取 10 个条目：

`SigninLogs | take 10`

查看条件访问成功的登录

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


计算已有多少次成功

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count'


用户按天累计的成功登录次数：

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


查看用户在特定时间段内执行特定操作的次数：

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


根据操作名称透视结果

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


使用内部联接合并审核和登录日志：

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated , UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


按客户端应用类型查看登录数：

`SigninLogs | summarize count() by ClientAppUsed`

按天对登录进行计数：

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

随机选取 5 个条目，并在结果中投射出你希望看到的列：

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


选取以降序排列的前 5 个条目，并投射出你希望看到的列

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

通过将这些值合并到其他两列来创建新的列：

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>创建警报规则  

此过程显示了如何在使用 breakglass 帐户时发送警报。

创建警报规则：


1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“Azure Active Directory”。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在“监视”部分，单击“日志” 。

4. 在“日志”页上，单击“入门” 。

5. 在“搜索”文本框中，键入：`SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. 单击 **“运行”** 。  

7. 在工具栏中，单击“新建警报规则”。

    ![新建警报规则](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. 在“创建警报规则”页上，验证作用域是否正确。

9. 在“条件”下，单击：“每当平均自定义日志搜索大于 <logic undefined> 个计数时”

    ![默认条件](./media/tutorial-log-analytics-wizard/default-condition.png)

10. 在“配置信号逻辑”页上的“警报逻辑”部分中，执行以下步骤 ：

    ![警报逻辑](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. 对于“依据”，选择“结果数” 。

    2. 对于“运算符”，选择“大于” 。

    3. 对于“阈值”，选择“0” 。 

11. 在“配置信号逻辑”页上的“计算依据”部分中，执行以下步骤 ：

    ![计算基于](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. 对于“时段(分钟)”，选择“5” 。

    2. 对于“频率(分钟)”，选择“5” 。

    3. 单击“Done”（完成） 。 

12. 在“操作组”下，单击“选择操作组” 。 

    ![操作组](./media/tutorial-log-analytics-wizard/action-group.png)

13. 在“选择要附加到此警报规则的操作组”上，单击“创建操作组” 。 

    ![创建操作组](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. 在“创建操作组”页上，执行以下步骤：

    ![实例详细信息](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. 在“操作组名称”文本框中，键入“我的操作组” 。

    2. 在“显示名称”文本框中，键入“我的操作” 。

    3. 单击“查看 + 创建”。 

    4. 单击“创建”。


15. 在“自定义操作”下，执行以下步骤：

    ![自定义操作](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. 选择“电子邮件主题”。

    2. 在“主题行”文本框中，键入：`Breakglass account has been used`

16. 在“警报规则详细信息”下，执行以下步骤：

    ![警报规则详细信息](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. 在“警报规则名称”文本框中，键入：`Breakglass account`

    2. 在“说明”文本框中，键入：`Your emergency access account has been used`

17. 单击“创建警报规则”。   


## <a name="create-a-custom-workbook"></a>创建自定义工作簿

此过程显示了如何使用快速启动模板创建新的工作簿。




1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“Azure Active Directory”。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在“监视”部分，单击“工作簿” 。

    ![工作簿](./media/tutorial-log-analytics-wizard/workbooks.png)

4. 在“快速入门”部分中，单击“空” 。

    ![快速入门](./media/tutorial-log-analytics-wizard/quick-start.png)

5. 单击“添加”。

    ![添加工作簿](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. 单击“添加文本”。

    ![添加文本](./media/tutorial-log-analytics-wizard/add-text.png)


7. 在文本框中，键入：`# Client apps used in the past week`，然后单击“完成编辑”。

    ![工作簿文本](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. 在新的工作簿中，单击“添加”，然后单击“添加查询” 。

    ![添加查询](./media/tutorial-log-analytics-wizard/add-query.png)

9. 在查询文本框中，键入：`SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. 单击 **“运行查询”**。

    ![运行查询](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. 在工具栏中的“可视化效果”下，单击“饼图” 。

    ![饼图](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. 单击“完成编辑”。

    ![完成编辑](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>将查询添加到工作簿模板  

此过程显示了如何将查询添加到现有工作簿模板。 该示例基于一个查询，该查询显示条件访问成功与失败的分布情况。


1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“Azure Active Directory”。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在“监视”部分，单击“工作簿” 。

    ![工作簿](./media/tutorial-log-analytics-wizard/workbooks.png)

4. 在“条件访问”部分中，单击“条件访问见解和报表” 。

    ![条件访问模板](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. 在工具栏中，单击“编辑”。

    ![条件访问模板](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. 在工具栏中，单击三个点，然后单击“添加”，再单击“添加查询” 。

    ![添加工作簿查询](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. 在查询文本框中，键入：`SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. 单击 **“运行查询”**。

    ![运行查询](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. 单击“时间范围”，然后选择“在查询中设置” 。

10. 单击“可视化效果”，然后选择“条形图” 。 

11. 单击“高级设置”，键入 `Conditional Access status over the last 20 days` 作为图表标题，然后单击“完成编辑” 。 

    ![设置图表标题](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>后续步骤

继续学习下一篇文章，了解如何使用 Azure 门户管理设备标识。
> [!div class="nextstepaction"]
> [Monitoring](overview-monitoring.md)
