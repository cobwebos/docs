---
title: 在 Azure Active Directory 门户中配置日志（预览版） |Microsoft Docs
description: Azure Active Directory 门户中的预配活动报表简介
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
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d48aa3ead28ab0b0a22478a0c4183995483058a
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983501"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>在 Azure Active Directory 门户中预配报表（预览）

Azure Active Directory (Azure AD) 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录** - 有关托管应用程序的使用情况和用户登录活动的信息。
    - **审核日志** - [审核日志](concept-audit-logs.md) - 有关用户和组管理、托管应用程序和目录活动的系统活动信息。
    - **设置日志**-提供有关由 Azure AD 预配服务设置的用户、组和角色的系统活动。 

- **安全性** 
    - **风险登录** - [风险登录](concept-risky-sign-ins.md)是指可能由非用户帐户合法拥有者进行的登录尝试。
    - **已标记为存在风险的用户** - [风险用户](concept-user-at-risk.md)是指可能已泄露的用户帐户。

本主题简要介绍预配报表。

## <a name="prerequisites"></a>先决条件

### <a name="who-can-access-the-data"></a>谁可以访问该数据？
* 安全管理员、安全读者、报表读者、应用程序管理员和云应用程序管理员角色中的用户
* 全局管理员


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>访问预配活动需要哪些 Azure AD 许可证？

租户必须具有与之关联的 Azure AD Premium 许可证，才能查看 "所有预配活动" 报表。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 

## <a name="provisioning-logs"></a>“预配”日志

预配日志提供以下问题的答案：

* 已成功在 ServiceNow 中创建哪些组？
* 如何从 Amazon Web Services 导入角色？
* 在 DropBox 中未成功创建哪些用户？

可以通过在[Azure 门户](https://portal.azure.com)中**Azure Active Directory**边栏选项卡的 "**监视**" 部分选择 "**设置日志**" 来访问设置日志。 某些预配记录可能需要长达两个小时才能在门户中显示。

![设置日志](./media/concept-provisioning-logs/access-provisioning-logs.png "设置日志")


设置日志有一个默认列表视图，其中显示：

- 标识
- 操作
- 源系统
- 目标系统
- 状态
- 日期


![默认列](./media/concept-provisioning-logs/default-columns.png "默认列")

单击工具栏中的“列”即可自定义列表视图。

![列选择器](./media/concept-provisioning-logs/column-chooser.png "列选择器")

用于显示其他字段，或者删除已显示的字段。

![可用列](./media/concept-provisioning-logs/available-columns.png "可用列")

选择列表视图中的某个项可获得更详细的信息。

![详细信息](./media/concept-provisioning-logs/steps.png "筛选器")


## <a name="filter-provisioning-activities"></a>筛选预配活动

若要将报告的数据缩小到适合您的级别，可以使用以下默认字段筛选预配数据。 请注意，筛选器中的值基于租户动态填充。 例如，如果租户中没有任何 create 事件，则不会有用于创建的筛选选项。

- 标识
- 操作
- 源系统
- 目标系统
- 状态
- Date


![筛选器](./media/concept-provisioning-logs/filter.png "筛选器")

**标识**筛选器使你能够指定所关注的名称或标识。 此标识可以是用户、组、角色或其他对象。 可以按对象的名称或 ID 进行搜索。 该 ID 因情况而异。 例如，在将 Azure AD 的对象预配到 SalesForce 时，源 ID 是 Azure AD 中用户的对象 ID，而 TargetID 是 Salesforce 中用户的 ID。 从 Workday 预配到 Active Directory 时，源 ID 是 Workday 工作人员员工 ID。 请注意，用户的名称可能并不总是出现在标识列中。 始终会有一个 ID。 

**源系统**筛选器使您能够指定标识的设置位置。 例如，将对象从 Azure AD 设置为 ServiceNow 时，将 Azure AD 源系统。 

**目标系统**筛选器使您能够指定将标识预配到的位置。 例如，在将对象从 Azure AD 设置为 ServiceNow 时，目标系统为 ServiceNow。 

使用“状态”筛选器，可以选择：

- 全部
- Success
- 失败
- 已跳过

**操作**筛选器可用于筛选：

- 创建 
- Update
- DELETE
- 禁用
- 其他

“日期”筛选器用于定义已返回数据的时间范围。  
可能的值有：

- 1 个月
- 7 天
- 30 天
- 24 小时
- 自定义时间范围

选择自定义时间范围时，可以配置开始日期和结束日期。


除了默认字段外，还可以在筛选器中包含以下字段：

- **作业 id** -唯一作业 id 与启用了设置的每个应用程序相关联。   

- **周期 ID** -唯一标识设置周期。 你可以共享此 ID 以支持查找发生此事件的周期。

- **更改 ID** -设置事件的唯一标识符。 你可以共享此 ID 以支持查找预配事件。   



  

## <a name="provisioning-details"></a>预配详细信息 

选择 "设置" 列表视图中的项时，可以获得有关此项的更多详细信息。
详细信息按以下类别分组：

- 步骤

- 故障排除和建议

- 修改的属性

- 总结


![筛选器](./media/concept-provisioning-logs/provisioning-tabs.png "选项卡")



### <a name="steps"></a>步骤

"**步骤**" 选项卡概述了设置对象所需的步骤。 设置对象可能包含以下四个步骤： 

- 导入对象
- 确定对象是否在范围内
- 源和目标之间的匹配对象
- 设置对象（采取措施-这可能是创建、更新、删除或禁用）



![筛选器](./media/concept-provisioning-logs/steps.png "筛选器")


### <a name="troubleshoot-and-recommendations"></a>故障排除和建议


"**疑难解答" 和 "建议**" 选项卡提供错误代码和原因。 仅当出现故障时，才可以使用错误信息。 


### <a name="modified-properties"></a>修改的属性

**修改后的属性**显示旧值和新值。 在没有旧值的情况下，"旧值" 列为空白。 


### <a name="summary"></a>总结

"**摘要**" 选项卡概述源系统和目标系统中的对象发生了什么情况和标识符。 

## <a name="what-you-should-know"></a>要点

- 如果有高级版，Azure 门户将报告的预配数据存储30天，如果有免费版，则存储7天。

- 您可以使用 "更改 ID" 属性作为唯一标识符。 例如，当与产品支持交互时，这很有用。

- 当前没有下载预配数据的选项。

- 当前不支持 log analytics。

- 从应用的上下文中访问预配日志时，它不会自动将事件筛选为审核日志执行方式的特定应用。

## <a name="next-steps"></a>后续步骤

* [检查用户设置的状态](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [为 Azure AD 库应用程序配置用户预配时出现问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


