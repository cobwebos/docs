---
title: "在逻辑应用中添加 Office 365 用户连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Office 365 用户连接器概述"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2e4af1c9369a97d518d027dc4679b9f01ca53d4c


---
# <a name="get-started-with-the-office-365-users-connector"></a>Office 365 用户连接器入门
连接到 Office 365 用户，获取配置文件、搜索用户等。 

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

通过 Office 365 用户，你可以：

* 根据从 Office 365 用户中获取的数据生成你的业务流。 
* 使用获取直接下属、获取经理的用户配置文件等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，获取用户的直接下属，然后获取此信息并更新 SQL Azure 数据库。 

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Office 365 用户连接器具有以下可用操作。 没有任何触发器。

| 触发器 | 操作 |
| --- | --- |
| 无 |<ul><li>获取经理</li><li>获取我的配置文件</li><li>获取直接下属</li><li>获取用户配置文件</li><li>搜索用户</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。 

## <a name="create-a-connection-to-office-365-users"></a>创建到 Office 365 用户的连接
将此连接器添加到逻辑应用时，必须登录到 Office 365 用户帐户，并允许逻辑应用连接到你的帐户。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

创建连接后，输入 Office 365 用户属性，如用户 ID。 本主题中的 **REST API 参考**介绍了这些属性。

> [!TIP]
> 可在其他逻辑应用中使用此相同 Office 365 用户连接。
> 
> 

## <a name="office-365-users-rest-api-reference"></a>Office 365 用户 REST API 参考
适用于版本：1.0。

### <a name="get-my-profile"></a>获取我的配置文件
检索当前用户的配置文件。  
```GET: /users/me``` 

此调用没有任何参数。

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 202 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-user-profile"></a>获取用户配置文件
检索特定用户配置文件。  
```GET: /users/{userId}``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| userId |字符串 |是 |路径 |无 |用户主体名称或电子邮件 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 202 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-manager"></a>获取经理
检索指定用户的经理的用户配置文件。  
```GET: /users/{userId}/manager``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| userId |字符串 |是 |路径 |无 |用户主体名称或电子邮件 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 202 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="get-direct-reports"></a>获取直接下属
获取直接下属。  
```GET: /users/{userId}/directReports``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| userId |字符串 |是 |路径 |无 |用户主体名称或电子邮件 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 202 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

### <a name="search-for-users"></a>搜索用户
检索用户配置文件的搜索结果。  
```GET: /users``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| searchTerm |字符串 |否 |查询 |无 |搜索字符串（适用于：显示名称、名字、姓氏、邮件、邮件别名和用户主体名称） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 202 |操作成功 |
| 400 |BadRequest |
| 401 |未授权 |
| 403 |禁止 |
| 500 |内部服务器错误 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
#### <a name="user-user-model-class"></a>User：用户模型类
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| DisplayName |字符串 |否 |
| GivenName |字符串 |否 |
| Surname |字符串 |否 |
| Mail |字符串 |否 |
| MailNickname |字符串 |否 |
| TelephoneNumber |字符串 |否 |
| AccountEnabled |布尔值 |否 |
| ID |字符串 |是 |
| UserPrincipalName |字符串 |否 |
| 系 |字符串 |否 |
| JobTitle |字符串 |否 |
| mobilePhone |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

返回到 [API 列表](apis-list.md)。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG



<!--HONumber=Jan17_HO3-->


