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
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86eec0cf7108e2d3b47f7b98dbdaffe76be8afd8
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603503"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的“审核活动”报告 

通过 Azure Active Directory (Azure AD) 报告，可以获取确定环境运行状况所需的信息。

报告体系结构包括以下组件：

- **活动** 
    - **登录** - [登录报告](concept-sign-ins.md)提供有关托管应用程序的使用情况和用户登录活动的信息。
    - **审核日志** - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。
- **安全性** 
    - 有**风险的登录**-有[风险登录](../identity-protection/overview-identity-protection.md)是指可能由不是用户帐户合法所有者执行的登录尝试的指示符。 
    - **已标记为存在风险的用户** - [风险用户](../identity-protection/overview-identity-protection.md)是指可能已泄露的用户帐户。

本文概述了审核报告。
 
## <a name="who-can-access-the-data"></a>谁可以访问该数据？

* **安全管理员**、**安全读者**、**报表读者**、**全局读者**或**全局管理员**角色中的用户

## <a name="audit-logs"></a>审核日志

Azure AD 审核日志提供系统活动的记录以实现符合性。 若要访问审核报告，请在**Azure Active Directory**的 "**监视**" 部分选择 "**审核日志**"。 请注意，审核日志的滞后时间可能最长为一小时，因此，在完成任务后，在门户中显示审核活动数据可能需要较长时间。



审核日志有一个默认列表视图，用于显示：

- 匹配项的日期和时间
- 记录了匹配项的服务
- 活动的类别和名称（内容）** 
- 活动的状态（成功或失败）
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

使用“服务”**** 筛选器可以从以下服务的下拉列表中进行选择：

- 全部
- AAD 管理 UX
- 访问评审
- 帐户预配
- 应用程序代理
- 身份验证方法
- B2C
- 条件性访问
- 核心目录
- 权利管理
- 混合身份验证
- 标识保护
- 受邀用户
- MIM 服务
- MyApps
- PIM
- 自助服务组管理
- 自助服务密码管理
- 使用条款

“类别”筛选器用于选择下述筛选器之一：****

- 全部
- AdministrativeUnit
- ApplicationManagement
- 身份验证
- 授权
- 联系人
- 设备
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Label
- 其他
- PermissionGrantPolicy
- 策略
- ResourceManagement
- RoleManagement
- UserManagement

**活动**筛选器基于所选的类别和活动资源类型。 可以选择要查看的特定活动，也可以全选。 

可以使用图形 API 获取所有审核活动的列表：`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

可以使用“状态”筛选器根据审核操作的状态进行筛选。**** 状态可以是下列其中一项：

- 全部
- Success
- 失败

**目标**筛选器使你可以通过 (UPN) 的名称或用户主体名称来搜索特定目标。 目标名称和 UPN 区分大小写。 

通过筛选器启动的筛选器，您可以定义使用 **者** 名称或通用主体名称 (UPN) 以开头。 名称和 UPN 区分大小写。

使用 " **日期范围** " 筛选器可以定义返回的数据的时间范围。  
可能的值包括：

- 7 天
- 24 小时
- “自定义”

选择自定义时间范围时，可以配置开始时间和结束时间。

也可选择下载筛选的数据（多达 250,000 条记录），只需选择“下载”按钮即可。**** 可以下载 CSV 或 JSON 格式的日志。 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。

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

如果只想查看与用户相关的审核数据，则可以在 "**用户**" 选项卡的 "**监视**" 部分中的 "**审核日志**" 下找到筛选视图。此入口点已将**UserManagement**作为预先选择的类别。

![审核日志](./media/concept-audit-logs/users.png "审核日志")

如果只想查看与组相关的审核数据，则可以在 "**组**" 选项卡的 "**监视**" 部分中的 "**审核日志**" 下找到筛选视图。此入口点已将**GroupManagement**作为预先选择的类别。

![审核日志](./media/concept-audit-logs/groups.png "审核日志")

### <a name="enterprise-applications-audit-logs"></a>企业应用程序审核日志

通过基于应用程序的审核报表，可以获得如下问题的答案：

* 添加或更新了哪些应用程序？
* 删除了哪些应用程序？
* 应用程序的服务主体是否有变化？
* 应用程序的名称是否已更改？
* 哪些用户同意使用应用程序？

如果希望查看与应用程序相关的审核数据，可以在“企业应用程序”边栏选项卡的“活动”部分中的“审核日志”下方查找筛选视图。************ 此入口点已将“企业应用程序”预先选择为“应用程序类型”。********

![审核日志](./media/concept-audit-logs/enterpriseapplications.png "审核日志")

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 活动日志

可以从 [Microsoft 365 管理中心](/office365/admin/admin-overview/about-the-admin-center)查看 Microsoft 365 活动日志。 尽管 Microsoft 365 活动和 Azure AD 活动日志共享大量的目录资源，但只有 Microsoft 365 管理中心提供 Microsoft 365 活动日志的完整视图。 

你还可以使用 [Office 365 管理 api](/office/office-365-management-api/office-365-management-apis-overview)以编程方式访问 Microsoft 365 活动日志。

## <a name="next-steps"></a>后续步骤

- [Azure AD 审核活动参考](reference-audit-activities.md)
- [Azure AD 报告保留参考](reference-reports-data-retention.md)
- [Azure AD 日志延迟参考](reference-reports-latencies.md)