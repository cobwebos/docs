---
title: 开发 SCIM 终结点，以便将用户预配到 Azure AD 的应用
description: 跨域标识管理（SCIM）的系统标准化自动用户预配。 了解如何开发 SCIM 终结点，如何将 SCIM API 与 Azure Active Directory 集成，并开始自动将用户和组预配到你的云应用程序中。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 65bbb35d041a48e68d01a50e88e42fbeb73f2ea6
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864277"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>生成 SCIM 终结点并使用 Azure AD 配置用户预配

作为应用程序开发人员，你可以使用系统来实现跨域标识管理（SCIM）用户管理 API，以便在应用程序和 Azure AD 之间自动预配用户和组。 本文介绍如何生成 SCIM 终结点并与 Azure AD 预配服务集成。 SCIM 规范提供了用于预配的常见用户架构。 与 SAML 或 OpenID Connect 等联合标准结合使用时，SCIM 为管理员提供了一种基于标准的端到端解决方案，用于访问管理。

SCIM 是两个终结点的标准化定义：/Users 终结点和/Groups 终结点。 它使用常见的 REST 谓词来创建、更新和删除对象，并为常见属性（如组名称、用户名、名字、姓氏和电子邮件）预定义架构。 提供 SCIM 2.0 REST API 的应用可减少或消除使用专有用户管理 API 的难点。 例如，任何符合 SCIM 的客户端都知道如何将 JSON 对象的 HTTP POST 发送到/Users 终结点，以创建新的用户条目。 不需要对相同的基本操作使用略微不同的 API，符合 SCIM 标准的应用可以立即利用预先存在的客户端、工具和代码。 

![使用 SCIM 从 Azure AD 预配到应用](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0 （RFC [7642](https://tools.ietf.org/html/rfc7642)、 [7643](https://tools.ietf.org/html/rfc7643)、 [7644](https://tools.ietf.org/html/rfc7644)）中定义的用于管理的标准用户对象架构和 rest api 允许标识提供者和应用更轻松地彼此集成。 生成 SCIM 终结点的应用程序开发人员无需进行自定义工作即可与任何 SCIM 兼容的客户端集成。

自动预配到应用程序需要生成 SCIM 终结点，并将其与 Azure AD SCIM 客户端集成。 执行以下步骤，开始将用户和组预配到应用程序。 
    
  * **[步骤1：设计用户和组架构。](#step-1-design-your-user-and-group-schema)** 确定应用程序所需的对象和属性，并确定它们如何映射到 Azure AD SCIM 实现所支持的用户和组架构。

  * **[步骤2：了解 Azure AD SCIM 实现。](#step-2-understand-the-azure-ad-scim-implementation)** 了解如何实现 Azure AD SCIM 客户端，并为 SCIM 协议请求处理和响应建模。

  * **[步骤3：生成 SCIM 终结点。](#step-3-build-a-scim-endpoint)** 终结点必须是 SCIM 2.0 兼容的，才能与 Azure AD 预配服务集成。 作为选项，你可以使用 Microsoft 公共语言基础结构（CLI）库和代码示例来生成终结点。 这些示例仅用于引用和测试;建议不对生产应用进行编码，以依赖于它们。

  * **[步骤4：将 SCIM 终结点与 Azure AD SCIM 客户端集成。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 如果你的组织使用的第三方应用程序实现 Azure AD 支持的 SCIM 2.0 的配置文件，则可以立即开始自动预配和取消预配用户和组。

  * **[步骤5：将应用程序发布到 Azure AD 应用程序库。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 使客户能够轻松发现应用程序并轻松配置预配。 

![将 SCIM 终结点与 Azure AD 集成的步骤](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>步骤1：设计用户和组架构

每个应用程序都需要使用不同的属性来创建用户或组。 通过标识应用程序所需的对象（用户、组）和属性（名称、管理器、作业标题等）开始集成。 SCIM 标准定义了用于管理用户和组的架构。 核心用户架构只需要三个属性： **id** （服务提供商定义的标识符）、 **externalId** （客户端定义的标识符）和**元**数据（由服务提供商维护的只读元数据）。 所有其他属性都是可选的。 除核心用户架构外，SCIM 标准还定义了一个企业用户扩展和一个模型，用于扩展用户架构以满足应用程序的需求。 例如，如果你的应用程序需要用户的管理者，则可以使用企业用户架构收集用户的管理器和核心架构以收集用户的电子邮件。 若要设计架构，请执行以下步骤：
  1. 列出应用程序所需的属性。 将要求分解到身份验证所需的属性（例如，loginName 和电子邮件）、管理用户的生命周期所需的属性（例如状态/活动）以及特定应用程序的工作所需的其他属性（例如，管理器、标记），这可能会很有帮助。
  2. 检查是否已在核心用户架构或企业用户架构中定义了这些属性。 如果核心或企业用户架构中不包含所需的任何属性，则需要定义包含所需属性的用户架构扩展。 在下面的示例中，我们已向用户添加了一个扩展，以允许在用户上设置 "标记"。 最好只从核心和企业用户架构开始，以后再展开到其他自定义架构。  
  3. 将 SCIM 属性映射到 Azure AD 中的用户属性。 如果在 SCIM 终结点中定义的属性之一在 Azure AD 用户架构上没有明确的对应项，则很有可能不会在大多数租户上将数据存储在用户对象上。 考虑此特性是否可用于创建用户。 如果该属性对您的应用程序运行至关重要，请指导租户管理员扩展其架构或使用如下所示的扩展属性。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表1：概述所需的属性 
| 步骤1：确定应用需要的属性| 步骤2：将应用需求映射到 SCIM standard| 步骤3：将 SCIM 特性映射到 Azure AD 特性|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|名称 lastName|lastName|
|workMail|电子邮件 [类型 eq "work"]。值|Mail|
|manager|manager|manager|
|标记|urn： ietf： params： scim：架构：扩展：2.0： CustomExtension：标记|extensionAttribute1|
|状态|活动|isSoftDeleted （计算值未存储在用户上）|

上面定义的架构将使用下面的 Json 有效负载来表示。 请注意，除应用程序所需的属性外，JSON 表示形式还包含所需的 "id"、"externalId" 和 "meta" 特性。

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

### <a name="table-2-default-user-attribute-mapping"></a>表2：默认用户属性映射
然后，你可以使用下表来了解应用程序所需的属性如何映射到 Azure AD 中的属性和 SCIM RFC。 您可以[自定义](customize-application-attributes.md)属性在 AZURE AD 和 SCIM 终结点之间的映射方式。 请注意，不需要同时支持用户和组或下面显示的所有属性。 它们是有关 Azure AD 中的特性如何经常映射到 SCIM 协议中的属性的参考。 

| Azure Active Directory 用户 | “urn:ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |活动 |
|department|urn： ietf： params： scim：架构：扩展： enterprise：2.0： User：部门|
| displayName |displayName |
|employeeId|urn： ietf： params： scim：架构：扩展： enterprise：2.0： User： employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn： ietf： params： scim：架构：扩展： enterprise：2.0： User： manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>表3：默认组属性映射

| Azure Active Directory 组 | urn： ietf： params： scim：架构： core：2.0：组 |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC 中定义了多个终结点。 可以开始启用/User 终结点，并从该处进行扩展。 使用自定义属性或架构频繁更改时，/Schemas 终结点非常有用。 它使客户端能够自动检索最新的架构。 支持组时，/Bulk 终结点特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 使用自定义属性或架构频繁更改时，/Schemas 终结点非常有用。 它使客户端能够自动检索最新的架构。 支持组时，/Bulk 终结点特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表4：确定要开发的终结点
|ENDPOINT|DESCRIPTION|
|--|--|
|/User|对用户对象执行 CRUD 操作。|
|/Group|对组对象执行 CRUD 操作。|
|/ServiceProviderConfig|提供有关支持的 SCIM 标准功能的详细信息，例如，支持的资源和身份验证方法。|
|/ResourceTypes|指定有关每个资源的元数据|
|/Schemas|每个客户端和服务提供商支持的属性集可能有所不同。 尽管一个服务提供商可能包括 "名称"、"标题" 和 "电子邮件"，但另一个服务提供商使用 "名称"、"标题" 和 "phoneNumbers"。 架构端点允许发现支持的属性。|
|/Bulk|使用大容量操作，可以在单个操作中对大型资源对象集合执行操作（例如，更新大型组的成员身份）。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>步骤2：了解 Azure AD SCIM 实现
> [!IMPORTANT]
> Azure AD SCIM 实现的行为最近于 2018 年 12 月 18 日更新。 有关更改内容的信息，请参阅 [Azure AD 用户预配服务 SCIM 2.0 协议合规性](application-provisioning-config-problem-scim-compatibility.md)。

如果构建的应用程序支持 SCIM 2.0 用户管理 API，本部分将详细介绍如何实现 Azure AD SCIM 客户端。 它还演示如何为 SCIM 协议请求处理和响应建模。 实现 SCIM 终结点后，可以按照上一部分中所述的过程对其进行测试。

在[SCIM 2.0 协议规范](http://www.simplecloud.info/#Specification)中，应用程序必须满足以下要求：

* 支持根据[SCIM 协议的第 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)节，创建用户和组（可选）。  
* 支持根据[SCIM 协议的每节 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)修改修补请求的用户或组。  
* 支持检索之前创建的用户或组的已知资源，如[SCIM 协议的3.4.1 节所](https://tools.ietf.org/html/rfc7644#section-3.4.1)述。  
* 支持查询用户或组，如[SCIM 协议的3.4.2 节所](https://tools.ietf.org/html/rfc7644#section-3.4.2)述。  默认情况下， `id`用户通过其`username`和`externalid`查询来检索用户，并且将查询组。 `displayName`  
* 支持按 ID 和管理器查询用户，如 SCIM 协议的3.4.2 节所述。  
* 支持按 ID 和成员查询组，这与 SCIM 协议的每节3.4.2 一样。  
* 接受单个持有者令牌，用于对应用程序进行 Azure AD 身份验证和授权。

实现 SCIM 终结点时，请遵循以下常规准则，以确保与 Azure AD 的兼容性：

* `id`是所有资源的必需属性。 每个返回资源的响应都应确保每个资源都具有此属性`ListResponse` ，但不包含任何成员。
* 查询/筛选器请求的响应应始终为`ListResponse`。
* 组是可选的，但仅当 SCIM 实现支持修补程序请求时才受支持。
* 不需要在修补程序响应中包含整个资源。
* Microsoft Azure AD 只使用以下运算符：  
    - `eq`
    - `and`
* 对于 SCIM 中的结构元素，无需区分大小写，具体取决于`op`中定义的修补程序操作https://tools.ietf.org/html/rfc7644#section-3.5.2值。 Azure AD 将 "op" 的值作为`Add`、 `Replace`和`Remove`发出。
* Microsoft Azure AD 请求获取随机用户和组，以确保终结点和凭据有效。 它也作为[Azure 门户](https://portal.azure.com)中的**测试连接**流的一部分完成。 
* 可在其上查询资源的属性应设置为[Azure 门户](https://portal.azure.com)的应用程序上的匹配属性。 有关详细信息，请参阅[自定义用户预配属性映射](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>用户预配和取消预配

下图显示了 Azure Active Directory 发送到 SCIM 服务的消息，以管理应用程序的标识存储中用户的生命周期。  

![显示用户预配和取消预配顺序](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*用户预配和取消预配顺序*

### <a name="group-provisioning-and-deprovisioning"></a>组预配和取消预配

组预配和取消设置是可选的。 实现并启用后，下图显示 Azure AD 发送到 SCIM 服务的消息，以管理应用程序的标识存储中某个组的生命周期。  这些消息在以下两个方面与用户消息不同：

* 检索组的请求指定要从为响应请求而提供的任何资源中排除成员属性。  
* 确定引用属性是否具有特定值的请求是有关成员属性的请求。  

![显示组预配和取消预配顺序](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*组预配和取消预配顺序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 协议请求和响应
本部分提供 Azure AD SCIM 客户端发出的示例 SCIM 请求以及预期的响应。 为了获得最佳结果，应将应用编码为按此格式处理这些请求，并发出预期的响应。

> [!IMPORTANT]
> 若要了解 Azure AD 用户预配服务如何以及何时发出下面所述的操作，请参阅[预配周期：预配周期的初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [。](how-provisioning-works.md)

[用户操作](#user-operations)
  - [创建用户](#create-user)（[请求](#request) / [响应](#response)）
  - [获取用户](#get-user)（[请求](#request-1) / [响应](#response-1)）
  - [按查询获取用户](#get-user-by-query)（[请求](#request-2) / [响应](#response-2)）
  - [按查询获取用户-零个结果](#get-user-by-query---zero-results)（[请求](#request-3)
/ [响应](#response-3)）
  - [更新用户 [多值属性]](#update-user-multi-valued-properties) （[请求](#request-4) /  [响应](#response-4)）
  - [更新用户 [单值属性]](#update-user-single-valued-properties) （[请求](#request-5)
/ [响应](#response-5)） 
  - [禁用用户](#disable-user)（[请求](#request-14) / 
[响应](#response-14)）
  - [删除用户](#delete-user)（[请求](#request-6) / 
[响应](#response-6)）


[组操作](#group-operations)
  - [创建组](#create-group)（[请求](#request-7) / [响应](#response-7)）
  - [获取组](#get-group)（[请求](#request-8) / [响应](#response-8)）
  - [按 displayName 获取组](#get-group-by-displayname)（[请求](#request-9) / [响应](#response-9)）
  - [更新组 [非成员属性]](#update-group-non-member-attributes) （[请求](#request-10) /
 [响应](#response-10)）
  - [更新组 [添加成员]](#update-group-add-members) （[请求](#request-11) /
[响应](#response-11)）
  - [更新组 [删除成员]](#update-group-remove-members) （[请求](#request-12) /
[响应](#response-12)）
  - [删除组](#delete-group)（[请求](#request-13) /
[响应](#response-13)）

### <a name="user-operations"></a>用户操作

* 用户可以通过`userName`或`email[type eq "work"]`属性进行查询。  

#### <a name="create-user"></a>创建用户

###### <a name="request"></a>请求

*POST/Users*
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

*已创建 HTTP/1.1 201*
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
*获取/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>响应（已找到用户）
*HTTP/1。1 200 “确定”*
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
*获取/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>响应（找不到用户。 请注意，详细信息不是必需的，只是状态。）

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>按查询获取用户

##### <a name="request"></a><a name="request-2"></a>请求

*GET/Users？ filter = userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>响应

*HTTP/1。1 200 “确定”*
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

#### <a name="get-user-by-query---zero-results"></a>按查询获取用户-零个结果

##### <a name="request"></a><a name="request-3"></a>请求

*获取/Users？ filter = 用户名 eq "不存在的用户"*

##### <a name="response"></a><a name="response-3"></a>响应

*HTTP/1。1 200 “确定”*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>更新用户 [多值属性]

##### <a name="request"></a><a name="request-4"></a>请求

*PATCH/Users/6764549bef60420686bc HTTP/1。1*
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

*HTTP/1。1 200 “确定”*
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

#### <a name="update-user-single-valued-properties"></a>更新用户 [单值属性]

##### <a name="request"></a><a name="request-5"></a>请求

*PATCH/Users/5171a35d82074e068ce2 HTTP/1。1*
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

*HTTP/1。1 200 “确定”*
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

*PATCH/Users/5171a35d82074e068ce2 HTTP/1。1*
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

*删除/Users/5171a35d82074e068ce2 HTTP/1。1*

##### <a name="response"></a><a name="response-6"></a>响应

*HTTP/1.1 204 无内容*

### <a name="group-operations"></a>组操作

* 组应始终使用空成员列表创建。
* 可以通过`displayName`属性来查询组。
* 更新到组修补请求应在响应中生成*HTTP 204 无内容*。 不建议使用所有成员列表返回正文。
* 不需要支持返回组的所有成员。

#### <a name="create-group"></a>创建组

##### <a name="request"></a><a name="request-7"></a>请求

*POST/Groups HTTP/1。1*
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

*已创建 HTTP/1.1 201*
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

*GET/Groups/40734ae655284ad3abcc？ excludedAttributes = members HTTP/1。1*

##### <a name="response"></a><a name="response-8"></a>响应
*HTTP/1。1 200 “确定”*
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

#### <a name="get-group-by-displayname"></a>通过 displayName 获取组

##### <a name="request"></a><a name="request-9"></a>请求
*GET/Groups？ excludedAttributes = members&filter = displayName eq "displayName" HTTP/1。1*

##### <a name="response"></a><a name="response-9"></a>响应

*HTTP/1。1 200 “确定”*
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

#### <a name="update-group-non-member-attributes"></a>更新组 [非成员属性]

##### <a name="request"></a><a name="request-10"></a>请求

*PATCH/Groups/fa2ce26709934589afc5 HTTP/1。1*
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

*HTTP/1.1 204 无内容*

### <a name="update-group-add-members"></a>更新组 [添加成员]

##### <a name="request"></a><a name="request-11"></a>请求

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1。1*
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

*HTTP/1.1 204 无内容*

#### <a name="update-group-remove-members"></a>更新组 [删除成员]

##### <a name="request"></a><a name="request-12"></a>请求

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1。1*
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

*HTTP/1.1 204 无内容*

#### <a name="delete-group"></a>删除组

##### <a name="request"></a><a name="request-13"></a>请求

*删除/Groups/cdb1ce18f65944079d37 HTTP/1。1*

##### <a name="response"></a><a name="response-13"></a>响应

*HTTP/1.1 204 无内容*

### <a name="security-requirements"></a>安全要求
**TLS 协议版本**

唯一可接受的 TLS 协议版本为 TLS 1.2 和 TLS 1.3。 不允许使用其他版本的 TLS。 不允许使用任何 SSL 版本。 
- RSA 密钥必须至少为2048位。
- ECC 密钥必须至少为256位，使用批准的椭圆曲线生成


**密钥长度**

所有服务都必须使用使用足够长度的加密密钥生成的 x.509 证书，这意味着：

**密码套件**

必须按下面指定的准确顺序将所有服务配置为使用以下密码套件。 请注意，如果只有一个 RSA 证书，则安装的 ECDSA 密码套件不会有任何影响。 </br>

TLS 1.2 密码套件最小栏：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>步骤3：生成 SCIM 终结点

现在，你已设计了架构并了解 Azure AD SCIM 实现，接下来可以开始开发 SCIM 终结点。 你可以依赖于由 SCIM 社区发布的多个开源 SCIM 库，而不是从头开始创建并完全生成实现。

Azure AD 预配团队发布的开源 .NET Core[参考代码](https://aka.ms/SCIMReferenceCode)是可以立即开始开发的一种资源。 生成 SCIM 终结点后，需要对其进行测试。你可以使用作为引用代码一部分提供的[postman 测试](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)的集合，或通过[上面](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)提供的示例请求/响应来运行。  

   > [!Note]
   > 参考代码旨在帮助你开始生成 SCIM 终结点，并按 "原样" 提供。 来自社区的贡献欢迎使用来帮助生成和维护代码。

此解决方案由两个项目组成： _SCIM_和_WebHostSample_。

_SCIM_项目是定义符合 SCIM 规范的 web 服务组件的库。 它声明了接口_SCIM_，请求将转换为对提供程序的方法的调用，这些方法将被设计为对标识存储进行操作。

![细目：请求转换为对提供程序方法的调用](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_SCIM WebHostSample_项目是基于_空_模板 ASP.NET Core Web 应用程序的 Visual Studio。 这允许将示例代码部署为独立的、托管在容器中或 Internet Information Services 中。 它还实现_SCIM。每 microsoft.systemforcrossdomainidentitymanagement.iprovider.startupbehavior_接口将类存储在内存中作为示例标识存储。

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

### <a name="building-a-custom-scim-endpoint"></a>构建自定义 SCIM 终结点

SCIM 服务必须有一个 HTTP 地址和服务器身份验证证书，该证书的根证书颁发机构是下列名称之一：

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK 包括一个可在开发过程中使用的 HTTPS 开发证书，该证书将作为首次运行体验的一部分进行安装。 根据您运行 ASP.NET Core Web 应用程序的方式，该应用程序将侦听其他端口：

* SCIM. WebHostSample：https://localhost:5001
* IIS Express：https://localhost:44359/

有关 ASP.NET Core 中的 HTTPS 的详细信息，请使用以下链接：[在 ASP.NET Core 中强制使用 https](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>处理终结点身份验证

来自 Azure Active Directory 的请求包括 OAuth 2.0 持有者令牌。 接收请求的任何服务应对颁发者进行身份验证，使其与预期 Azure Active Directory 租户 Azure Active Directory。

在令牌中，颁发者由 iss 声明标识，如`"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`。 在此示例中，声明值`https://sts.windows.net`的基址标识 Azure Active Directory 作为颁发者，而相对地址段_cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_是颁发令牌的 Azure Active Directory 租户的唯一标识符。

令牌的受众将是库中应用程序的应用程序模板 ID，在单个租户中注册的每个应用程序可能会收到与 SCIM 请求`iss`相同的声明。 所有自定义应用的应用程序模板 ID 都是_8adf8e6e-67b2-4cf2-a259-e3dc5476c621_。 Azure AD 预配服务生成的令牌只能用于测试。 不应在生产环境中使用它。

在示例代码中，使用 AspNetCore. JwtBearer 包对请求进行身份验证。 下面的代码强制使用为指定租户 Azure Active Directory 颁发的持有者令牌对服务的任何终结点请求进行身份验证：

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

还需要持有持有者令牌来使用提供的[postman 测试](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)，并使用 localhost 执行本地调试。 示例代码使用 ASP.NET Core 环境来更改开发阶段中的身份验证选项，并允许使用自签名令牌。

有关 ASP.NET Core 中的多个环境的详细信息，请使用以下链接：[在 ASP.NET Core 中使用多种环境](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

下面的代码强制通过使用自定义密钥进行签名的持有者令牌对服务的任何终结点请求进行身份验证：

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

将 GET 请求发送到令牌控制器若要获取有效持有者令牌，方法_GenerateJSONWebToken_负责创建一个与为开发配置的参数相匹配的令牌：

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>处理用户的预配和取消预配

***示例1。查询匹配用户的服务***

Azure Active Directory 会在服务中查询是否有某个用户的 externalId 属性值与 Azure AD 中用户的 mailNickname 属性值匹配。 查询表示为超文本传输协议（HTTP）请求（如本示例），其中 jyoung 是 Azure Active Directory 中用户的 mailNickname 的示例。

>[!NOTE]
> 这只是一个示例。 并非所有用户都具有 mailNickname 属性，并且用户在目录中的值可能不唯一。 此外，用于匹配（在本例中为 externalId）的属性可在[Azure AD 属性映射](customize-application-attributes.md)中进行配置。

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

在示例代码中，请求转换为对服务提供程序的 QueryAsync 方法的调用。 以下是该方法的签名： 

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

在示例查询中，对于具有给定 externalId 属性值的用户，传递给 QueryAsync 方法的参数的值是：

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***示例2。预配用户***

如果对 externalId 属性值与用户的 mailNickname 属性值相匹配的用户对 web 服务的查询的响应未返回任何用户，则 Azure Active Directory 请求服务设置与 Azure Active Directory 中的用户相对应的用户。  以下是此类请求的示例： 

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

在示例代码中，请求转换为对服务提供程序的 CreateAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

在用于预配用户的请求中，资源参数的值是在 SCIM 库中定义的 SCIM 类的实例。  如果预配用户的请求成功，则方法的实现应返回 SCIM 类的实例，并将 "标识符" 属性的值设置为新预配用户的唯一标识符。  

***示例3。查询用户的当前状态*** 

为了更新存在于前端为 SCIM 的标识存储中的已知用户，Azure Active Directory 将通过类似于下面的请求向服务请求该用户的当前状态来继续处理： 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

在示例代码中，请求转换为对服务提供程序的 RetrieveAsync 方法的调用。 以下是该方法的签名： 

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
  
* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***示例4。查询要更新的引用属性的值*** 

如果要更新引用属性，Azure Active Directory 会查询服务，以确定服务前端的标识存储中的引用属性的当前值是否已经与 Azure Active Directory 中该属性的值相匹配。 对于用户，以这种方式查询当前值的唯一属性是 manager 属性。 下面是用于确定用户对象的 manager 属性当前是否具有特定值的请求示例：在示例代码中，请求转换为对服务提供程序的 QueryAsync 方法的调用。 作为参数自变量值提供的对像属性值如下： 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

此处，索引 x 的值可以是0，并且索引 y 的值可以是1，或者 x 的值可以是1，而 y 的值可以是0，具体取决于筛选器查询参数表达式的顺序。   

***示例5。Azure AD 请求到 SCIM 服务以更新用户*** 

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

在示例代码中，请求转换为对服务提供程序的 UpdateAsync 方法的调用。 以下是该方法的签名： 

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
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***示例6。取消预配用户***

若要从前端 SCIM 服务的标识存储取消设置用户，Azure AD 发送如下请求：

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

在示例代码中，请求转换为对服务提供程序的 DeleteAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

作为 resourceIdentifier 参数值提供的对象在用于取消预配用户的请求示例中具有这些属性值： 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>步骤4：将 SCIM 终结点与 Azure AD SCIM 客户端集成

可以将 Azure AD 配置为自动将已分配的用户和组预配到实现了[SCIM 2.0 协议](https://tools.ietf.org/html/rfc7644)的特定配置文件的应用程序。 [步骤2：了解 AZURE AD SCIM 实现](#step-2-understand-the-azure-ad-scim-implementation)中介绍了该配置文件的详细信息。

请咨询应用程序提供者，或参阅应用程序提供者文档中的说明，以了解是否符合这些要求。

> [!IMPORTANT]
> Azure AD SCIM 实现基于 Azure AD 用户预配服务，该服务旨在使用户不断地在 Azure AD 和目标应用程序之间保持同步，并实现一组非常具体的标准操作。 了解这些行为以了解 Azure AD SCIM 客户端的行为非常重要。 有关详细信息，请参阅[预配周期部分：预配周期的初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。 [How provisioning works](how-provisioning-works.md)

### <a name="getting-started"></a>入门

支持本文所述 SCIM 配置文件的应用程序可以使用 Azure AD 应用程序库中的“非库应用程序”功能连接到 Azure Active Directory。 连接后，Azure AD 将每隔 40 分钟运行同步过程，此过程将为分配的用户和组查询应用程序的 SCIM 终结点，并根据分配详细信息创建或修改这些用户和组。

**连接到支持 SCIM 的应用程序：**

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)。 请注意，你可以通过注册[开发人员计划](https://developer.microsoft.com/office/dev-program)来访问使用 P2 许可证的 Azure Active Directory 免费试用版
2. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
3. 选择 " **+ 新建应用程序** > **所有** > **非库应用程序**"。
4. 输入应用程序的名称，然后选择 "**添加**" 以创建应用对象。 新应用将添加到企业应用程序列表中，并打开到其应用管理屏幕。

   ![屏幕截图显示 Azure AD 应用程序库](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 应用程序库*

5. 在应用管理屏幕的左侧面板中，选择 "**预配**"。
6. 在“预配模式”菜单中，选择“自动”********。

   ![示例：应用程序在 Azure 门户中的设置页面](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *在 Azure 门户中配置预配*

7. 在“租户 URL”字段中，输入应用程序的 SCIM 终结点的 URL****。 示例：`https://api.contoso.com/scim/`
8. 如果 SCIM 终结点需要来自非 Azure AD 颁发者的 OAuth 持有者令牌，可将所需的 OAuth 持有者令牌复制到可选的“密钥令牌”字段****。 如果此字段保留为空，则 Azure AD 包括每个请求 Azure AD 颁发的 OAuth 持有者令牌。 将 Azure AD 用作标识提供者的应用可以验证 Azure AD 颁发的此令牌。 
   > [!NOTE]
   > 建议***不要***将此字段留空，并依赖于 Azure AD 生成的令牌。 此选项主要用于测试目的。
9. 选择 "**测试连接**"，Azure Active Directory 尝试连接到 SCIM 终结点。 如果尝试失败，将显示错误消息。  

    > [!NOTE]
    > **测试连接**使用随机 GUID 作为在 Azure AD 配置中选择的匹配属性，针对不存在的用户查询 SCIM 终结点。 预期正确响应为“HTTP 200 正常”以及空的 SCIM ListResponse 消息。

10. 如果尝试连接到应用程序成功，请选择 "**保存**" 以保存管理员凭据。
11. 在 "**映射**" 部分中，有两个可选择的[属性映射](customize-application-attributes.md)集：一个用于用户对象，一个用于组对象。 分别选择它们，查看从 Azure Active Directory 同步到应用的属性。 选为“匹配”属性的特性用于匹配应用中的用户和组，以执行更新操作****。 选择“保存”，提交所有更改****。

    > [!NOTE]
    > 也可通过禁用“组”映射来选择性禁用组对象的同步。

12. “设置”下的“作用域”字段定义同步的用户和组********。 选择 "**仅同步分配的用户和组**（推荐）"，以便仅同步 "**用户和组**" 选项卡中分配的用户和组。
13. 配置完成后，将设置**状态**设置为 **"开**"。
14. 选择 "**保存**" 以启动 Azure AD 预配服务。
15. 如果仅同步分配的用户和组（推荐），请确保选择 "**用户和组**" 选项卡并分配要同步的用户或组。

初始周期开始后，可以在左侧面板中选择 "**设置日志**" 来监视进度，其中显示了预配服务对应用执行的所有操作。 若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始周期的执行时间比后续同步长，只要服务正在运行，大约每隔40分钟就会进行一次同步。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>步骤5：将应用程序发布到 Azure AD 应用程序库

如果要构建的应用程序将由多个租户使用，则可以将其提供给 Azure AD 应用程序库。 这样，组织就可以轻松发现应用程序并配置设置。 在 Azure AD 库中发布你的应用程序并使其可供其他人使用非常简单。 在[此处](../develop/howto-app-gallery-listing.md)查看步骤。 Microsoft 将与你合作，将你的应用程序集成到我们的库、测试终结点，并发布载入[文档](../saas-apps/tutorial-list.md)供客户使用。 

### <a name="gallery-onboarding-checklist"></a>库载入清单
按照下面的清单，确保你的应用程序是载入可的，并且客户具有平稳的部署体验。 载入库时，将收集此信息。 
> [!div class="checklist"]
> * 支持[SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation)用户和组终结点（仅需要一个，但建议同时使用这两个）
> * 支持每个租户每秒至少25个请求（必需）
> * 建立工程和支持联系人，指导客户发布库载入（必需）
> * 3应用程序的不过期测试凭据（必需）
> * 支持 OAuth 授权代码授予或长生存期令牌（必需）
> * 建立工程和支持点联系以支持客户发布库载入（必需）
> * 支持使用单个修补程序更新多个组成员身份（建议） 
> * 公开记录你的 SCIM 终结点（建议） 
> * [支持架构发现](https://tools.ietf.org/html/rfc7643#section-6)（建议）


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>在应用程序库中预配连接器的授权
SCIM 规范未定义用于身份验证和授权的特定于 SCIM 的方案。 它依赖于现有的行业标准。 Azure AD 预配客户端支持库中应用程序的两种授权方法。 

|授权方法|优点|缺点|支持|
|--|--|--|--|
|用户名和密码（Azure AD 不推荐或支持）|易于实现|不安全- [Pa $ $word 不重要](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|库应用按案例支持。 不支持非库应用。|
|长生存期持有者令牌|生存期较长的令牌不要求用户提供。 在设置预配时，管理员可以轻松地使用它们。|在不使用安全方法（如电子邮件）的情况下，长时间生存期令牌可能很难与管理员共享。 |库和非库应用支持。 |
|OAuth 授权代码授予|访问令牌的生存期比密码短得多，并且具有长生存期持有者令牌不具有的自动刷新机制。  在初始授权期间，实际用户必须提供，从而增加了责任级别。 |要求用户提供。 如果用户离开了组织，则令牌将无效，并且将需要再次完成授权。|库应用支持。 对非库应用的支持正在进行。|
|OAuth 客户端凭据授予|访问令牌的生存期比密码短得多，并且具有长生存期持有者令牌不具有的自动刷新机制。 授权代码授权和客户端凭据 grant 都创建相同类型的访问令牌，因此在这些方法之间移动对于 API 是透明的。  预配可完全自动进行，并且无需用户交互即可无提示方式请求新令牌。 ||库和非库应用不支持。 积压工作（backlog）中的支持。|

[!NOTE] 不建议在 Azure AD 预配配置自定义应用 UI 中将令牌字段留空。 生成的令牌主要可用于测试目的。

**OAuth 授权代码授予流：** 预配服务支持[授权代码授予](https://tools.ietf.org/html/rfc6749#page-24)。 提交你的应用程序在库中发布的请求后，我们的团队将与你一起收集以下信息：
*  授权 URL：客户端的 URL，通过用户代理重定向从资源所有者获取授权。 用户被重定向到此 URL 以授权访问。 请注意，此 URL 当前不可按租户配置。
*  令牌交换 URL：客户端用于交换访问令牌授权的 URL，通常使用客户端身份验证。 请注意，此 URL 当前不可按租户配置。
*  客户端 ID：授权服务器向注册的客户端颁发客户端标识符，该标识符是表示客户端提供的注册信息的唯一字符串。  客户端标识符不是机密;它向资源所有者公开，**不得**单独用于客户端身份验证。  
*  客户端密码：客户端密码是由授权服务器生成的机密。 它应该是唯一的唯一值，只是授权服务器。 

请注意，由于公开了客户端机密，因此不支持 OAuth v1。 支持 OAuth v2。  

最佳做法（建议但不需要）：
* 支持多个重定向 Url。 管理员可以配置 "portal.azure.com" 和 "aad.portal.azure.com" 中的预配。 支持多个重定向 Url 将确保用户可以从任一门户授予访问权限。
* 支持多个机密，以确保顺利地续订密钥，而无需停机。 

**生存期较长的 OAuth 持有者令牌：** 如果你的应用程序不支持 OAuth 授权代码授予流，你还可以生成一个长期的 OAuth 持有者令牌，而不是管理员可用于设置预配集成。 令牌应为永久标记，否则在令牌过期时将[隔离](application-provisioning-quarantine-status.md)设置作业。 此令牌的大小必须小于 1 KB。  

有关其他身份验证和授权方法，请在[UserVoice](https://aka.ms/appprovisioningfeaturerequest)上告诉我们。

### <a name="gallery-go-to-market-launch-check-list"></a>库走向市场启动检查列表
为了帮助推动对联合集成的认知和需求，我们建议您更新现有文档，并放大您的营销渠道中的集成。  下面是我们建议你完成以支持启动的一组清单活动

* **销售和客户支持就绪情况。** 确保你的销售和支持团队了解，并可以与集成功能进行交流。 简要介绍销售和支持团队，向他们提供 Faq 并将集成添加到销售材料中。 
* **博客文章和/或按版本。** 创建一个博客文章或按版本，介绍联合集成、优点和入门方式。 [示例： Imprivata 和 Azure Active Directory 按下发布](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **社交媒体。** 利用你的社交媒体（如 Twitter、Facebook 或 LinkedIn）来促进与客户的集成。 请确保包含@AzureAD ，以便我们可以转推你的帖子。 [示例： Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **营销网站。** 创建或更新市场营销页面（例如集成页、合作伙伴页、定价页等）以包括联合集成的可用性。 [示例： Pingboard 集成页](https://pingboard.com/org-chart-for)， [Smartsheet 集成页](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)， [Monday.com 定价页](https://monday.com/pricing/) 
* **技术文档。** 创建帮助中心文章或有关客户如何开始使用的技术文档。 [示例： Envoy + Microsoft Azure Active Directory 集成。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **客户通信。** 通过您的客户通信（每月新闻稿、电子邮件活动、产品发行说明）通知客户新的集成。 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>允许 Azure AD 预配服务使用的 IP 地址发出 SCIM 请求

某些应用允许入站流量发送到应用。 为了使 Azure AD 预配服务能够按预期运行，必须允许使用的 IP 地址。 有关每个服务标记/区域的 IP 地址列表，请参阅 JSON 文件 - [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 你可以根据需要将这些 Ip 下载并编程到防火墙中。 可以在 "AzureActiveDirectoryDomainServices" 下找到 Azure AD 预配的保留 IP 范围。

## <a name="related-articles"></a>相关文章

* [自动执行用户预配和取消预配到 SaaS 应用](user-provisioning.md)
* [为用户预配自定义属性映射](customize-application-attributes.md)
* [为属性映射编写表达式](functions-for-customizing-application-data.md)
* [用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帐户预配通知](user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
