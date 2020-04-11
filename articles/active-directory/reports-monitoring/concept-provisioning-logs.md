---
title: Azure 活动目录门户中的预配日志（预览） |微软文档
description: Azure 活动目录门户中预配活动报表简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113321"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>在 Azure 活动目录门户中预配报表（预览）

Azure Active Directory (Azure AD) 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录** - 有关托管应用程序的使用情况和用户登录活动的信息。
    - **审核日志** - [审核日志](concept-audit-logs.md)提供有关用户和组管理、托管应用程序和目录活动的系统活动信息。
    - **预配日志**- 提供有关 Azure AD 预配服务预配的用户、组和角色的系统活动。 

- **安全性** 
    - **风险**登录 -[风险登录](concept-risky-sign-ins.md)是登录尝试的指示器，该尝试可能由不是用户帐户的合法所有者的人员执行。
    - **已标记为存在风险的用户** - [风险用户](concept-user-at-risk.md)是指可能已泄露的用户帐户。

本主题概述了预配报告。

## <a name="prerequisites"></a>先决条件

### <a name="who-can-access-the-data"></a>谁可以访问该数据？
* 安全管理员、安全读取器、报表阅读器、应用程序管理员和云应用程序管理员角色中的用户
* 全局管理员


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>访问预配活动需要哪些 Azure AD 许可证？

租户必须具有与其关联的 Azure AD 高级许可证才能查看所有向上预配活动报告。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 

## <a name="provisioning-logs"></a>“预配”日志

预配日志提供以下问题的答案：

* 在 ServiceNow 中成功创建哪些组？
* 如何从亚马逊 Web 服务导入角色？
* 在 DropBox 中未成功创建哪些用户？

通过在[Azure 门户](https://portal.azure.com)中的**Azure 活动目录**边栏选项卡的**监视**部分中选择**预配日志**，可以访问预配日志。 某些预配记录最多可能需要两个小时才能显示在门户中。

![“预配”日志](./media/concept-provisioning-logs/access-provisioning-logs.png "“预配”日志")


预配日志具有默认列表视图，该视图显示：

- 标识
- 操作
- 源系统
- 目标系统
- 状态
- 日期


![默认列](./media/concept-provisioning-logs/default-columns.png "默认列")

您可以通过单击工具栏中的**列**来自定义列表视图。

![列选择器](./media/concept-provisioning-logs/column-chooser.png "列选择器")

用于显示其他字段，或者删除已显示的字段。

![可用列](./media/concept-provisioning-logs/available-columns.png "可用列")

选择列表视图中的某个项可获得更详细的信息。

![详细信息](./media/concept-provisioning-logs/steps.png "筛选器")


## <a name="filter-provisioning-activities"></a>筛选器预配活动

您可以筛选预配数据。 某些筛选器值会根据您的租户动态填充。 例如，如果租户中没有任何创建事件，则创建时没有筛选器选项。
在默认视图中，您可以选择以下筛选器：

- 标识
- Date
- 状态
- 操作


![筛选器](./media/concept-provisioning-logs/default-filter.png "筛选器")

通过 **"标识"** 筛选器，您可以指定您关心的名称或标识。 此标识可以是用户、组、角色或其他对象。 您可以按对象的名称或 ID 进行搜索。 ID 因方案而异。 例如，将对象从 Azure AD 预配到 SalesForce 时，源 ID 是 Azure AD 中用户的对象 ID，而 TargetID 是 Salesforce 中用户的 ID。 从工作日预配到活动目录时，源 ID 是工作日员工 ID。 请注意，用户的姓名可能并不总是存在于"标识"列中。 始终有一个 ID。 


“日期”筛选器用于定义已返回数据的时间范围。****  
可能的值包括：

- 1 个月
- 7 天
- 30 天
- 24 小时
- 自定义时间范围

选择自定义时间范围时，可以配置开始日期和结束日期。


使用“状态”**** 筛选器，可以选择：

- 全部
- Success
- 失败
- 已跳过



"**操作"** 筛选器允许您筛选：

- 创建 
- 更新
- 删除
- 禁用
- 其他

此外，除了默认视图的筛选器，还可以设置以下筛选器：

- 作业 ID
- 循环 ID
- 更改 ID
- 源 ID
- 目标 ID
- 应用程序


![选取字段](./media/concept-provisioning-logs/add-filter.png "选取字段")


- **作业 ID** - 与已为其启用预配的每个应用程序相关联的唯一作业 ID。   

- **周期 ID** - 唯一标识预配周期。 您可以共享此 ID 以支持查找发生此事件的周期。

- **更改 ID** - 预配事件的唯一标识符。 您可以共享此 ID 以支持查找预配事件。   


- **源系统**- 使您能够指定从何处预配标识。 例如，将对象从 Azure AD 预配到 ServiceNow 时，源系统是 Azure AD。 

- **目标系统**- 使您能够指定标识预配到的位置。 例如，将对象从 Azure AD 预配到 ServiceNow 时，目标系统是 ServiceNow。 

- **应用程序**- 使您能够仅显示具有包含特定字符串的显示名称的应用程序的记录。

 

## <a name="provisioning-details"></a>预配详细信息 

在预配列表视图中选择项目时，将获取有关此项的更多详细信息。
详细信息根据以下类别进行分组：

- 步骤

- 故障排除和建议

- 修改的属性

- 总结


![筛选器](./media/concept-provisioning-logs/provisioning-tabs.png "制表符")



### <a name="steps"></a>步骤

"**步骤**"选项卡概述了预配对象的步骤。 预配对象可以包含四个步骤： 

- 导入对象
- 确定对象是否在作用域中
- 在源和目标之间匹配对象
- 预配对象（执行操作 - 这可能是创建、更新、删除或禁用）



![筛选器](./media/concept-provisioning-logs/steps.png "筛选器")


### <a name="troubleshoot-and-recommendations"></a>故障排除和建议


**疑难解答和建议**选项卡提供错误代码和原因。 错误信息仅在发生故障时可用。 


### <a name="modified-properties"></a>修改的属性

**修改后的属性**显示旧值和新值。 如果没有旧值，则旧值列为空。 


### <a name="summary"></a>总结

**摘要**选项卡提供源和目标系统中对象的已发生的情况和标识符的概述。 

## <a name="what-you-should-know"></a>要点

- Azure 门户存储 30 天的预配数据（如果有高级版）和 7 天（如果有免费版本）。

- 您可以将更改 ID 属性用作唯一标识符。 例如，在与产品支持交互时，这很有帮助。

- 当前没有下载预配数据的选项。

- 目前不支持日志分析。

- 当您从应用的上下文中访问预配日志时，它不会自动将事件筛选到特定应用，就像审核日志那样。

## <a name="error-codes"></a>错误代码

使用下表更好地了解如何解决在预配日志中可能发现的错误。 对于缺少的任何错误代码，请使用此页面底部的链接提供反馈。 

|错误代码|说明|
|---|---|
|冲突，进入冲突|更正 Azure AD 或应用程序中的冲突属性值，或者如果冲突用户帐户应匹配并接管，则查看匹配的属性配置。 有关配置匹配属性的详细信息，请查看以下[文档](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。|
|TooManyRequests|目标应用拒绝此更新用户尝试，因为它超载并接收了太多请求。 没什么可做的。 此尝试将自动停用。 微软也被告知此问题。|
|InternalServerError |目标应用返回了意外错误。 目标应用程序可能存在阻止此功能的工作的服务问题。 此尝试将在 40 分钟内自动停用。|
|权利不足、方法不允许、不允许、未经授权| Azure AD 能够对目标应用程序进行身份验证，但无权执行更新。 请查看目标应用程序以及相应的应用程序[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)提供的任何说明。|
|不可处理实体|目标应用程序返回了意外响应。 目标应用程序的配置可能不正确，或者目标应用程序可能存在阻止此功能的工作的服务问题。|
|Web 例外协议错误 |连接到目标应用程序时发生 HTTP 协议错误。 没什么可做的。 此尝试将在 40 分钟内自动停用。|
|无效锚定|以前由预配服务创建或匹配的用户不再存在。 检查以确保用户存在。 要强制重新匹配所有用户，请使用 MS 图形 API[重新启动作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 请注意，重新启动预配将触发初始周期，这可能需要时间才能完成。 它还删除预配服务用于操作的缓存，这意味着租户中的所有用户和组都必须再次进行评估，并且可能会删除某些预配事件。|
|NotImplemented | 目标应用返回了意外响应。 应用的配置可能不正确，或者目标应用可能存在服务问题，无法解决此问题。 请查看目标应用程序以及相应的应用程序[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)提供的任何说明。 |
|必填字段缺失，缺失值 |无法创建用户，因为缺少所需的值。 更正源记录中缺少的属性值，或查看匹配的属性配置以确保不省略所需的字段。 [了解有关](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)配置匹配属性的更多详细信息。|
|未找到架构属性 |无法执行操作，因为指定了目标应用程序中不存在的属性。 请参阅有关属性自定义[的文档](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，并确保配置正确。|
|InternalError |Azure AD 预配服务中发生了内部服务错误。 没什么可做的。 此尝试将在 40 分钟内自动重试。|
|InvalidDomain |由于包含无效域名的属性值，无法执行该操作。 更新用户的域名或将其添加到目标应用程序中的允许列表中。 |
|超时 |无法完成该操作，因为目标应用程序需要很长时间才能响应。 没什么可做的。 此尝试将在 40 分钟内自动重试。|
|超出许可证限制|无法在目标应用程序中创建用户，因为此用户没有可用的许可证。 为目标应用程序获取其他许可证，或者查看用户分配和属性映射配置，以确保使用正确的属性分配正确的用户。|
|重复目标条目  |无法完成该操作，因为使用已配置的匹配属性找到了目标应用程序中的多个用户。 从目标应用程序中删除重复的用户，或者按照[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)所述重新配置属性映射。|
|重复源条目 | 无法完成该操作，因为使用已配置的匹配属性找到多个用户。 删除重复用户，或按照[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)所述重新配置属性映射。|

## <a name="next-steps"></a>后续步骤

* [检查用户预配的状态](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [为 Azure AD 库应用程序配置用户预配时遇到的问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


