---
title: Azure Active Directory 门户中的“审核活动”报告 | Microsoft Docs
description: Azure Active Directory 门户中的审核活动报告简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的“审核活动”报告 

通过 Azure Active Directory (Azure AD) 中的报告，可以获取确定环境运行状况所需的信息。

Azure AD 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录活动** — 有关托管应用程序的使用和用户登录活动的信息
    - **审核日志** - 有关用户和组管理、托管应用程序和目录活动的系统活动信息。
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



## <a name="azure-ad-audit-activity-list"></a>Azure AD 审核活动列表

本部分提供的列表包含可以记录的所有活动。 


|服务名称|审核类别|活动资源类型|活动|
|---|---|---|---|
|帐户预配|应用程序管理|Application|管理|
|帐户预配|应用程序管理|Application|目录操作|
|帐户预配|应用程序管理|Application|导出|
|帐户预配|应用程序管理|Application|导入|
|帐户预配|应用程序管理|Application|其他|
|帐户预配|应用程序管理|Application|进程托管|
|帐户预配|应用程序管理|Application|同步规则操作|
|应用程序代理|应用程序管理|Application|添加应用程序|
|应用程序代理|资源|资源|添加应用程序 SSL 证书|
|应用程序代理|资源|资源|删除 SSL 绑定|
|应用程序代理|应用程序管理|Application|删除应用程序|
|应用程序代理|目录管理|Directory|禁用桌面 SSO|
|应用程序代理|目录管理|Directory|禁用特定域的桌面 SSO|
|应用程序代理|目录管理|Directory|禁用应用程序代理|
|应用程序代理|目录管理|Directory|禁用直通身份验证|
|应用程序代理|目录管理|Directory|启用桌面 SSO|
|应用程序代理|目录管理|Directory|启用特定域的桌面 SSO|
|应用程序代理|目录管理|Directory|启用应用程序代理|
|应用程序代理|目录管理|Directory|启用直通身份验证|
|应用程序代理|资源|资源|注册连接器|
|应用程序代理|应用程序管理|Application|更新应用程序|
|应用程序代理|应用程序管理|Application|更新应用程序单一登录模式|
|自动密码滚动更新|应用程序管理|Application|自动密码滚动更新|
|B2C|应用程序管理|Application|添加 V2 应用程序权限|
|B2C|授权|授权|添加 V2 应用程序权限|
|B2C|密钥|密钥|将基于 ASCII 机密的密钥添加到 CPIM 密钥容器|
|B2C|授权|授权|将基于 ASCII 机密的密钥添加到 CPIM 密钥容器|
|B2C|密钥|密钥|将密钥添加到 CPIM 密钥容器|
|B2C|授权|授权|将密钥添加到 CPIM 密钥容器|
|B2C|资源|资源|AdminPolicyDatas-RemoveResources|
|B2C|授权|授权|AdminPolicyDatas-SetResources|
|B2C|资源|资源|AdminPolicyDatas-SetResources|
|B2C|资源|资源|AdminUserJourneys-GetResources|
|B2C|授权|授权|AdminUserJourneys-GetResources|
|B2C|授权|授权|AdminUserJourneys-RemoveResources|
|B2C|资源|资源|AdminUserJourneys-RemoveResources|
|B2C|资源|资源|AdminUserJourneys-SetResources|
|B2C|授权|授权|AdminUserJourneys-SetResources|
|B2C|授权|授权|创建 IdentityProvider|
|B2C|资源|资源|创建 IdentityProvider|
|B2C|授权|授权|创建 V1 应用程序|
|B2C|应用程序管理|Application|创建 V1 应用程序|
|B2C|应用程序管理|Application|创建 V2 应用程序|
|B2C|授权|授权|创建 V2 应用程序|
|B2C|目录管理|Directory|在租户中创建自定义域|
|B2C|授权|授权|在租户中创建自定义域|
|B2C|授权|授权|创建新的 AdminUserJourney|
|B2C|资源|资源|创建新的 AdminUserJourney|
|B2C|资源|资源|创建本地化资源 json|
|B2C|授权|授权|创建本地化资源 json|
|B2C|授权|授权|创建新的自定义 IDP|
|B2C|资源|资源|创建新的自定义 IDP|
|B2C|资源|资源|创建新的 IDP|
|B2C|授权|授权|创建新的 IDP|
|B2C|授权|授权|创建或更新 B2C 目录资源|
|B2C|资源|资源|创建或更新 B2C 目录资源|
|B2C|资源|资源|创建策略|
|B2C|授权|授权|创建策略|
|B2C|授权|授权|创建 trustFramework 策略|
|B2C|资源|资源|创建 trustFramework 策略|
|B2C|授权|授权|创建前缀可配置的 trustFramework 策略|
|B2C|资源|资源|创建前缀可配置的 trustFramework 策略|
|B2C|资源|资源|创建用户属性|
|B2C|授权|授权|创建用户属性|
|B2C|授权|授权|CreateTrustFrameworkPolicy|
|B2C|资源|资源|CreateTrustFrameworkPolicy|
|B2C|授权|授权|创建或更新新的 AdminUserJourney|
|B2C|资源|资源|删除 IDP|
|B2C|授权|授权|删除 IDP|
|B2C|资源|资源|删除 IdentityProvider|
|B2C|授权|授权|删除 IdentityProvider|
|B2C|应用程序管理|Application|删除 V1 应用程序|
|B2C|授权|授权|删除 V1 应用程序|
|B2C|应用程序管理|Application|删除 V2 应用程序|
|B2C|授权|授权|删除 V2 应用程序|
|B2C|授权|授权|删除 V2 应用程序授权|
|B2C|应用程序管理|Application|删除 V2 应用程序授权|
|B2C|资源|资源|删除 B2C 目录资源|
|B2C|授权|授权|删除 B2C 目录资源|
|B2C|密钥|密钥|删除 CPIM 密钥容器|
|B2C|授权|授权|删除 CPIM 密钥容器|
|B2C|密钥|密钥|删除密钥容器|
|B2C|资源|资源|删除 trustFramework 策略|
|B2C|授权|授权|删除 trustFramework 策略|
|B2C|资源|资源|删除用户属性|
|B2C|授权|授权|删除用户属性|
|B2C|授权|授权|启用 B2C 功能|
|B2C|目录管理|Directory|启用 B2C 功能|
|B2C|资源|资源|获取资源组中的 B2C 目录资源|
|B2C|资源|资源|获取订阅中的 B2C 目录资源|
|B2C|授权|授权|获取订阅中的 B2C 目录资源|
|B2C|授权|授权|获取自定义 IDP|
|B2C|资源|资源|获取自定义 IDP|
|B2C|资源|资源|获取 IDP|
|B2C|授权|授权|获取 IDP|
|B2C|授权|授权|获取 V1 和 V2 应用程序|
|B2C|应用程序管理|Application|获取 V1 和 V2 应用程序|
|B2C|授权|授权|获取 V1 应用程序|
|B2C|应用程序管理|Application|获取 V1 应用程序|
|B2C|授权|授权|获取 V1 应用程序|
|B2C|应用程序管理|Application|获取 V1 应用程序|
|B2C|应用程序管理|Application|获取 V2 应用程序|
|B2C|授权|授权|获取 V2 应用程序|
|B2C|应用程序管理|Application|获取 V2 应用程序|
|B2C|授权|授权|获取 V2 应用程序|
|B2C|资源|资源|获取 B2C 目录资源|
|B2C|授权|授权|获取 B2C 目录资源|
|B2C|授权|授权|获取租户中自定义域的列表|
|B2C|目录管理|Directory|获取租户中自定义域的列表|
|B2C|授权|授权|获取用户旅程|
|B2C|资源|资源|获取用户旅程|
|B2C|资源|资源|获取用户旅程允许的应用程序声明|
|B2C|授权|授权|获取用户旅程允许的应用程序声明|
|B2C|资源|资源|获取用户旅程允许的自断言声明|
|B2C|授权|授权|获取用户旅程允许的自断言声明|
|B2C|资源|资源|获取允许的自断言策略声明|
|B2C|授权|授权|获取允许的自断言策略声明|
|B2C|资源|资源|获取可用的输出声明列表|
|B2C|授权|授权|获取可用的输出声明列表|
|B2C|资源|资源|获取用户旅程的内容定义|
|B2C|授权|授权|获取用户旅程的内容定义|
|B2C|授权|授权|获取特定管理流的 IDP|
|B2C|资源|资源|获取特定管理流的 IDP|
|B2C|密钥|密钥|获取 JWK 格式的密钥容器活动密钥元数据|
|B2C|授权|授权|获取 JWK 格式的密钥容器活动密钥元数据|
|B2C|密钥|密钥|获取密钥容器元数据|
|B2C|资源|资源|获取所有管理流的列表|
|B2C|授权|授权|获取所有管理流的列表|
|B2C|授权|授权|获取所有用户的所有管理流的标记列表|
|B2C|资源|资源|获取所有用户的所有管理流的标记列表|
|B2C|资源|资源|获取用户的租户列表|
|B2C|授权|授权|获取用户的租户列表|
|B2C|资源|资源|获取本地帐户的自断言声明|
|B2C|授权|授权|获取本地帐户的自断言声明|
|B2C|资源|资源|获取本地化资源 json|
|B2C|授权|授权|获取本地化资源 json|
|B2C|授权|授权|获取 Microsoft.AzureActiveDirectory 资源提供程序的操作|
|B2C|资源|资源|获取 Microsoft.AzureActiveDirectory 资源提供程序的操作|
|B2C|资源|资源|获取策略|
|B2C|授权|授权|获取策略|
|B2C|资源|资源|获取策略|
|B2C|授权|授权|获取策略|
|B2C|目录管理|Directory|获取租户的资源属性|
|B2C|授权|授权|获取租户的资源属性|
|B2C|资源|资源|获取受支持的 IDP 列表|
|B2C|授权|授权|获取受支持的 IDP 列表|
|B2C|资源|资源|获取用户旅程的受支持的 IDP 列表|
|B2C|授权|授权|获取用户旅程的受支持的 IDP 列表|
|B2C|目录管理|Directory|获取租户信息|
|B2C|授权|授权|获取租户信息|
|B2C|目录管理|Directory|获取租户允许的功能|
|B2C|授权|授权|获取租户允许的功能|
|B2C|授权|授权|获取租户定义的自定义 IDP 列表|
|B2C|资源|资源|获取租户定义的自定义 IDP 列表|
|B2C|资源|资源|获取租户定义的 IDP 列表|
|B2C|授权|授权|获取租户定义的 IDP 列表|
|B2C|资源|资源|获取租户定义的本地 IDP 列表|
|B2C|授权|授权|获取租户定义的本地 IDP 列表|
|B2C|资源|资源|获取用户的租户详细信息，以便创建资源|
|B2C|授权|授权|获取用户的租户详细信息，以便创建资源|
|B2C|授权|授权|获取租户列表|
|B2C|授权|授权|获取 tenantDomains|
|B2C|目录管理|Directory|获取 tenantDomains|
|B2C|资源|资源|获取默认的 CPIM 支持的区域性|
|B2C|授权|授权|获取默认的 CPIM 支持的区域性|
|B2C|资源|资源|获取管理流的详细信息|
|B2C|授权|授权|获取管理流的详细信息|
|B2C|授权|授权|获取此租户的 UserJourneys 的列表|
|B2C|资源|资源|获取此租户的 UserJourneys 的列表|
|B2C|授权|授权|获取 CPIM 支持的可用区域性的集合|
|B2C|资源|资源|获取 CPIM 支持的可用区域性的集合|
|B2C|授权|授权|获取 trustFramework 策略|
|B2C|资源|资源|获取 trustFramework 策略|
|B2C|授权|授权|获取 XML 格式的 trustFramework 策略|
|B2C|资源|资源|获取 XML 格式的 trustFramework 策略|
|B2C|资源|资源|获取用户属性|
|B2C|授权|授权|获取用户属性|
|B2C|授权|授权|获取用户属性|
|B2C|资源|资源|获取用户属性|
|B2C|授权|授权|获取用户旅程列表|
|B2C|资源|资源|获取用户旅程列表|
|B2C|授权|授权|GetIEFPolicies|
|B2C|资源|资源|GetIEFPolicies|
|B2C|授权|授权|GetIdentityProviders|
|B2C|资源|资源|GetIdentityProviders|
|B2C|资源|资源|GetTrustFrameworkPolicy|
|B2C|授权|授权|GetTrustFrameworkPolicy|
|B2C|密钥|密钥|获取 jwk 格式的 CPIM 密钥容器|
|B2C|授权|授权|获取 jwk 格式的 CPIM 密钥容器|
|B2C|密钥|密钥|获取租户中密钥容器的列表|
|B2C|授权|授权|获取租户中密钥容器的列表|
|B2C|授权|授权|获取租户的类型|
|B2C|目录管理|Directory|获取租户的类型|
|B2C|身份验证|身份验证|向应用程序颁发访问令牌|
|B2C|身份验证|身份验证|向应用程序颁发授权代码|
|B2C|其他|其他|向应用程序颁发授权代码|
|B2C|身份验证|身份验证|向应用程序颁发 id_token|
|B2C|其他|其他|向应用程序颁发 id_token|
|B2C|授权|授权|MigrateTenantMetadata|
|B2C|资源|资源|MigrateTenantMetadata|
|B2C|资源|资源|移动资源|
|B2C|授权|授权|修补 IdentityProvider|
|B2C|资源|资源|修补 IdentityProvider|
|B2C|资源|资源|PutTrustFrameworkPolicy|
|B2C|授权|授权|PutTrustFrameworkPolicy|
|B2C|授权|授权|PutTrustFrameworkpolicy|
|B2C|资源|资源|PutTrustFrameworkpolicy|
|B2C|资源|资源|删除用户旅程|
|B2C|授权|授权|删除用户旅程|
|B2C|授权|授权|还原 CPIM 密钥容器备份|
|B2C|密钥|密钥|还原 CPIM 密钥容器备份|
|B2C|应用程序管理|Application|检索 V2 应用程序授权|
|B2C|授权|授权|检索 V2 应用程序授权|
|B2C|应用程序管理|Application|检索当前租户中的 V2 应用程序服务主体|
|B2C|授权|授权|检索当前租户中的 V2 应用程序服务主体|
|B2C|密钥|密钥|保存密钥容器|
|B2C|授权|授权|更新自定义 IDP|
|B2C|资源|资源|更新自定义 IDP|
|B2C|资源|资源|更新 IDP|
|B2C|授权|授权|更新 IDP|
|B2C|资源|资源|更新本地 IDP|
|B2C|授权|授权|更新本地 IDP|
|B2C|应用程序管理|Application|更新 V1 应用程序|
|B2C|授权|授权|更新 V1 应用程序|
|B2C|应用程序管理|Application|更新 V2 应用程序|
|B2C|授权|授权|更新 V2 应用程序|
|B2C|应用程序管理|Application|更新 V2 应用程序授权|
|B2C|授权|授权|更新 V2 应用程序授权|
|B2C|资源|资源|更新 B2C 目录资源|
|B2C|资源|资源|更新策略|
|B2C|授权|授权|更新策略|
|B2C|资源|资源|更新订阅状态|
|B2C|资源|资源|更新用户属性|
|B2C|授权|授权|更新用户属性|
|B2C|密钥|密钥|上传 CPIM 加密密钥|
|B2C|授权|授权|上传 CPIM 加密密钥|
|B2C|授权|授权|用户授权：针对租户功能集禁用了 API|
|B2C|授权|授权|用户授权：为用户授予了“租户管理员”访问权限|
|B2C|授权|授权|用户授权：为用户授予了“已验证用户”访问权限|
|B2C|身份验证|身份验证|验证本地帐户凭据|
|B2C|资源|资源|验证移动资源|
|B2C|身份验证|身份验证|验证用户身份验证|
|B2C|目录管理|Directory|验证是否已启用 B2C 功能|
|B2C|授权|授权|验证是否已启用 B2C 功能|
|B2C|授权|授权|验证是否已启用功能|
|B2C|目录管理|Directory|验证是否已启用功能|
|核心目录|应用程序管理|Application|添加 OAuth2PermissionGrant|
|核心目录|管理单元管理|AdministrativeUnit|添加管理单元|
|核心目录|用户管理|用户|向用户添加应用角色分配授权|
|核心目录|组管理|组|向组添加应用角色分配|
|核心目录|应用程序管理|Application|向服务主体添加应用角色分配|
|核心目录|应用程序管理|Application|添加应用程序|
|核心目录|资源|资源|添加设备|
|核心目录|资源|资源|添加设备配置|
|核心目录|角色管理|角色|将符合条件的成员添加到角色|
|核心目录|组管理|组|添加组|
|核心目录|管理单元管理|AdministrativeUnit|将成员添加到管理单元|
|核心目录|组管理|组|将成员添加到组|
|核心目录|角色管理|角色|将成员添加到角色|
|核心目录|应用程序管理|Application|将所有者添加到应用程序|
|核心目录|组管理|组|将所有者添加到组|
|核心目录|策略管理|策略|将所有者添加到策略|
|核心目录|应用程序管理|Application|将所有者添加到服务主体|
|核心目录|目录管理|Directory|将合作伙伴添加到公司|
|核心目录|策略管理|策略|添加策略|
|核心目录|应用程序管理|Application|将策略添加到服务主体|
|核心目录|资源|资源|将注册的所有者添加到设备|
|核心目录|资源|资源|将注册的用户添加到设备|
|核心目录|角色管理|角色|将角色分配添加到角色定义|
|核心目录|角色管理|角色|从模板添加角色|
|核心目录|角色管理|角色|将带有范围的成员添加到角色|
|核心目录|应用程序管理|Application|添加服务主体|
|核心目录|应用程序管理|Application|添加服务主体凭据|
|核心目录|目录管理|Directory|添加未验证的域|
|核心目录|用户管理|用户|添加用户|
|核心目录|用户管理|用户|添加用户强身份验证手机应用详细信息|
|核心目录|目录管理|Directory|添加已验证的域|
|核心目录|用户管理|用户|更改用户许可证|
|核心目录|用户管理|用户|更改用户密码|
|核心目录|应用程序管理|Application|同意使用应用程序|
|核心目录|用户管理|用户|将联合用户转换为托管用户|
|核心目录|用户管理|用户|为用户创建应用程序密码|
|核心目录|目录管理|Directory|创建公司|
|核心目录|目录管理|Directory|创建公司设置|
|核心目录|组管理|组|创建组设置|
|核心目录|管理单元管理|AdministrativeUnit|删除管理单元|
|核心目录|应用程序管理|Application|删除应用程序|
|核心目录|用户管理|用户|为用户删除应用程序密码|
|核心目录|目录管理|Directory|删除公司设置|
|核心目录|资源|资源|删除设备|
|核心目录|资源|资源|删除设备配置|
|核心目录|组管理|组|删除组|
|核心目录|组管理|组|删除组设置|
|核心目录|策略管理|策略|删除策略|
|核心目录|用户管理|用户|删除用户|
|核心目录|目录管理|Directory|降级合作伙伴|
|核心目录|资源|资源|设备不再合规|
|核心目录|资源|资源|设备不再托管|
|核心目录|目录管理|Directory|目录已删除|
|核心目录|目录管理|Directory|目录已永久删除|
|核心目录|目录管理|Directory|目录已计划删除|
|核心目录|用户管理|用户|禁用帐户|
|核心目录|用户管理|用户|启用强身份验证|
|核心目录|组管理|组|完成向用户应用基于组的许可证|
|核心目录|应用程序管理|Application|硬删除应用程序|
|核心目录|组管理|组|硬删除组|
|核心目录|用户管理|用户|硬删除用户|
|核心目录|目录管理|Directory|将公司提升为合作伙伴|
|核心目录|目录管理|Directory|清除 Rights Management 属性|
|核心目录|应用程序管理|Application|删除 OAuth2PermissionGrant|
|核心目录|组管理|组|从组删除应用角色分配|
|核心目录|应用程序管理|Application|从服务主体删除应用角色分配|
|核心目录|用户管理|用户|从用户删除应用角色分配|
|核心目录|角色管理|角色|从角色中删除符合条件的成员|
|核心目录|管理单元管理|AdministrativeUnit|从管理单元中删除成员|
|核心目录|组管理|组|从组中删除成员|
|核心目录|角色管理|角色|从角色中删除成员|
|核心目录|应用程序管理|Application|从应用程序中删除所有者|
|核心目录|组管理|组|从组中删除所有者|
|核心目录|应用程序管理|Application|从服务主体中删除所有者|
|核心目录|目录管理|Directory|从公司中删除合作伙伴|
|核心目录|策略管理|策略|删除策略凭据|
|核心目录|应用程序管理|Application|从服务主体中删除策略|
|核心目录|资源|资源|从设备中删除注册的所有者|
|核心目录|资源|资源|从设备中删除注册的用户|
|核心目录|角色管理|角色|从角色定义中删除角色分配|
|核心目录|角色管理|角色|从角色中删除带有范围的成员|
|核心目录|应用程序管理|Application|删除服务主体|
|核心目录|应用程序管理|Application|删除服务主体凭据|
|核心目录|目录管理|Directory|删除未验证的域|
|核心目录|用户管理|用户|删除用户强身份验证手机应用详细信息|
|核心目录|目录管理|Directory|删除已验证的域|
|核心目录|用户管理|用户|重置用户密码|
|核心目录|组管理|组|还原组|
|核心目录|应用程序管理|Application|还原应用程序|
|核心目录|用户管理|用户|还原用户|
|核心目录|应用程序管理|Application|吊销许可|
|核心目录|目录管理|Directory|设置公司信息|
|核心目录|目录管理|Directory|设置 DirSync 功能|
|核心目录|目录管理|Directory|设置 DirSyncEnabled 标志|
|核心目录|目录管理|Directory|设置合作关系|
|核心目录|目录管理|Directory|设置意外删除阈值|
|核心目录|目录管理|Directory|设置公司允许的数据位置|
|核心目录|目录管理|Directory|设置公司跨国功能已启用|
|核心目录|目录管理|Directory|在租户上设置目录功能|
|核心目录|目录管理|Directory|设置域身份验证|
|核心目录|目录管理|Directory|在域中设置联合设置|
|核心目录|用户管理|用户|设置强制更改用户密码|
|核心目录|组管理|组|设置组许可证|
|核心目录|组管理|组|已设置要由用户管理的组|
|核心目录|目录管理|Directory|设置密码策略|
|核心目录|目录管理|Directory|设置 Rights Management 属性|
|核心目录|用户管理|用户|设置用户管理器|
|核心目录|用户管理|用户|设置用户 oath 令牌元数据功能已启用|
|核心目录|组管理|组|开始向用户应用基于组的许可证|
|核心目录|组管理|组|触发组许可证重新计算|
|核心目录|用户管理|用户|更新 StsRefreshTokenValidFrom 时间戳|
|核心目录|管理单元管理|AdministrativeUnit|更新管理单元|
|核心目录|应用程序管理|Application|更新应用程序|
|核心目录|目录管理|Directory|更新公司|
|核心目录|目录管理|Directory|更新公司设置|
|核心目录|资源|资源|更新设备|
|核心目录|资源|资源|更新设备配置|
|核心目录|目录管理|Directory|更新域|
|核心目录|用户管理|用户|更新外部机密|
|核心目录|应用程序管理|Application|更新外部机密|
|核心目录|组管理|组|更新组|
|核心目录|组管理|组|更新组设置|
|核心目录|策略管理|策略|更新策略|
|核心目录|角色管理|角色|更新角色|
|核心目录|应用程序管理|Application|更新服务主体|
|核心目录|用户管理|用户|更新用户|
|核心目录|目录管理|Directory|验证域|
|核心目录|目录管理|Directory|验证电子邮件验证域|
|标识保护|用户管理|用户|管理员生成临时密码|
|标识保护|用户管理|用户|管理员要求用户重置其密码|
|标识保护|其他|其他|下载单个风险事件类型|
|标识保护|其他|其他|下载选择加入的每周摘要的管理和状态|
|标识保护|其他|其他|下载所有风险事件类型|
|标识保护|其他|其他|下载免费的用户风险事件|
|标识保护|其他|其他|下载已标记为存在风险的用户|
|标识保护|目录管理|Directory|登记|
|标识保护|策略管理|策略|设置 MFA 注册策略|
|标识保护|策略管理|策略|设置登录风险策略|
|标识保护|策略管理|策略|设置用户风险策略|
|标识保护|目录管理|Directory|更新警报设置|
|标识保护|目录管理|Directory|更新每周摘要设置|
|受邀用户|用户管理|用户|将外部用户分配到应用程序|
|受邀用户|其他|其他|已处理批量邀请|
|受邀用户|其他|其他|已上传批量邀请|
|受邀用户|用户管理|用户|电子邮件未发送，用户已取消订阅|
|受邀用户|用户管理|用户|邀请外部用户|
|受邀用户|用户管理|用户|兑换外部用户邀请|
|受邀用户|用户管理|用户|创建病毒性租户|
|受邀用户|用户管理|用户|创建病毒性用户|
|Microsoft Identity Manager (MIM)|组管理|组|添加成员|
|Microsoft Identity Manager (MIM)|组管理|组|创建组|
|Microsoft Identity Manager (MIM)|组管理|组|删除组|
|Microsoft Identity Manager (MIM)|组管理|组|删除成员|
|Microsoft Identity Manager (MIM)|组管理|组|更新组|
|Microsoft Identity Manager (MIM)|用户管理|用户|用户密码注册|
|Microsoft Identity Manager (MIM)|用户管理|用户|用户密码重置|
|Privileged Identity Management|角色管理|角色|AccessReview_Review|
|Privileged Identity Management|角色管理|角色|AccessReview_Update|
|Privileged Identity Management|角色管理|角色|ActivationAborted|
|Privileged Identity Management|角色管理|角色|ActivationApproved|
|Privileged Identity Management|角色管理|角色|ActivationCanceled|
|Privileged Identity Management|角色管理|角色|ActivationRequested|
|Privileged Identity Management|角色管理|角色|已添加|
|Privileged Identity Management|角色管理|角色|分配|
|Privileged Identity Management|角色管理|角色|提升|
|Privileged Identity Management|角色管理|角色|已删除|
|Privileged Identity Management|角色管理|角色|角色设置更改|
|Privileged Identity Management|角色管理|角色|ScanAlertsNow|
|Privileged Identity Management|角色管理|角色|注册|
|Privileged Identity Management|角色管理|角色|停用|
|Privileged Identity Management|角色管理|角色|UpdateAlertSettings|
|Privileged Identity Management|角色管理|角色|UpdateCurrentState|
|自助服务组管理|组管理|组|批准要求加入组的挂起请求|
|自助服务组管理|组管理|组|取消要求加入组的挂起请求|
|自助服务组管理|组管理|组|创建生命周期管理策略|
|自助服务组管理|组管理|组|删除要求加入组的挂起请求|
|自助服务组管理|组管理|组|拒绝要求加入组的挂起请求|
|自助服务组管理|组管理|组|续订组|
|自助服务组管理|组管理|组|请求加入组|
|自助服务组管理|组管理|组|设置动态组属性|
|自助服务组管理|组管理|组|更新生命周期管理策略|
|自助服务密码管理|用户管理|用户|被阻止进行自助密码重置|
|自助服务密码管理|用户管理|用户|更改密码(自助服务)|
|自助服务密码管理|目录管理|Directory|对目录禁用密码写回|
|自助服务密码管理|目录管理|Directory|对目录启用密码写回|
|自助服务密码管理|用户管理|用户|重置密码(由管理员)|
|自助服务密码管理|用户管理|用户|重置密码(自助服务)|
|自助服务密码管理|用户管理|用户|自助密码重置流活动进度|
|自助服务密码管理|用户管理|用户|自助密码重置流活动进度|
|自助服务密码管理|用户管理|用户|解锁用户帐户(自助服务)|
|自助服务密码管理|用户管理|用户|用户已注册自助密码重置|
|使用条款|策略管理|策略|接受使用条款|
|使用条款|策略管理|策略|创建使用条款|
|使用条款|策略管理|策略|拒绝使用条款|
|使用条款|策略管理|策略|删除使用条款|
|使用条款|策略管理|策略|编辑使用条款|
|使用条款|策略管理|策略|发布使用条款|
|使用条款|策略管理|策略|取消发布使用条款|




## <a name="next-steps"></a>后续步骤

有关报告的概述，请参阅 [Azure Active Directory 报告](active-directory-reporting-azure-portal.md)。

