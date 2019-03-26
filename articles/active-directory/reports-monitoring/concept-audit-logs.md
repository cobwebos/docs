---
title: Azure Active Directory 门户中的“审核活动”报告 | Microsoft Docs
description: Azure Active Directory 门户中的审核活动报告简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3da96c09026baff3965e0a90d1f461fd948a3a50
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438389"
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

* 中的用户**安全管理员**，**安全读者**，**报表读取器**或者**全局管理员**角色
* 此外，所有用户（非管理员）都可以都查看其自己的审核活动

## <a name="audit-logs"></a>审核日志

Azure AD 审核日志提供系统活动的记录以实现符合性。 若要访问审核数据，请在 **Azure Active Directory** 的“活动”部分中选择“审核日志”。 请注意，审核日志的延迟可能长达一个小时，因此在完成任务后，审核活动数据可能需要很长时间才能显示在门户中。

![审核日志](./media/concept-audit-logs/61.png "审核日志")

审核日志有一个默认列表视图，用于显示：

- 匹配项的日期和时间
- 服务记录的匹配项
- 类别和活动的名称 (*什么*) 
- 活动 （成功或失败） 的状态
- 目标
- 活动的发起者/参与者（人员）

![审核日志](./media/concept-audit-logs/listview.png "审核日志")

单击工具栏中的“列”即可自定义列表视图。

![审核日志](./media/concept-audit-logs/columns.png "审核日志")

用于显示其他字段，或者删除已显示的字段。

![审核日志](./media/concept-audit-logs/columnselect.png "审核日志")

选择列表视图中的某个项可获得更详细的信息。

![审核日志](./media/concept-audit-logs/details.png "审核日志")


## <a name="filtering-audit-logs"></a>筛选审核日志

可以根据以下字段筛选审核数据：

- 服务
- 类别
- 活动
- 状态
- 目标
- 发起者（参与者/执行组件）
- 日期范围

![审核日志](./media/concept-audit-logs/filter.png "审核日志")

**服务**筛选器，可以从以下服务的一个下拉列表中选择：

- All
- 访问评审
- 帐户预配 
- SSO 应用程序
- 身份验证方法
- B2C
- 条件性访问
- 核心目录
- 权利管理
- 标识保护
- 受邀用户
- PIM
- 自助服务组管理
- 自助服务 Passord 管理
- 使用条款

**类别**筛选器，可选择以下筛选器之一：

- All
- AdministrativeUnit
- ApplicationManagement
- Authentication
- 授权
- 联系人
- 设备
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- 其他
- 策略
- ResourceManagement
- RoleManagement
- UserManagement

**活动**筛选器基于类别和活动资源类型所做的选择。 可以选择要查看的特定活动，也可以全选。 

若要获取所有审核活动的列表，可以使用图形 API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta，其中， $tenantdomain 为域名，也可以参阅[审核报告事件](reference-audit-activities.md)一文。

**状态**筛选器可筛选基于审核操作的状态。 状态可以是以下值之一：

- All
- Success
- 失败

**目标**筛选器，可以按名称或用户主体名称 (UPN) 搜索特定目标。 目标名称和 UPN 是区分大小写。 

**由启动**筛选器使您能够定义执行组件的名称或通用主体名称 (UPN)。 名称和 UPN 是区分大小写。

**日期范围**筛选器用于定义返回的数据的时间范围。  
可能的值包括：

- 1 个月
- 7 天
- 24 小时
- “自定义”

选择自定义时间范围时，可以配置开始时间和结束时间。

您还可以选择通过选择下载的已筛选的数据、 最多 250,000 记录**下载**按钮。 您可以选择下载为 CSV 或 JSON 格式中的日志。 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。

![审核日志](./media/concept-audit-logs/download.png "审核日志")

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

如果只是想要查看与用户相关的审核数据，可以查找筛选的视图下**审核日志**中**活动**一部分**用户**选项卡。此入口点已**UserManagement**作为预先选择的类别。

![审核日志](./media/concept-audit-logs/users.png "审核日志")

如果只是想要查看与组相关的审核数据，可以查找筛选的视图下**审核日志**中**活动**一部分**组**选项卡。此入口点已**GroupManagement**作为预先选择的类别。

![审核日志](./media/concept-audit-logs/groups.png "审核日志")

### <a name="enterprise-applications-audit-logs"></a>企业应用程序审核日志

通过基于应用程序的审核报表，可以获得如下问题的答案：

* 添加或更新了哪些应用程序？
* 删除了哪些应用程序？
* 应用程序的服务主体是否有变化？
* 应用程序的名称是否已更改？
* 哪些用户同意使用应用程序？

如果希望查看与应用程序相关的审核数据，可以在“企业应用程序”边栏选项卡的“活动”部分中的“审核日志”下方查找筛选视图。 此入口点已**企业应用程序**预先选择作为**应用程序类型**。

![审核日志](./media/concept-audit-logs/enterpriseapplications.png "审核日志")

## <a name="office-365-activity-logs"></a>Office 365 活动日志

您可以查看从 Office 365 活动日志[Microsoft 365 管理中心内](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)。 尽管 Office 365 活动和 Azure AD 活动日志共享大量的目录资源，仅 Microsoft 365 管理中心内提供了 Office 365 活动日志的完整视图。 

此外可以使用 [Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) 以编程方式访问 Office 365 活动日志。

## <a name="next-steps"></a>后续步骤

- [Azure AD 审核活动参考](reference-audit-activities.md)
- [Azure AD 报告保留参考](reference-reports-data-retention.md)
- [Azure AD 日志延迟参考](reference-reports-latencies.md)
