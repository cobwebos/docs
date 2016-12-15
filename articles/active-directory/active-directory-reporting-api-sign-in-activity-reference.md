---
title: "Azure Active Directory 登录活动报告 API 参考 |Microsoft 文档"
description: "Azure Active Directory 登录活动报告 API 的参考信息"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4c60c0fd364400c881f1b8b96598725f6bd11d26


---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Azure Active Directory 登录活动报告 API 参考
本主题包含在有关 Azure Active Directory 报告 API 的主题集合中。  
Azure AD 报告提供一个用于通过代码或相关工具访问登录活动报告数据的 API。
本主题提供有关**登录活动报告 API** 的参考信息。

请参阅:

* [登录活动](active-directory-reporting-azure-portal.md#sign-in-activities)，其中介绍了详细的概念信息
* [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)，获取有关报告 API 的详细信息。

有关疑问、问题或反馈，请联系 [AAD 报告帮助](mailto:aadreportinghelp@microsoft.com)。

## <a name="who-can-access-the-api-data"></a>谁可以访问 API 数据？
* 安全管理员或安全读者角色中的用户
* 全局管理员
* 有权访问 API 的任何应用（仅可根据全局管理员的权限设置应用授权）

## <a name="prerequisites"></a>先决条件
若要通过报告 API 访问此报告，必须满足以下条件：

* 拥有 [Azure Active Directory Premium P1 或 P2 版本](active-directory-editions.md)
* 完成了[访问 Azure AD 报告 API 的先决条件](active-directory-reporting-api-prerequisites.md)。 

## <a name="accessing-the-api"></a>访问 API
你可以通过“Graph 浏览器”[](https://graphexplorer2.cloudapp.net)访问此 API，或以编程方式使用 PowerShell 等访问此 API。 为了使 PowerShell 正确解释在 AAD Graph REST 调用中使用的 OData 筛选器语法，必须使用反撇号字符（也称为重音符）对 $ 字符进行“转义”。 反撇号字符用作 [PowerShell 的转义字符](https://technet.microsoft.com/library/hh847755.aspx)，允许 PowerShell 对 $ 字符进行原义解释，并避免将它误用作 PowerShell 变量名称（即 $filter）。

本主题着重介绍了 Graph 浏览器。 有关 PowerShell 示例，请参阅此 [PowerShell 脚本](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script)。

## <a name="api-endpoint"></a>API 终结点
可以使用以下基 URI 访问此 API：  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



由于数据量的原因，此 API 限制为一百万条返回的记录。 

此调用批量返回数据。 每一批中最多有 1000 条记录。  
若要获取下一批记录，请使用“下一个”链接。 从第一组返回的记录中获取 [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) 信息。 跳过标记将在结果集的末尾。  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>支持的筛选器
你可以在筛选器窗体中缩小 API 调用返回的记录数。  
对于与登录 API 相关的数据，支持以下筛选器：

* **$top=\<要返回的记录数\>** - 限制返回的记录数。 此操作成本高昂。 如果你想要返回数以千计的对象，则不应使用此筛选器。  
* **$filter=\<筛选语句\>** - 根据受支持的筛选字段，指定你所关注的记录类型

## <a name="supported-filter-fields-and-operators"></a>支持的筛选字段和运算符
若要指定你所关注的记录类型，可生成包含以下一个或一组筛选字段的筛选语句：

* [signinDateTime](#signindatetime) - 定义日期或日期范围
* [userId](#userid) - 根据用户的 ID 定义特定的用户。
* [userPrincipalName](#userprincipalname) - 根据用户的用户主体名称 (UPN) 定义特定的用户
* [appId](#appid) - 根据应用的 ID 定义特定的应用
* [appDisplayName](#appdisplayname) - 根据应用的显示名称定义特定的应用
* [loginStatus](#loginStatus) - 定义登录状态（成功/失败）

> [!NOTE]
> 使用 Graph 资源管理器时，需要对筛选字段中的每个字母使用正确的大小写。
> 
> 

若要缩小返回的数据范围，可以创建支持的筛选器和筛选器字段组合。 例如，以下语句返回 2016 年 7 月 1 日与 2016 年 7 月 6 日之间的前 10 条记录：

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**支持的运算符**：eq、ge、le、gt、lt

**示例**：

使用特定的日期

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



使用日期范围    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**注释**：

datetime 参数应采用 UTC 格式 

- - -
### <a name="userid"></a>userId
**支持的运算符**：eq

**示例**：

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**注释**：

userId 的值是一个字符串值

- - -
### <a name="userprincipalname"></a>userPrincipalName
**支持的运算符**：eq

**示例**：

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**注释**：

userPrincipalName 的值是一个字符串值

- - -
### <a name="appid"></a>appId
**支持的运算符**：eq

**示例**：

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**注释**：

appId 的值是一个字符串值

- - -
### <a name="appdisplayname"></a>appDisplayName
**支持的运算符**：eq

**示例**：

    $filter=appDisplayName+eq+'Azure+Portal' 


**注释**：

appDisplayName 的值是一个字符串值

- - -
### <a name="loginstatus"></a>loginStatus
**支持的运算符**：eq

**示例**：

    $filter=loginStatus+eq+'1'  


**注释**：

loginStatus 有两个选项：0 - 成功，1 - 失败

- - -
## <a name="next-steps"></a>后续步骤
* 想要查看筛选的登录活动的示例？ 请查看 [Azure Active Directory 登录活动报告 API 示例](active-directory-reporting-api-sign-in-activity-samples.md)。
* 是否要了解有关 Azure AD 报告 API 的详细信息？ 请参阅 [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)。




<!--HONumber=Nov16_HO3-->


