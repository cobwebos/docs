---
title: Azure Active Directory 门户中的“审核活动”报告 | Microsoft Docs
description: Azure Active Directory 门户中的审核活动报告简介
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 883f521040c67cb8fe9578bc5c490bc3dfccba28
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624652"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的“审核活动”报告 

通过 Azure Active Directory (Azure AD) 报告，可以获取确定环境运行状况所需的信息。

报告体系结构包括以下组件：

- **活动** 
    - **登录** - [登录报告](concept-sign-ins.md)提供有关托管应用程序的使用情况和用户登录活动的信息。
    - **审核日志** - 针对 Azure AD 中的各种功能所做的所有更改进行日志记录，通过这些日志提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。
- **安全性** 
    - **风险登录** - [风险登录](concept-risky-sign-ins.md)是指可能由非用户帐户合法拥有者进行的登录尝试。 
    - **已标记为存在风险的用户** - [风险用户](concept-user-at-risk.md)是指可能已泄露的用户帐户。

本文概述了审核报告。
 
## <a name="who-can-access-the-data"></a>谁可以访问该数据？

* 具有**安全管理员**、**安全读者**或**全局管理员**角色的用户
* 此外，所有用户（非管理员）都可以都查看其自己的审核活动

## <a name="audit-logs"></a>审核日志

Azure AD 审核日志提供系统活动的记录以实现符合性。 若要访问审核数据，请在 **Azure Active Directory** 的“活动”部分中选择“审核日志”。 

![审核日志](./media/concept-audit-logs/61.png "审核日志")

审核日志有一个默认列表视图，用于显示：

- 匹配项的日期和时间
- 活动的发起者/参与者（*谁*） 
- 活动（*内容*） 
- 目标

![审核日志](./media/concept-audit-logs/18.png "审核日志")

单击工具栏中的“列”即可自定义列表视图。

![审核日志](./media/concept-audit-logs/19.png "审核日志")

用于显示其他字段，或者删除已显示的字段。

![审核日志](./media/concept-audit-logs/21.png "审核日志")

选择列表视图中的某个项可获得更详细的信息。

![审核日志](./media/concept-audit-logs/22.png "审核日志")


## <a name="filtering-audit-logs"></a>筛选审核日志

可以根据以下字段筛选审核数据：

- 日期范围
- 发起者（参与者/执行组件）
- 类别
- 活动资源类型
- 活动

![审核日志](./media/concept-audit-logs/23.png "审核日志")

“日期范围”筛选器用于定义已返回数据的时间范围。  
可能的值包括：

- 1 个月
- 7 天
- 24 小时
- “自定义”

选择自定义时间范围时，可以配置开始时间和结束时间。

“发起者”筛选器用于定义参与者的名称或通用主体名称 (UPN)。

“类别”筛选器用于选择下述筛选器之一：

- 所有
- 核心类别
- 核心目录
- 自助服务密码管理
- 自助组管理
- 帐户预配 - 自动密码滚动更新
- 受邀用户
- MIM 服务
- 标识保护
- B2C

“活动资源类型”筛选器用于选择下述筛选器之一：

- 所有 
- 组
- 目录
- 用户
- 应用程序
- 策略
- 设备
- 其他

选择“组”作为“活动资源类型”时，会获得一个额外的筛选器类别，因此还可以提供“源”：

- Azure AD
- O365


“活动”筛选器基于类别以及所做的活动资源类型选择。 可以选择要查看的特定活动，也可以全选。 

若要获取所有审核活动的列表，可以使用图形 API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta，其中， $tenantdomain 为域名，也可以参阅[审核报告事件](reference-audit-activities.md)一文。

## <a name="audit-logs-shortcuts"></a>审核日志快捷方式

除了 **Azure Active Directory**，Azure 门户还提供了两个额外的进行数据审核的入口点：

- 用户和组
- 企业应用程序

### <a name="users-and-groups-audit-logs"></a>用户和组审核日志

使用基于用户和组的审核报表，可以获得如下问题的答案：

- 对用户应用了哪种类型的更新？

- 更改了多少用户？

- 更改了多少密码？

- 管理员在目录中做了什么？

- 添加了哪些组？

- 是否存在成员身份已更改的组？

- 是否已更改组的所有者？

- 向组或用户分配了哪些许可证？

如果只想查看与用户和组相关的审核数据，可以在“用户和组”的“活动”部分中的“审核日志”下方查找筛选视图。 此入口点已将“用户和组”作为预先选择的“活动资源类型”。

![审核日志](./media/concept-audit-logs/93.png "审核日志")

### <a name="enterprise-applications-audit-logs"></a>企业应用程序审核日志

通过基于应用程序的审核报表，可以获得如下问题的答案：

* 添加或更新了哪些应用程序？
* 删除了哪些应用程序？
* 应用程序的服务主体是否有变化？
* 应用程序的名称是否已更改？
* 哪些用户同意使用应用程序？

如果希望查看与应用程序相关的审核数据，可以在“企业应用程序”边栏选项卡的“活动”部分中的“审核日志”下方查找筛选视图。 此入口点已将“企业应用程序”预先选择为“活动资源类型”。

![审核日志](./media/concept-audit-logs/134.png "审核日志")

可以将此视图向下筛选到“组”或“用户”。

![审核日志](./media/concept-audit-logs/25.png "审核日志")


## <a name="next-steps"></a>后续步骤

- [Azure AD 审核活动参考](reference-audit-activities.md)
- [Azure AD 报告保留参考](reference-reports-data-retention.md)
- [Azure AD 日志延迟参考](reference-reports-latencies.md)