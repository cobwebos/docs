---
title: Azure Active Directory 门户中的“审核活动”报告 | Microsoft Docs
description: Azure Active Directory 门户中的审核活动报告简介
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 04/19/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: ae6e2b25ccc7b0d3f29b62e56279f60034ef728c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588635"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的“审核活动”报告 

通过 Azure Active Directory (Azure AD) 中的报告，可以获取确定环境运行状况所需的信息。

Azure AD 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录活动** — 有关托管应用程序的使用和用户登录活动的信息
    - **审核日志** - 针对 Azure AD 中的各种功能所做的所有更改进行日志记录，通过这些日志提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如用户、应用、组、角色、策略、身份验证等）所做的更改。
- **安全性** 
    - **风险登录** - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 有关详细信息，请参阅“有风险的登录”。
    - **已标记为存在风险的用户** - 风险用户是指可能已泄露的用户帐户。 有关详细信息，请参阅“已标记为存在风险的用户”。

本主题对审核活动进行了概述。
 
## <a name="who-can-access-the-data"></a>谁可以访问该数据？
* 安全管理员或安全读者角色中的用户
* 全局管理员
* 各用户（非管理员）可以查看自己的活动


## <a name="audit-logs"></a>审核日志

Azure Active Directory 中的审核日志为符合性提供系统活动的记录。  
所有审核数据的第一个入口点为 **Azure Active Directory** 的“活动”部分中的“审核日志”。

![审核日志](./media/active-directory-reporting-activity-audit-logs/61.png "审核日志")

审核日志有一个默认列表视图，用于显示：

- 匹配项的日期和时间
- 活动的发起者/参与者（*谁*） 
- 活动（*内容*） 
- 目标

![审核日志](./media/active-directory-reporting-activity-audit-logs/18.png "审核日志")

单击工具栏中的“列”即可自定义列表视图。

![审核日志](./media/active-directory-reporting-activity-audit-logs/19.png "审核日志")

用于显示其他字段，或者删除已显示的字段。

![审核日志](./media/active-directory-reporting-activity-audit-logs/21.png "审核日志")


通过单击列表视图中的项，可以获得已提供的所有相关详情。

![审核日志](./media/active-directory-reporting-activity-audit-logs/22.png "审核日志")


## <a name="filtering-audit-logs"></a>筛选审核日志

要将所报告数据的范围缩小到适当的级别，可以使用以下字段筛选审核数据：

- 日期范围
- 发起者（参与者/执行组件）
- 类别
- 活动资源类型
- 活动

![审核日志](./media/active-directory-reporting-activity-audit-logs/23.png "审核日志")


“日期范围”筛选器用于定义已返回数据的时间范围。  
可能的值包括：

- 1 个月
- 7 天
- 24 小时
- “自定义”

选择自定义时间范围时，可以配置开始时间和结束时间。

“发起者”筛选器用于定义参与者的名称或其通用主体名称 (UPN)。

“类别”筛选器用于选择下述筛选器之一：

- 全部
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

- 全部 
- 组
- Directory
- 用户
- Application
- 策略
- 设备
- 其他

选择“组”作为“活动资源类型”时，会获得一个额外的筛选器类别，因此还可以提供“源”：

- Azure AD
- O365


“活动”筛选器基于类别以及所做的活动资源类型选择。 可以选择要查看的特定活动，也可以全选。 

若要获取所有审核活动的列表，可以使用图形 API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta，其中， $tenantdomain 为域名，也可以参阅[审核报告事件](active-directory-reporting-audit-events.md)一文。


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

![审核日志](./media/active-directory-reporting-activity-audit-logs/93.png "审核日志")

### <a name="enterprise-applications-audit-logs"></a>企业应用程序审核日志

通过基于应用程序的审核报表，可以获得如下问题的答案：

* 已添加或更新的应用程序有哪些？
* 已删除的应用程序有哪些？
* 应用程序的服务原则是否有变化？
* 应用程序的名称是否已更改？
* 哪些用户同意使用应用程序？

如果只想查看与应用程序相关的审核数据，可以在“企业应用程序”边栏选项卡的“活动”部分中的“审核日志”下方查找筛选视图。 此入口点已将“企业应用程序”作为预先选择的“活动资源类型”。

![审核日志](./media/active-directory-reporting-activity-audit-logs/134.png "审核日志")

可以进一步对此视图进行筛选，使之只剩下“组”或“用户”。

![审核日志](./media/active-directory-reporting-activity-audit-logs/25.png "审核日志")



## <a name="next-steps"></a>后续步骤

- 有关报告的概述，请参阅 [Azure Active Directory 报告](active-directory-reporting-azure-portal.md)。

- 有关所有审核活动的完整列表，请参阅 [Azure AD 审核活动参考](active-directory-reporting-activity-audit-reference.md)。

