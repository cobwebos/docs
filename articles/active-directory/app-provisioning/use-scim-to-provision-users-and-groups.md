---
title: 开发 SCIM 终结点以便将用户预配到 Azure AD 中的应用
description: 跨域身份管理系统 (SCIM) 将自动用户预配标准化。 了解如何开发 SCIM 终结点，将 SCIM API 与 Azure Active Directory 集成，以及开始自动将用户和组预配到云应用程序中。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: fd534443c56612d0c0d67c228cba154fb1db18c3
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967046"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>使用 Azure AD 生成 SCIM 终结点并配置用户预配

应用程序开发人员可以使用跨域身份管理系统 (SCIM) 用户管理 API 实现在应用程序与 Azure AD 之间自动预配用户和组。 本文介绍如何生成 SCIM 终结点以及如何与 Azure AD 预配服务集成。 SCIM 规范提供了预配用的常见用户架构。 与联合标准（例如 SAML 或 OpenID Connect）结合使用时，SCIM 为管理员提供了基于标准的端到端访问权限管理解决方案。

SCIM 是 /Users 终结点和 /Groups 终结点的标准化定义。 它使用常见的 REST 谓词来创建、更新以及删除对象和常见属性（例如组名、用户名、名字、姓氏和电子邮件）的预定义架构。 提供 SCIM 2.0 REST API 的应用可以减少或免去使用专有用户管理 API 带来的麻烦。 例如，任何兼容的 SCIM 客户端都知道如何将 JSON 对象的 HTTP POST 发送到 /Users 终结点，以创建新的用户条目。 符合 SCIM 标准的应用不需要采用略有不同的 API 来执行相同的基本操作，而是可以立即使用现有的客户端、工具和代码。 

![使用 SCIM 从 Azure AD 预配到应用](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642)、[7643](https://tools.ietf.org/html/rfc7643)、[7644](https://tools.ietf.org/html/rfc7644)) 中定义的用于管理的标准用户对象架构和 REST API 允许标识提供者和应用更轻松地相互集成。 生成 SCIM 终结点的应用程序开发人员无需执行自定义工作即可与任何符合 SCIM 的客户端集成。

将预配到应用程序自动化需要生成 SCIM 终结点并将其与 Azure AD SCIM 客户端集成。 执行以下步骤，开始将用户和组预配到应用程序中。 
    
  * **[步骤 1：设计用户和组架构。](#step-1-design-your-user-and-group-schema)** 确定应用程序所需的对象和属性，并确定它们如何映射到 Azure AD SCIM 实现支持的用户和组架构。

  * **[步骤 2：了解 Azure AD SCIM 实现。](#step-2-understand-the-azure-ad-scim-implementation)** 了解如何实现 Azure AD SCIM 客户端，并对 SCIM 协议请求处理和响应进行建模。

  * **[步骤 3：生成 SCIM 终结点。](#step-3-build-a-scim-endpoint)** 终结点必须与 SCIM 2.0 兼容，才能与 Azure AD 预配服务集成。 可以选择使用 Microsoft 公共语言基础结构 (CLI) 库和代码示例来生成终结点。 这些示例仅供参考和测试；我们不建议在编写生产应用的代码时依赖这些示例。

  * **[步骤 4：将 SCIM 终结点与 Azure AD SCIM 客户端集成。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 如果组织使用的第三方应用程序实现了 Azure AD 支持的 SCIM 2.0 的配置文件，则可以立即开始自动预配和取消预配用户和组。

  * **[步骤 5：将应用程序发布到 Azure AD 应用程序库。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 使客户轻松发现应用程序和配置预配。 

![将 SCIM 终结点与 Azure AD 集成的相关步骤](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>步骤 1：设计用户和组架构

每个应用程序都需要不同的属性才能创建用户或组。 通过标识应用程序所需的对象（users 和 groups）和属性（name、manager、job title 等）开始集成。 SCIM 标准定义用于管理用户和组的架构。 核心用户架构只需三个属性：ID（服务提供商定义的标识符）、externalId（客户端定义的标识符）和 meta（服务提供商维护的只读元数据）  。 所有其他属性都是可选的。 除了核心用户架构外，SCIM 标准还定义了企业用户扩展和用于扩展用户架构的模型，以满足应用程序的需求。 例如，如果应用程序需要用户的管理员，则可以使用企业用户架构来收集用户的管理员，并使用核心架构来收集用户的电子邮件。 要设计架构，请按照以下步骤操作：
  1. 列出应用程序所需的属性。 将要求细分为身份验证所需的属性（例如 loginName 和电子邮件）、管理用户生命周期所需的属性（例如状态/活动）以及特定应用程序正常运行所需的其他属性（例如管理员、标记）可能会有所帮助。
  2. 检查这些属性是否已在核心用户架构或企业用户架构中定义。 如果核心或企业用户架构中不包含需要的任何属性，则需要定义用户架构的扩展以涵盖所需属性。 在下面的示例中，我们为用户添加了扩展，以允许为用户预配“标记”。 最好仅从核心和企业用户架构开始，然后再扩展到其他自定义架构。  
  3. 将 SCIM 属性映射到 Azure AD 中的用户属性。 如果在 SCIM 终结点中定义的某个属性在 Azure AD 用户架构上没有明确的对应项，则很有可能大多数租户根本没有将数据存储在用户对象上。 请考虑此属性是否为用于创建用户的可选属性。 如果该属性对于应用程序能否正常运行至关重要，请指导租户管理员扩展其架构或将如下所示的扩展属性用于“tags”属性。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表 1：概述所需的属性 
| 步骤 1：确定应用所需的属性| 步骤 2：将应用要求映射到 SCIM 标准| 步骤 3：将 SCIM 属性映射到 Azure AD 属性|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|Emails[type eq “work”].value|Mail|
|manager|manager|manager|
|标记|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|活动|isSoftDeleted（计算值不存储在用户上）|

上面定义的架构将使用下面的 JSON 有效负载来表示。 请注意，除应用程序所需的属性外，JSON 表示形式还包含所需的 `id` 、 `externalId` 和 `meta` 属性。

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>表 2：默认用户属性映射
然后，可以使用下表了解应用程序所需的属性如何映射到 Azure AD 和 SCIM RFC 中的属性。 可以[自定义](customize-application-attributes.md)在 Azure AD 和 SCIM 终结点之间映射属性的方式。 请注意，不需要同时支持用户和组，也不需要同时支持以下所示的所有属性。 在考虑如何将 Azure AD 中的属性经常映射到 SCIM 协议中的属性时，它们可以作为参考。 

| Azure Active Directory 用户 | “urn:ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |活动 |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>表 3：默认组属性映射

| Azure Active Directory 组 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC 中定义了多个终结点。 可以从 /User 终结点开始，然后从该处扩展。 /Schemas 终结点在使用自定义属性或架构频繁更改时非常有用。 它使客户端能够自动检索最新的架构。 /Bulk 终结点在支持组时特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 /Schemas 终结点在使用自定义属性或架构频繁更改时非常有用。 它使客户端能够自动检索最新的架构。 /Bulk 终结点在支持组时特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表 4：确定要开发的终结点
|ENDPOINT|DESCRIPTION|
|--|--|
|/User|对用户对象执行 CRUD 操作。|
|/Group|对组对象执行 CRUD 操作。|
|/ServiceProviderConfig|详细说明受支持的 SCIM 标准的特点（例如受支持的资源和身份验证方法）。|
|/ResourceTypes|指定有关每个资源的元数据|
|/Schemas|每个客户端和服务提供商支持的属性集可能有所不同。 一个服务提供程序可以包括 `name` 、 `title` 和 `emails` ，而另一个服务提供程序使用 `name` 、 `title` 和 `phoneNumbers` 。 利用架构终结点可以发现受支持的属性。|
|/Bulk|使用批量操作，你可以通过单个操作（例如更新大型组的成员身份）对大量资源对象执行操作。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>步骤 2：了解 Azure AD SCIM 实现
> [!IMPORTANT]
> Azure AD SCIM 实现的行为最近于 2018 年 12 月 18 日更新。 有关更改内容的信息，请参阅 [Azure AD 用户预配服务 SCIM 2.0 协议合规性](application-provisioning-config-problem-scim-compatibility.md)。

如果你正在构建支持 SCIM 2.0 用户管理 API 的应用程序，则本部分详细介绍了如何实现 Azure AD SCIM 客户端。 本部分还介绍了如何为 SCIM 协议请求处理和响应建模。 实现 SCIM 终结点后，就可以按照上一部分中描述的过程对其进行测试。

在 [SCIM 2.0 协议规范](http://www.simplecloud.info/#Specification)中，应用程序必须符合以下要求：

* 支持根据 [SCIM 协议第 3.3 节](https://tools.ietf.org/html/rfc7644#section-3.3)创建用户和组（可选）。  
* 支持根据 [SCIM 协议第 3.5.2 节](https://tools.ietf.org/html/rfc7644#section-3.5.2)修改具有 PATCH 请求的用户或组。 支持可确保以高性能的方式预配组和用户。 
* 支持根据 [SCIM 协议第 3.4.1 节](https://tools.ietf.org/html/rfc7644#section-3.4.1)为先前创建的用户或组检索已知资源。  
* 支持根据 [SCIM 协议第 3.4.2 节](https://tools.ietf.org/html/rfc7644#section-3.4.2)查询用户或组。  默认情况下，按 `id` 检索用户，按 `username` 和 `externalId` 查询用户，以及按 `displayName` 查询组。  
* 支持根据 SCIM 协议第 3.4.2 节，按 ID 和管理员查询用户。  
* 支持根据 SCIM 协议第 3.4.2 节，按 ID 和成员查询组。  
* 接受使用一个持有者令牌对应用程序进行 Azure AD 身份验证和授权。
* 支持软删除用户 `active=false` 并还原用户 `active=true` (用户对象应在请求中返回，无论用户是否) 活动。 只有当用户从应用程序中进行了硬删除时，才应返回该用户。 

实现 SCIM 终结点时，请遵循以下一般准则，以确保与 Azure AD 兼容：

* `id` 是所有资源的必需属性。 每个返回资源的响应都应确保每个资源都具有此属性，但成员数量为零的 `ListResponse` 除外。
* 对查询/筛选请求的响应应始终为 `ListResponse`。
* 组是可选项，但只有在 SCIM 实现支持 PATCH 请求时才受支持。
* 不必在 PATCH 响应中包含整个资源。
* Microsoft Azure AD 仅使用以下运算符：  
    - `eq`
    - `and`
* 对于 SCIM 中的结构元素，不要求区分大小写匹配，尤其是 https://tools.ietf.org/html/rfc7644#section-3.5.2 中定义的 PATCH `op` 操作值。 Azure AD 以 `Add`、`Replace` 和 `Remove` 的形式发出“op”的值。
* Microsoft Azure AD 发出用于提取随机用户和组的请求，以确保终结点和凭据有效。 在 [Azure 门户](https://portal.azure.com)的“测试连接”流中也会完成此操作。 
* 应在 [Azure 门户](https://portal.azure.com)中将可以查询其资源的属性设置为应用程序的匹配属性。 有关详细信息，请参阅[自定义用户预配属性映射](customize-application-attributes.md)
* 在 SCIM 终结点上支持 HTTPS

### <a name="user-provisioning-and-deprovisioning"></a>用户预配和取消预配

下图显示了 Azure Active Directory 发送到 SCIM 服务以管理应用程序的标识存储区中用户的生命周期的消息。  

![显示用户预配和取消预配顺序](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
用户预配和取消预配顺序

### <a name="group-provisioning-and-deprovisioning"></a>组预配和取消预配

组预配和取消预配是可选操作。 在实现并启用后，下图显示了 Azure AD 发送到 SCIM 服务以管理应用程序的标识存储区中组的生命周期的消息。  这些消息在以下两个方面与用户相关的消息不同：

* 检索组的请求指定将成员属性从请求响应中提供的任何资源中排除。  
* 确定引用属性是否具有特定值的请求是有关成员属性的请求。  

![显示组预配和取消预配顺序](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
组预配和取消预配顺序

### <a name="scim-protocol-requests-and-responses"></a>SCIM 协议请求和响应
本节提供了 Azure AD SCIM 客户端发出的示例 SCIM 请求和示例预期响应。 为了获得最佳结果，应将应用编码为按此格式处理这些请求并发出预期的响应。

> [!IMPORTANT]
> 若要了解 Azure AD 用户预配服务如何以及何时发出下面所述的操作，请参阅[预配工作原理](how-provisioning-works.md)中的[预配周期：初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)部分。

[用户操作](#user-operations)
  - [创建用户](#create-user)（[请求](#request) / [响应](#response)）
  - [获取用户](#get-user)（[请求](#request-1) / [响应](#response-1)）
  - [通过查询获取用户](#get-user-by-query)（[请求](#request-2) / [响应](#response-2)）
  - [通过查询获取用户 - 零个结果](#get-user-by-query---zero-results)（[请求](#request-3)
/ [响应](#response-3)）
  - [更新用户[多值属性]](#update-user-multi-valued-properties)（[请求](#request-4) /  [响应](#response-4)）
  - [更新用户[单值属性]](#update-user-single-valued-properties)（[请求](#request-5)
/ [响应](#response-5)） 
  - [禁用用户](#disable-user)（[请求](#request-14) / 
[响应](#response-14)）
  - [删除用户](#delete-user)（[请求](#request-6) / 
[响应](#response-6)）


[组操作](#group-operations)
  - [创建组](#create-group)（[请求](#request-7) / [响应](#response-7)）
  - [获取组](#get-group)（[请求](#request-8) / [响应](#response-8)）
  - [按 displayName 获取组](#get-group-by-displayname)（[请求](#request-9) / [响应](#response-9)）
  - [更新组[非成员属性]](#update-group-non-member-attributes)（[请求](#request-10)/
 [响应](#response-10)）
  - [更新组[添加成员]](#update-group-add-members)（[请求](#request-11) /
[响应](#response-11)）
  - [更新组[删除成员]](#update-group-remove-members)（[请求](#request-12) /
[响应](#response-12)）
  - [删除组](#delete-group)（[请求](#request-13) /
[响应](#response-13)）

### <a name="user-operations"></a>用户操作

* 可以按 `userName` 或 `email[type eq "work"]` 属性查询用户。  

#### <a name="create-user"></a>创建用户

###### <a name="request"></a>请求

POST /Users
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>响应

HTTP/1.1 201 已创建
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>获取用户

###### <a name="request"></a><a name="request-1"></a>请求
GET /Users/5d48a0a8e9f04aa38008 

###### <a name="response-user-found"></a><a name="response-1"></a>响应（找到用户）
HTTP/1.1 200 OK
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>请求
GET /Users/5171a35d82074e068ce2 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>响应（找不到用户。 请注意，不需要详细信息，只需要状态。）

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>通过查询获取用户

##### <a name="request"></a><a name="request-2"></a>请求

GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"

##### <a name="response"></a><a name="response-2"></a>响应

HTTP/1.1 200 OK
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>通过查询获取用户 - 零个结果

##### <a name="request"></a><a name="request-3"></a>请求

GET /Users?filter=userName eq "non-existent user"

##### <a name="response"></a><a name="response-3"></a>响应

HTTP/1.1 200 OK
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>更新用户[多值属性]

##### <a name="request"></a><a name="request-4"></a>请求

PATCH /Users/6764549bef60420686bc HTTP/1.1
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>响应

HTTP/1.1 200 OK
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>更新用户[单值属性]

##### <a name="request"></a><a name="request-5"></a>请求

PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>响应

HTTP/1.1 200 OK
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>禁用用户

##### <a name="request"></a><a name="request-14"></a>请求

PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>响应

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>删除用户

##### <a name="request"></a><a name="request-6"></a>请求

DELETE /Users/5171a35d82074e068ce2 HTTP/1.1

##### <a name="response"></a><a name="response-6"></a>响应

HTTP/1.1 204 无内容

### <a name="group-operations"></a>组操作

* 应始终使用空的成员列表创建组。
* 可以按 `displayName` 属性查询组。
* 组 PATCH 请求的更新应在响应中生成“HTTP 204 无内容”。 不建议返回包含所有成员列表的正文。
* 不需要支持返回组的所有成员。

#### <a name="create-group"></a>创建组

##### <a name="request"></a><a name="request-7"></a>请求

POST /Groups HTTP/1.1
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>响应

HTTP/1.1 201 已创建
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>获取组

##### <a name="request"></a><a name="request-8"></a>请求

GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1

##### <a name="response"></a><a name="response-8"></a>响应
HTTP/1.1 200 OK
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>按 displayName 获取组

##### <a name="request"></a><a name="request-9"></a>请求
GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1

##### <a name="response"></a><a name="response-9"></a>响应

HTTP/1.1 200 OK
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>更新组[非成员属性]

##### <a name="request"></a><a name="request-10"></a>请求

PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>响应

HTTP/1.1 204 无内容

### <a name="update-group-add-members"></a>更新组[添加成员]

##### <a name="request"></a><a name="request-11"></a>请求

PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>响应

HTTP/1.1 204 无内容

#### <a name="update-group-remove-members"></a>更新组[删除成员]

##### <a name="request"></a><a name="request-12"></a>请求

PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>响应

HTTP/1.1 204 无内容

#### <a name="delete-group"></a>删除组

##### <a name="request"></a><a name="request-13"></a>请求

DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1

##### <a name="response"></a><a name="response-13"></a>响应

HTTP/1.1 204 无内容

### <a name="security-requirements"></a>安全要求
TLS 协议版本

唯一可接受的 TLS 协议版本为 TLS 1.2 和 TLS 1.3。 不允许使用其他版本的 TLS。 不允许使用任何版本的 SSL。 
- RSA 密钥必须至少为 2,048 位。
- ECC 密钥必须至少为 256 位，用批准的椭圆曲线生成


**密钥长度**

所有服务必须使用 X.509 证书，该证书使用足够长的加密密钥生成，这意味着：

**密码套件**

必须严格按照下面指定的顺序将所有服务配置为使用以下密码套件。 请注意，如果只拥有 RSA 证书，则安装的 ECDSA 密码套件无效。 </br>

TLS 1.2 密码套件最低标准：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP 范围
Azure AD 预配服务当前在 AzureActiveDirectory 的 IP 范围下[运行，如下所列。](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all) 可以添加 AzureActiveDirectory 标记下面列出的 IP 范围，以允许从 Azure AD 预配服务到应用程序的流量。 请注意，你将需要仔细检查 "IP 范围" 列表中的计算地址。 地址（如 "40.126.25.32"）可在 IP 范围列表中表示为 "40.126.0.0/18"。 你还可以使用以下 [API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)以编程方式检索 IP 范围列表。

## <a name="step-3-build-a-scim-endpoint"></a>步骤 3：生成 SCIM 终结点

既然你已设计了架构并了解了 Azure AD SCIM 实现，现在可以开始开发 SCIM 终结点了。 可以依赖于由 SCIM 社区发布的许多开源 SCIM 库，而不必从头开始完全靠自己构建实现。

Azure AD 预配团队发布的开源 .NET Core [参考代码](https://aka.ms/SCIMReferenceCode)就是其中一种可以立即开始进行开发的资源。 生成 SCIM 终结点之后，你将需要对其进行测试。可以使用作为参考代码一部分提供的 [postman 测试](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)集合，也可以运行[上面](#user-operations)提供的示例请求/响应。  

   > [!Note]
   > 该参考代码按“原样”提供，旨在帮助你开始生成 SCIM 终结点。 欢迎社区供稿，以帮助构建和维护代码。

解决方案由两个项目组成：Microsoft.SCIM 和 Microsoft.SCIM.WebHostSample 。

Microsoft.SCIM 项目是一个库，用于定义符合 SCIM 规范的 Web 服务的组件。 该项目声明接口 Microsoft.SCIM.IProvider，请求被转换为对提供程序方法的调用，这些方法将经过编程，可对标识存储区进行操作。

![明细：请求转换为对提供程序方法的调用](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Microsoft.SCIM.WebHostSample 项目是一个基于空模板的 Visual Studio ASP.NET Core Web 应用程序 。 这使得示例代码可以进行独立部署以及托管在容器或 Internet Information Services 中。 该项目还实现 Microsoft.SCIM.IProvider 接口，将类保留在内存中作为示例标识存储。

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>生成自定义 SCIM 终结点

SCIM 服务必须具有 HTTP 地址和服务器身份验证证书，其中的根证书颁发机构为下列其中之一：

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK 包括可在开发过程中使用的 HTTPS 开发证书，该证书在首次运行体验中安装。 根据运行 ASP.NET Core Web 应用程序的方式，它将侦听其他端口：

* Microsoft.SCIM.WebHostSample： https://localhost:5001
* IIS Express： https://localhost:44359/

有关 ASP.NET Core 中 HTTPS 的详细信息，请使用以下链接：[在 ASP.NET Core 中强制使用 HTTPS](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>处理终结点身份验证

来自 Azure Active Directory 的请求包括 OAuth 2.0 持有者令牌。 接收请求的任何服务都应将颁发者作为预期 Azure Active Directory 租户的 Azure Active Directory 进行身份验证。

在令牌中，颁发者由 iss 声明标识，例如 `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`。 在此示例中，声明值的基址 `https://sts.windows.net` 将 Azure Active Directory 标识为颁发者，而相对地址段 cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 是获得令牌的 Azure Active Directory 租户的唯一标识符。

令牌的受众将是库中应用程序的应用程序模板 ID，在单个租户中注册的每个应用程序都可能收到与 SCIM 请求相同的 `iss` 声明。 所有自定义应用的应用程序模板 ID 都是 8adf8e6e-67b2-4cf2-a259-e3dc5476c621。 Azure AD 预配服务生成的令牌仅应用于测试。 它不应用于生产环境。

在示例代码中，使用 Microsoft.AspNetCore.Authentication.JwtBearer 包对请求进行身份验证。 以下代码强制使用 Azure Active Directory 为指定租户颁发的持有者令牌来验证对任何服务终结点的请求：

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

使用提供的 [postman 测试](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)以及使用 localhost 执行本地调试也需要持有者令牌。 该示例代码使用 ASP.NET Core 环境在开发阶段更改身份验证选项并启用自签名令牌。

有关 ASP.NET Core 中多个环境的详细信息，请访问以下链接：[在 ASP.NET Core 中使用多个环境](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

以下代码强制使用通过自定义密钥签名的持有者令牌来验证对任何服务终结点的请求：

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

向令牌控制器发送 GET 请求以获取有效的持有者令牌，方法 GenerateJSONWebToken 负责创建与配置用于开发的参数匹配的令牌：

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>处理用户预配和取消预配

示例 1.查询服务以找到匹配的用户

Azure Active Directory `externalId` 在 Azure AD 中查询其属性值与用户的 mailNickname 属性值相匹配的用户的服务。 查询以类似此例的超文本传输协议 (HTTP) 请求形式表示，其中，jyoung 是 Azure Active Directory 中某个用户的 mailNickname 示例。

>[!NOTE]
> 这只是一个示例。 并非所有用户都具有 mailNickname 属性，并且该用户具有的值可能不是目录中的唯一值。 此外，用于匹配的属性 (在此情况下 `externalId`) 可在 [Azure AD 属性映射](customize-application-attributes.md)中进行配置。

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

在示例代码中，请求被转换为对服务提供程序的 QueryAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

在示例查询中，对于具有给定属性值的用户 `externalId` ，传递给 QueryAsync 方法的参数的值是：

* parameters.AlternateFilters.Count:1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator:ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

示例 2.预配用户

如果对具有与用户的 mailNickname 属性值匹配的属性值的用户对 web 服务的查询的响应 `externalId` 未返回任何用户，则 Azure Active Directory 请求服务将用户设置为 Azure Active Directory 中的用户。  以下是此类请求的示例： 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

在示例代码中，请求被转换为对服务提供程序的 CreateAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

在预配用户的请求中，资源参数的值是 Microsoft.SCIM.Schemas 库中定义的 Microsoft.SCIM.Core2EnterpriseUser 类的实例。  如果预配用户的请求成功，则该方法的实现应返回 Microsoft.SCIM.Core2EnterpriseUser 类的实例，并将 Identifier 属性的值设置为新预配用户的唯一标识符。  

示例 3.查询用户的当前状态 

为了更新存在于前端为 SCIM 的标识存储中的已知用户，Azure Active Directory 将通过类似于下面的请求向服务请求该用户的当前状态来继续处理： 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

在示例代码中，请求被转换为对服务提供程序的 RetrieveAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

在检索用户当前状态的请求示例中，作为参数自变量值提供的对象属性值如下所示： 
  
* 标识符："54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

示例 4.查询要更新的引用属性的值 

如果要更新引用属性，Azure Active Directory 将查询服务以判断以该服务为前端的标识存储区中的引用属性的当前值是否已经与 Azure Active Directory 中该属性的值相匹配。 对于用户，以这种方式查询当前值的唯一属性是 manager 属性。 以下是确定用户对象的 manager 属性当前是否具有特定值的请求示例：在示例代码中，请求被转换为对服务提供程序的 QueryAsync 方法的调用。 作为参数自变量值提供的对像属性值如下： 
  
* parameters.AlternateFilters.Count:2
* parameters.AlternateFilters.ElementAt(x).AttributePath:“ID”
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator:ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:"54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator:ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:"2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0):“ID”
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

此处，索引 x 的值可以是 0 并且索引 y 的值可以是 1，或者，x 的值可以是 1 并且 y 的值可以是 0，具体根据筛选器查询参数表达式的顺序而定。   

示例 5.从 Azure AD 向 SCIM 服务发出更新用户请求 

以下是从 Azure Active Directory 向 SCIM 服务发出更新用户请求的示例： 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

在示例代码中，请求被转换为对服务提供程序的 UpdateAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

在更新用户的请求示例中，作为修补参数值提供的对象具有这些属性值： 
  
* ResourceIdentifier.Identifier:"54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count:1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName:OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count:1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value:2819c223-7f76-453a-919d-413861904646

示例6.取消预配用户

若要从前端为 SCIM 服务的标识存储区中取消预配用户，Azure AD 会发送如下请求：

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

在示例代码中，请求被转换为对服务提供程序的 DeleteAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

在取消预配用户的请求示例中，作为 resourceIdentifier 参数值提供的对象具有以下属性值： 

* ResourceIdentifier.Identifier:"54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>步骤 4：将 SCIM 终结点与 Azure AD SCIM 客户端集成

可以将 Azure AD 配置为自动将分配的用户和组预配给实现 [SCIM 2.0 协议](https://tools.ietf.org/html/rfc7644)的特定配置文件的应用程序。 有关配置文件的详细信息，请参阅[步骤 2：了解 Azure AD SCIM 实现](#step-2-understand-the-azure-ad-scim-implementation)。

请咨询应用程序提供者，或参阅应用程序提供者文档中的说明，以了解是否符合这些要求。

> [!IMPORTANT]
> Azure AD SCIM 实现基于 Azure AD 用户预配服务构建，旨在使用户在 Azure AD 与目标应用程序之间持续保持同步，并实现一组非常具体的标准操作。 请务必了解这些行为，这样才能了解 Azure AD SCIM 客户端的行为。 有关详细信息，请参阅[预配工作原理](how-provisioning-works.md)中的[预配周期：初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)部分。

### <a name="getting-started"></a>入门

支持本文所述 SCIM 配置文件的应用程序可以使用 Azure AD 应用程序库中的“非库应用程序”功能连接到 Azure Active Directory。 连接后，Azure AD 将每隔 40 分钟运行同步过程，此过程将为分配的用户和组查询应用程序的 SCIM 终结点，并根据分配详细信息创建或修改这些用户和组。

**连接到支持 SCIM 的应用程序：**

1. 登录到 [Azure Active Directory 门户](https://aad.portal.azure.com)。 请注意，可以通过注册[开发人员计划](https://developer.microsoft.com/office/dev-program)来获得具有 P2 许可证的免费试用版 Azure Active Directory
2. 从左窗格中选择“企业应用程序”。 系统显示所有已配置的应用（包括从库中添加的应用）的列表。
3. 选择“+ 新建应用程序” > “全部” > “非库应用程序”  。
4. 输入应用程序的名称，然后选择“添加”创建应用对象。 新的应用将添加到企业应用程序列表中，并在应用管理屏幕中打开。

   ![屏幕截图显示 Azure AD 应用程序库](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   Azure AD 应用程序库

5. 在应用管理屏幕的左侧面板中，选择“预配”。
6. 在“预配模式”菜单中，选择“自动” 。

   ![示例：Azure 门户中的应用的“预配”页面](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   在 Azure 门户中配置预配

7. 在“租户 URL”字段中，输入应用程序的 SCIM 终结点的 URL。 示例： `https://api.contoso.com/scim/`
8. 如果 SCIM 终结点需要来自非 Azure AD 颁发者的 OAuth 持有者令牌，可将所需的 OAuth 持有者令牌复制到可选的“密钥令牌”字段。 如果此字段留空，则 Azure AD 会在每个请求中包含从 Azure AD 颁发的 OAuth 持有者令牌。 将 Azure AD 用作标识提供者的应用可以验证 Azure AD 颁发的此令牌。 
   > [!NOTE]
   > 不建议将此字段留空并依赖 Azure AD 生成的令牌。 此选项主要用于测试目的。
9. 选择“测试连接”，使 Azure Active Directory 尝试连接到 SCIM 终结点。 如果尝试失败，则显示错误信息。  

    > [!NOTE]
    > **测试连接**使用随机 GUID 作为在 Azure AD 配置中选择的匹配属性，针对不存在的用户查询 SCIM 终结点。 预期正确响应为“HTTP 200 正常”以及空的 SCIM ListResponse 消息。

10. 如果尝试连接应用程序成功，请选择“保存”以保存管理员凭据。
11. 在“映射”部分中有两个可选的[属性映射](customize-application-attributes.md)集：一个用于用户对象，一个用于组对象。 分别选择它们，查看从 Azure Active Directory 同步到应用的属性。 选为“匹配”属性的特性用于匹配应用中的用户和组，以执行更新操作。 选择“保存”，提交所有更改。

    > [!NOTE]
    > 也可通过禁用“组”映射来选择性禁用组对象的同步。

12. “设置”下的“作用域”字段定义同步的用户和组 。 选择“仅同步分配的用户和组”（推荐），以便仅同步“用户和组”选项卡中分配的用户和组 。
13. 配置完成后，请将“预配状态”设置为“启用” 。
14. 选择“保存”以启用 Azure AD 预配服务。
15. 如果仅同步分配的用户和组（推荐），请确保选择“用户和组”选项卡，并分配要同步的用户或组。

初始周期开始后，可以在左侧面板中选择“预配日志”来监视进度，该日志会显示预配服务在应用上执行的所有操作。 若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始周期执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>步骤 5：将应用程序发布到 Azure AD 应用程序库

如果要构建将由多个租户使用的应用程序，则可以将其置于 Azure AD 应用程序库。 这样，组织就可以轻松发现应用程序和配置预配。 轻松地在 Azure AD 库中发布应用并将预配提供给其他人。 在[此处](../azuread-dev/howto-app-gallery-listing.md)查看步骤。 Microsoft 将与你合作，将应用程序集成到库中、测试终结点，并发布上架[文档](../saas-apps/tutorial-list.md)供客户使用。 

### <a name="gallery-onboarding-checklist"></a>库上架清单
请按照下面的清单进行操作，以确保应用程序能够快速上架，并且客户可以享受顺畅的部署体验。 当上架到库时，系统将向你收集相关信息。 
> [!div class="checklist"]
> * 支持 [SCIM 2.0](#step-2-understand-the-azure-ad-scim-implementation) 用户和组终结点（仅需要其中一项支持，但建议同时确保这两项支持）
> * 支持每个租户的至少25个请求，以确保预配和取消预配用户和组，而无需延迟 (要求) 
> * 确定工程和支持联系人，以便在上架到库后为客户提供指导（必需）
> * 3 个应用程序的未过期测试凭据（必需）
> * 如下所述，支持 OAuth 授权代码许可或长期有效的令牌（必需）
> * 建立工程和支持联系点，以便在上架到库后为客户提供支持（必需）
> * 支持使用单个 PATCH 更新多个组成员身份（推荐） 
> * 公开记录 SCIM 终结点（推荐） 
> * [支持架构发现](https://tools.ietf.org/html/rfc7643#section-6)（推荐）


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>用于在应用程序库中预配连接器的授权
SCIM 规范未定义用于身份验证和授权的特定于 SCIM 的方案。 它依赖于使用现有行业标准。 Azure AD 预配客户端支持库中应用程序的两种授权方法。 

|授权方法|优点|缺点|支持|
|--|--|--|--|
|用户名和密码（Azure AD 不推荐或不支持）|易于实现|不安全 - [你的密码无关紧要](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|库应用根据具体情况提供支持。 非库应用不支持。|
|长期持有者令牌|长期令牌不要求用户提供此令牌。 管理员在设置预配时可以轻松地使用这些令牌。|如果不使用电子邮件等不安全的方法，则很难与管理员共享长期令牌。 |库应用和非库应用均支持。 |
|OAuth 授权代码许可|访问令牌的生存期比密码短得多，并且具有长期持有者令牌不具有的自动刷新机制。  初次授权时，真实用户必须提供该令牌，从而增加了责任级别。 |要求用户提供。 如果用户离开组织，则令牌无效，并且需要再次完成授权。|支持库应用，但不支持非库应用。 但是，可以在 UI 中提供一个访问令牌作为短期测试用途的机密令牌。 在积压工作（backlog）中支持对非库的 OAuth 代码授予。|
|OAuth 客户端凭据许可|访问令牌的生存期比密码短得多，并且具有长期持有者令牌不具有的自动刷新机制。 授权代码许可和客户端凭据许可都创建相同类型的访问令牌，因此换用这些方法对 API 是透明的。  预配可以完全自动执行，无需用户干预即可以无提示的方式请求提供新令牌。 ||库应用和非库应用不支持。 支持已在我们的积压工作 (backlog) 中。|

> [!NOTE]
> 不建议在 Azure AD 预配配置自定义应用 UI 中将令牌字段留空。 生成的令牌主要用于测试目的。

OAuth 授权代码许可流：预配服务支持[授权代码许可](https://tools.ietf.org/html/rfc6749#page-24)。 在提交了在库中发布应用的请求后，我们的团队将与你合作收集以下信息：
*  授权 URL：客户端通过用户代理重定向从资源所有者获得授权时所使用的 URL。 将用户重定向到此 URL 以授予访问权限。 请注意，当前无法为每个租户配置此 URL。
*  令牌交换 URL：客户端通常通过客户端身份验证来交换访问令牌的授权许可时所使用的 URL。 请注意，当前无法为每个租户配置此 URL。
*  客户端 ID：授权服务器向注册的客户端发出客户端标识符，该客户端标识符是表示客户端提供的注册信息的唯一字符串。  客户端标识符不是机密；它向资源所有者公开，并且不得单独用于客户端身份验证。  
*  客户端密码：客户端密码是授权服务器生成的机密。 它应是只有授权服务器知道的唯一值。 

请注意，由于公开了客户端密码，因此 OAuth v1 不受支持。 OAuth v2 受支持。  

最佳做法（不是必需的，但建议执行）：
* 支持多个重定向 URL。 管理员可以通过“portal.azure.com”和“aad.portal.azure.com”配置预配。 支持多个重定向 URL 将确保用户可以从任一门户授予访问权限。
* 支持多个机密可确保机密续订在不停机的情况下顺利进行。 

OAuth 代码授予流中的步骤：
1. 用户登录到 Azure 门户 > 企业应用程序 > 选择 "应用程序 > 预配" > 单击 "授权"。
2. Azure 门户将用户重定向到第三方应用)  (登录页。
3. 管理员向第三方应用程序提供凭据。 
4. 第三方应用将用户重定向到 Azure 门户，并提供授予代码 
5. Azure AD 预配服务将调用令牌 URL 并提供授权代码。 第三方应用程序使用访问令牌、刷新令牌和到期日期做出响应
6. 预配周期开始时，服务会检查当前访问令牌是否有效，并根据需要将其与新令牌交换。 每次向应用发出请求时提供访问令牌，并在每次请求前检查请求的有效性。

> [!NOTE]
> 虽然目前不可能在非库应用程序上设置 OAuth，但你可以手动从授权服务器生成访问令牌，并在非库应用程序的 "机密令牌" 字段中输入该令牌。 这样，你就可以在载入应用程序库之前验证 SCIM 服务器与 Azure AD SCIM 客户端的兼容性，从而支持 OAuth 代码授予。  

**长时间生存期 OAuth 持有者令牌：** 如果你的应用程序不支持 OAuth 授权代码授予流，你还可以生成一个长期的 OAuth 持有者令牌，而不是管理员可用于设置预配集成。 该令牌应永不过期，否则当令牌过期时，预配作业将被[隔离](application-provisioning-quarantine-status.md)。 此令牌的大小必须小于 1 KB。  

有关其他身份验证和授权方法，请在 [UserVoice](https://aka.ms/appprovisioningfeaturerequest) 上告诉我们。

### <a name="gallery-go-to-market-launch-check-list"></a>库上市投放检查列表
为了帮助提高联合集的认知度和需求，我们建议你更新现有文档并更加详细地说明营销渠道中的集成。  建议完成下面的一系列清单活动，以支持投放

* 销售和客户支持就绪情况。 确保销售团队和支持团队了解集成功能并进行适当宣传。 向销售和支持团队介绍简要情况，向他们提供常见问题解答，并将集成添加到销售材料中。 
* 博客文章和/或新闻稿。 撰写介绍联合集成、优点以及入门方法的博客文章或新闻稿。 [示例：Imprivata 和 Azure Active Directory 新闻稿](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* 社交媒体。 利用社交媒体（例如 Twitter、Facebook 或 LinkedIn）向客户推广集成。 请确保包含 @AzureAD，以便转推你的帖子。 [示例：Imprivata Twitter 帖子](https://twitter.com/azuread/status/1123964502909779968)
* 营销网站。 创建或更新营销页面（例如集成页面、合作伙伴页面、定价页面等），以包括联合集成的供应情况。 [示例：Pingboard 集成页面](https://pingboard.com/org-chart-for)、[Smartsheet 集成页面](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)和 [Monday.com 定价页面](https://monday.com/pricing/) 
* 技术文档。 创建有关客户如何入门的帮助中心文章或技术文档。 [示例：Envoy + Microsoft Azure Active Directory 集成。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* 客户通信。 通过客户通信（每月简讯、电子邮件活动、产品发行说明）提醒客户有新的集成。 

## <a name="related-articles"></a>相关文章

* [在 SaaS 应用中自动预配和取消预配用户](user-provisioning.md)
* [为用户预配自定义属性映射](customize-application-attributes.md)
* [为属性映射编写表达式](functions-for-customizing-application-data.md)
* [用于用户预配的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帐户预配通知](user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
