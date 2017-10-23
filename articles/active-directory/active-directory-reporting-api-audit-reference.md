---
title: "Azure Active Directory 审核 API 参考 | Microsoft 文档"
description: "如何开始使用 Azure Active Directory 审核 API"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/05/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 573e940c5390e7b990d889681eb37b73c5b253d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-audit-api-reference"></a>Azure Active Directory 审核 API 参考
本主题包含在有关 Azure Active Directory 报告 API 的主题集合中。  
Azure AD 报告向你提供了一个允许使用代码或相关工具访问审核数据的 API。
本主题用于向你提供有关**审核 API** 的参考信息。

请参阅：

* [审核日志](active-directory-reporting-azure-portal.md#activity-reports)，获取更多概念性信息

* [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)，获取有关报告 API 的详细信息。


对于：

- 常见问题解答，请参阅我们的[常见问题解答](active-directory-reporting-faq.md) 

- 问题，请[提交支持票证](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>谁可以访问该数据？
* 安全管理员或安全读者角色中的用户
* 全局管理员
* 有权访问 API 的任何应用（仅可根据全局管理员的权限设置应用授权）

## <a name="prerequisites"></a>先决条件
为了通过报告 API 访问此报告，必须具备：

* [Azure Active Directory 免费版或更高版本](active-directory-editions.md)
* 完成了[访问 Azure AD 报告 API 的先决条件](active-directory-reporting-api-prerequisites.md)。 

## <a name="accessing-the-api"></a>访问 API
可以通过“Graph 浏览器”[](https://graphexplorer2.cloudapp.net)访问此 API，或以编程方式使用 PowerShell 等访问此 API。 为了使 PowerShell 正确解释在 AAD Graph REST 调用中使用的 OData 筛选器语法，必须使用反撇号字符（也称为重音符）对 $ 字符进行“转义”。 反撇号字符用作 [PowerShell 的转义字符](https://technet.microsoft.com/library/hh847755.aspx)，允许 PowerShell 对 $ 字符进行原义解释，并避免将它误用作 PowerShell 变量名称（即 $filter）。

本主题着重介绍了 Graph 浏览器。 有关 PowerShell 示例，请参阅此 [PowerShell 脚本](active-directory-reporting-api-audit-samples.md#powershell-script)。

## <a name="api-endpoint"></a>API 终结点
可以使用以下 URI 访问此 API：  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Azure AD 审核 API（使用 OData 分页）返回的记录数没有任何限制。
有关报告数据的保留限制，请查看[报告保留策略](active-directory-reporting-retention.md)。

此调用分批返回数据。 每一批中最多有 1000 条记录。  
若要获取下一批记录，请使用“下一个”链接。 从第一组返回的记录中获取 skiptoken 信息。 跳过标记会在结果集的末尾。  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>支持的筛选器
可以缩小显示在筛选器窗体中的 API 调用所返回记录的范围。  
对于与登录 API 相关的数据，支持以下筛选器：

* **$top=\<要返回的记录数\>** - 限制返回的记录数。 此操作成本高昂。 如果想要返回数以千计的对象，则不应使用此筛选器。     
* **$filter=\<筛选语句\>** - 根据受支持的筛选字段，指定所关注的记录类型

## <a name="supported-filter-fields-and-operators"></a>支持的筛选字段和运算符
要指定你所关注的记录类型，可生成包含以下一个或一组筛选字段的筛选语句：

* [activityDate](#activitydate) - 定义日期或日期范围
* [category](#category) - 定义要筛选的类别。
* [activityStatus](#activitystatus) - 定义活动状态
* [activityType](#activitytype) - 定义活动类型
* [activity](#activity) - 将活动定义为字符串  
* [actor/name](#actorname) - 在角色名称的窗体中定义角色
* [actor/objectid](#actorobjectid) - 在角色 ID 的窗体中定义角色   
* [actor/upn](#actorupn) - 在角色的用户主体名称 (UPN) 的窗体中定义角色 
* [target/name](#targetname) - 在角色名称的窗体中定义目标
* [target/objectid](#targetobjectid) - 在目标 ID 的窗体中定义目标  
* [target/upn](#targetupn) - 在角色的用户主体名称 (UPN) 的窗体中定义角色   

- - -
### <a name="activitydate"></a>activityDate
**支持的运算符**：eq、ge、le、gt、lt

**示例**：

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**注释**：

日期时间应采用 UTC 格式

- - -
### <a name="category"></a>category

**支持的值**：

| 类别                         | 值     |
| :--                              | ---       |
| 核心目录                   | Directory |
| 自助服务密码管理 | SSPR      |
| 自助服务组管理    | SSGM      |
| 帐户预配             | 同步      |
| 自动密码滚动更新      | 自动密码滚动更新 |
| 标识保护              | 标识保护 |
| 受邀用户                    | 受邀用户 |
| MIM 服务                      | MIM 服务 |



**支持的运算符**：eq

**示例**：

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>activityStatus

**支持的值**：

| 活动状态 | 值 |
| :--             | ---   |
| 成功         | 0     |
| 失败         | - 1   |

**支持的运算符**：eq

**示例**：

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>activityType
**支持的运算符**：eq

**示例**：

    $filter=activityType eq 'User'    

**注释**：

区分大小写

- - -
### <a name="activity"></a>activity
**支持的运算符**：eq、contains、startsWith

**示例**：

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**注释**：

区分大小写

- - -
### <a name="actorname"></a>actor/name
**支持的运算符**：eq、contains、startsWith

**示例**：

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**注释**：

不区分大小写

- - -
### <a name="actorobjectid"></a>actor/objectId
**支持的运算符**：eq

**示例**：

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>target/name
**支持的运算符**：eq、contains、startsWith

**示例**：

    $filter=targets/any(t: t/name eq 'some name')    

**注释**：

不区分大小写

- - -
### <a name="targetupn"></a>target/upn
**支持的运算符**：eq、startsWith

**示例**：

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**注释**：

* 不区分大小写
* 在查询 Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity 时，需要添加完整命名空间

- - -
### <a name="targetobjectid"></a>target/objectId
**支持的运算符**：eq

**示例**：

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>actor/upn
**支持的运算符**：eq、startsWith

**示例**：

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**注释**：

* 不区分大小写 
* 在查询 Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity 时，需要添加完整命名空间

- - -
## <a name="next-steps"></a>后续步骤
* 是否要查看筛选的系统活动的示例？ 请查看 [Azure Active Directory 审核 API 示例](active-directory-reporting-api-audit-samples.md)。
* 是否要了解有关 Azure AD 报告 API 的详细信息？ 请参阅 [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)。

