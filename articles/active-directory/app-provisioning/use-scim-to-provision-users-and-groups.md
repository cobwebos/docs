---
title: 开发 SCIM 终结点，用于从 Azure AD 预配到应用
description: 跨域身份管理系统 （SCIM） 可标准化自动用户预配。 了解如何开发 SCIM 终结点、将 SCIM API 与 Azure 活动目录集成，并开始将用户和组自动预配到云应用程序中。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297679"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>使用 Azure 活动目录（Azure AD）构建 SCIM 终结点并配置用户预配

作为应用程序开发人员，可以使用跨域标识管理系统 （SCIM） 用户管理 API 在应用程序和 Azure AD 之间自动预配用户和组。 本文介绍如何构建 SCIM 终结点并与 Azure AD 预配服务集成。 SCIM 规范提供了用于预配的通用用户架构。 当与 SAML 或 OpenID 连接等联合标准结合使用时，SCIM 为管理员提供了基于标准的端到端访问管理解决方案。

SCIM 是两个终结点的标准化定义：/Users 终结点和 /组终结点。 它使用常见的 REST 谓词创建、更新和删除对象，以及为组名称、用户名、名字、姓氏和电子邮件等常见属性创建、更新和删除的预先定义的架构。 提供 SCIM 2.0 REST API 的应用可以减少或消除使用专有用户管理 API 的痛苦。 例如，任何符合 SCIM 客户端都知道如何将 JSON 对象的 HTTP POST 制作到 /Users 终结点以创建新的用户条目。 符合 SCIM 标准的应用无需针对相同的基本操作需要稍微不同的 API，而是可以立即利用预先存在的客户端、工具和代码。 

![从 Azure AD 预配到具有 SCIM 的应用](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0（RFC [7642、7643、7644）](https://tools.ietf.org/html/rfc7642)中[7643](https://tools.ietf.org/html/rfc7643)定义的用于管理的标准用户[7644](https://tools.ietf.org/html/rfc7644)对象架构和休息 API 允许标识提供程序和应用程序更轻松地相互集成。 构建 SCIM 终结点的应用程序开发人员可以与任何符合 SCIM 的客户端集成，而无需执行自定义工作。

自动预配到应用程序需要构建 SCIM 终结点并将其与 Azure AD SCIM 客户端集成。 执行以下步骤，开始将用户和组预配到应用程序中。 
    
  * **[第 1 步：设计用户和组架构。](#step-1-design-your-user-and-group-schema)** 确定应用程序需要的对象和属性，并确定它们如何映射到 Azure AD SCIM 实现支持的用户和组架构。

  * **[第 2 步：了解 Azure AD SCIM 实现。](#step-2-understand-the-azure-ad-scim-implementation)** 了解 Azure AD SCIM 客户端是如何实现的，并为您的 SCIM 协议请求处理和响应建模。

  * **[步骤 3：构建 SCIM 终结点。](#step-3-build-a-scim-endpoint)** 终结点必须与 SCIM 2.0 兼容才能与 Azure AD 预配服务集成。 作为一个选项，您可以使用 Microsoft 通用语言基础结构 （CLI） 库和代码示例来构建终结点。 这些示例仅供参考和测试;我们建议不要对生产应用进行编码，以依赖它们。

  * **[步骤 4：将 SCIM 终结点与 Azure AD SCIM 客户端集成。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 如果您的组织正在使用实现 Azure AD 支持的 SCIM 2.0 配置文件的第三方应用程序，则可以立即开始自动预配和取消预配用户和组。

  * **[步骤 5：将应用程序发布到 Azure AD 应用程序库。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 使客户能够轻松发现您的应用程序并轻松配置预配。 

![将 SCIM 终结点与 Azure AD 集成的步骤](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>第 1 步：设计用户和组架构

每个应用程序都需要不同的属性来创建用户或组。 通过标识应用程序所需的对象（用户、组）和属性（名称、经理、职务等）来启动集成。 SCIM 标准定义了用于管理用户和组的架构。 核心用户架构只需要三个属性 **：id（** 服务提供商定义的标识符）、外部**Id（** 客户端定义的标识符）和**元**（由服务提供商维护的只读元数据）。 所有其他属性都是可选的。 除了核心用户架构外，SCIM 标准还定义了企业用户扩展和用于扩展用户架构以满足应用程序需求的模型。 例如，如果应用程序需要用户的经理，则可以使用企业用户架构收集用户的经理和核心架构来收集用户的电子邮件。 要设计架构，请按照以下步骤操作：
  1. 列出应用程序所需的属性。 将您的要求分解为身份验证所需的属性（例如登录名和电子邮件）、管理用户生命周期所需的属性（例如状态/活动）以及特定应用程序工作所需的其他属性（例如管理器、标记）会很有帮助。
  2. 检查这些属性是否已在核心用户架构或企业用户架构中定义。 如果需要并且不在核心或企业用户架构中涵盖任何属性，则需要定义用户架构的扩展，该扩展涵盖所需的属性。 在下面的示例中，我们向用户添加了一个扩展，以允许在用户上预配"标记"。 最好从核心和企业用户架构开始，然后扩展到其他自定义架构。  
  3. 将 SCIM 属性映射到 Azure AD 中的用户属性。 如果在 SCIM 终结点中定义的一个属性在 Azure AD 用户架构上没有明确的对应项，则大多数租户上很可能数据根本不存储在用户对象上。 请考虑此属性是否可以为创建用户而可选。 如果属性对于应用程序正常工作至关重要，请指导租户管理员扩展其架构或使用扩展属性，如下所示的"标记"属性。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表 1：概述所需的属性 
| 第 1 步：确定应用所需的属性| 第 2 步：将应用要求映射到 SCIM 标准| 步骤 3：将 SCIM 属性映射到 Azure AD 属性|
|--|--|--|
|登录名|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|名称.姓氏|lastName|
|工作邮件|电子邮件[键入 eq"工作"]值|Mail|
|manager|manager|manager|
|标记|urn：ietf：参数：scim：架构：扩展：2.0：自定义扩展：标记|extensionAttribute1|
|status|活动|已软删除（计算值未存储在用户上）|

上面定义的架构将使用下面的 Json 负载表示。 请注意，除了应用程序所需的属性外，JSON 表示形式还包括所需的"id"、"外部 Id"和"元"属性。

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
然后，可以使用下表了解应用程序所需的属性如何映射到 Azure AD 和 SCIM RFC 中的属性。 您可以[自定义](customize-application-attributes.md)属性在 Azure AD 和 SCIM 终结点之间映射的方式。 请注意，您不需要同时支持用户和组或下面显示的所有属性。 它们是 Azure AD 中属性通常映射到 SCIM 协议中属性的参考。 

| Azure Active Directory 用户 | “urn:ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |活动 |
|department|urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：部门|
| displayName |displayName |
|employeeId|urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：员工编号|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：经理 |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>表 3：默认组属性映射

| Azure Active Directory 组 | urn：ietf：参数：scim：schema：核心：2.0：组 |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC 中定义了多个终结点。 您可以开始使用 /User 终结点，然后从那里展开。 使用自定义属性或架构频繁更改时，/Schema 终结点非常有用。 它使客户端能够自动检索最新的架构。 /Bulk 终结点在支持组时特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 使用自定义属性或架构频繁更改时，/Schema 终结点非常有用。 它使客户端能够自动检索最新的架构。 /Bulk 终结点在支持组时特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表 4：确定要开发的结束点
|ENDPOINT|DESCRIPTION|
|--|--|
|/User|对用户对象执行 CRUD 操作。|
|/Group|对组对象执行 CRUD 操作。|
|/服务提供商配置|提供有关支持的 SCIM 标准功能的详细信息，例如支持的资源和身份验证方法。|
|/资源类型|指定有关每个资源的元数据|
|/架构|每个客户端和服务提供商支持的属性集可能有所不同。 一个服务提供商可能包括"名称"、"标题"和"电子邮件"，而另一个服务提供商则使用"名称"、"标题"和"电话号码"。 架构终结点允许发现支持的属性。|
|/体积|批量操作允许您在单个操作中对大量资源对象执行操作（例如，更新大型组的成员身份）。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>第 2 步：了解 Azure AD SCIM 实现
> [!IMPORTANT]
> Azure AD SCIM 实现的行为最近于 2018 年 12 月 18 日更新。 有关更改内容的信息，请参阅 [Azure AD 用户预配服务 SCIM 2.0 协议合规性](application-provisioning-config-problem-scim-compatibility.md)。

如果要构建支持 SCIM 2.0 用户管理 API 的应用程序，本节将详细介绍如何实现 Azure AD SCIM 客户端。 它还演示如何对 SCIM 协议请求处理和响应建模。 实现 SCIM 终结点后，可以通过按照上一节中描述的过程对其进行测试。

在[SCIM 2.0 协议规范](http://www.simplecloud.info/#Specification)中，您的应用程序必须满足以下要求：

* 支持根据 SCIM 协议 的第[3.3](https://tools.ietf.org/html/rfc7644#section-3.3)节创建用户，也可以选择组。  
* 支持根据[SCIM 协议的第 3.5.2 节](https://tools.ietf.org/html/rfc7644#section-3.5.2)修改具有 PATCH 请求的用户或组。  
* 支持检索前面创建的用户或组的已知资源，如[SCIM 协议的第 3.4.1 节](https://tools.ietf.org/html/rfc7644#section-3.4.1)。  
* 支持查询用户或组，如[SCIM 协议的第 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2)节。  `id`默认情况下，用户由其`username``externalid`和 检索和查询，并且组由`displayName`查询。  
* 根据 SCIM 协议的第 3.4.2 节，支持按 ID 和管理器查询用户。  
* 根据 SCIM 协议的第 3.4.2 节，支持按 ID 和成员查询组。  
* 接受单个承载令牌，以便对应用程序进行 Azure AD 的身份验证和授权。

在实现 SCIM 终结点时，请遵循以下一般准则，以确保与 Azure AD 兼容：

* `id`是所有资源的必要属性。 返回资源的每个响应应确保每个资源都具有此属性，但成员为零除外`ListResponse`。
* 对查询/筛选器请求的响应应始终为`ListResponse`。
* 组是可选的，但仅当 SCIM 实现支持 PATCH 请求时才受支持。
* 不必将整个资源包含在 PATCH 响应中。
* Microsoft Azure AD 仅使用以下运算符：  
    - `eq`
    - `and`
* 不需要对 SCIM 中的结构元素（尤其是 在 中`op`https://tools.ietf.org/html/rfc7644#section-3.5.2定义的 PATCH 操作值）进行区分大小写的匹配。 Azure AD 将"op"的值表示为`Add``Replace`和`Remove`。
* Microsoft Azure AD 发出获取随机用户和组的请求，以确保终结点和凭据有效。 它还作为[Azure 门户](https://portal.azure.com)中的**测试连接**流的一部分完成。 
* 可以查询资源的属性应设置为[Azure 门户](https://portal.azure.com)中的应用程序中的匹配属性。 有关详细信息，请参阅[自定义用户预配属性映射](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>用户预配和取消预配

下图显示了 Azure 活动目录发送到 SCIM 服务以管理应用程序标识存储中用户的生命周期的消息。  

![显示用户预配和取消预配序列](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*用户预配和取消预配序列*

### <a name="group-provisioning-and-deprovisioning"></a>组预配和取消预配

组预配和取消预配是可选的。 实现并启用后，下图将显示 Azure AD 发送到 SCIM 服务以管理应用程序标识存储中的组的生命周期的消息。  这些消息与有关用户的消息有两种不同：

* 检索组的请求指定成员属性将从响应请求提供的任何资源中排除。  
* 确定引用属性是否具有特定值的请求是有关成员属性的请求。  

![显示组预配和取消预配序列](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*组预配和取消预配顺序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 协议请求和响应
本节提供 Azure AD SCIM 客户端发出的 SCIM 请求示例以及预期响应的示例。 为获得最佳效果，应编写应用代码以以此格式处理这些请求，并发出预期的响应。

> [!IMPORTANT]
> 要了解 Azure AD 用户预配服务如何以及何时发出下面描述的操作，请参阅[预配周期部分：预配的工作原理中的初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。 [How provisioning works](how-provisioning-works.md)

[用户操作](#user-operations)
  - [创建用户](#create-user)（[请求](#request) / [响应](#response)）
  - [获取用户](#get-user)（[请求](#request-1) / [响应](#response-1)）
  - [通过查询获取用户](#get-user-by-query)（[请求](#request-2) / [响应](#response-2)）
  - [通过查询获取用户 - 零结果](#get-user-by-query---zero-results)（[请求](#request-3)
/ [响应](#response-3)）
  - [更新用户 [多值属性]](#update-user-multi-valued-properties) （[请求](#request-4) /  [响应](#response-4)）
  - [更新用户 [单值属性]](#update-user-single-valued-properties) （[请求](#request-5)
/ [响应](#response-5)） 
  - [禁用用户](#disable-user)（[请求](#request-14) / 
[响应](#response-14)）
  - [删除用户](#delete-user)（[请求](#request-6) / 
[响应](#response-6)）


[集团运营](#group-operations)
  - [创建组](#create-group)（[请求](#request-7) / [响应](#response-7)）
  - [获取组](#get-group)（[请求](#request-8) / [响应](#response-8)）
  - [按显示名称获取组](#get-group-by-displayname)（[请求](#request-9) / [响应](#response-9)）
  - [更新组 [非成员属性]](#update-group-non-member-attributes) （[请求](#request-10)/
 [响应](#response-10)）
  - [更新组 [添加成员]](#update-group-add-members) （[请求](#request-11) /
[响应](#response-11)）
  - [更新组 [删除成员]](#update-group-remove-members) （[请求](#request-12) /
[响应](#response-12)）
  - [删除组](#delete-group)（[请求](#request-13) /
[响应](#response-13)）

### <a name="user-operations"></a>用户操作

* 用户可以按`userName`或`email[type eq "work"]`属性查询。  

#### <a name="create-user"></a>创建用户

###### <a name="request"></a>请求

*POST /用户*
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

*HTTP/1.1 201 已创建*
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
*GET /用户/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>响应（找到用户）
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
*GET /用户/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>响应（找不到用户。 请注意，详细信息不是必需的，仅状态。

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

*GET /用户？过滤器_用户名 eq"Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

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

#### <a name="get-user-by-query---zero-results"></a>通过查询获取用户 - 零结果

##### <a name="request"></a><a name="request-3"></a>请求

*GET /用户？筛选器_用户名 eq"不存在的用户"*

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

*PATCH /用户/6764549bef60420686bc HTTP/1.1*
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

*PATCH /用户/5171a35d82074e068ce2 HTTP/1.1*
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

*PATCH /用户/5171a35d82074e068ce2 HTTP/1.1*
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

*删除 /用户/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>响应

*HTTP/1.1 204 无内容*

### <a name="group-operations"></a>集团运营

* 应始终使用空成员列表创建组。
* 属性可以查询`displayName`组。
* 对组 PATCH 请求的更新应在响应中生成*HTTP 204 无内容*。 返回具有所有成员列表的正文是不可取的。
* 没有必要支持返回组的所有成员。

#### <a name="create-group"></a>创建组

##### <a name="request"></a><a name="request-7"></a>请求

*POST /组 HTTP/1.1*
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

*HTTP/1.1 201 已创建*
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

*GET /组/40734ae655284ad3abcc？排除属性_成员 HTTP/1.1*

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

#### <a name="get-group-by-displayname"></a>按显示名称获取组

##### <a name="request"></a><a name="request-9"></a>请求
*GET /组？排除属性_成员&筛选器*显示名称 eq "显示名称" HTTP/1.1*

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

*PATCH /组/fa2ce2670934589afc5 HTTP/1.1*
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

*PATCH /组/a99962b9f99d4c4fac67 HTTP/1.1*
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

*PATCH /组/a99962b9f99d4c4fac67 HTTP/1.1*
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

*删除 /组/cdb1ce1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>响应

*HTTP/1.1 204 无内容*

### <a name="security-requirements"></a>安全要求
**TLS 协议版本**

唯一可接受的 TLS 协议版本是 TLS 1.2 和 TLS 1.3。 不允许其他版本的 TLS。 不允许使用 SSL 版本。 
- RSA 密钥必须至少为 2，048 位。
- ECC 键必须至少为 256 位，使用经批准的椭圆曲线生成


**键长度**

所有服务都必须使用使用足够长度的加密密钥生成的 X.509 证书，这意味着：

**密码套房**

所有服务都必须配置为按照下面指定的确切顺序使用以下密码套件。 请注意，如果您只有 RSA 证书，则安装 ECDSA 密码套件没有任何效果。 </br>

TLS 1.2 密码套房最小酒吧：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>第 3 步：构建 SCIM 终结点

现在，您已经设计了架构并了解了 Azure AD SCIM 实现，您可以开始开发 SCIM 终结点。 您可以依赖 SCIM 社区发布的许多开源 SCIM 库，而不是从头开始，完全独立构建实现。

Azure AD 预配团队发布的开源 .NET Core[参考代码](https://aka.ms/SCIMReferenceCode)是能够快速启动开发的资源之一。 生成 SCIM 终结点后，将需要对其进行测试。您可以使用作为参考代码一部分提供的[邮递员测试](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)的集合，或运行[上面](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)提供的示例请求/响应。  

   > [!Note]
   > 参考代码旨在帮助您开始构建 SCIM 终结点，并提供"AS IS"。 欢迎社区提供捐助，帮助构建和维护代码。

该解决方案由两个项目组成，_即微软.SCIM_和_微软.SCIM.WebHostSample_。

_Microsoft.SCIM_项目是定义符合 SCIM 规范的 Web 服务的组件的库。 它声明接口_Microsoft.SCIM.IProvider，_ 请求被转换为对提供程序方法的调用，这些方法将被编程为在标识存储上运行。

![细分：转换为对提供程序方法的调用的请求](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft.SCIM.WebHostSample_项目是基于_空_模板的可视化工作室ASP.NET核心 Web 应用程序。 这允许将示例代码部署为独立代码、托管在容器中或 Internet 信息服务中。 它还实现了_Microsoft.SCIM.IProvider_接口，将类保留在内存中作为示例标识存储。

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

SCIM 服务必须具有 HTTP 地址和服务器身份验证证书，根证书颁发机构是以下名称之一：

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK 包括可在开发期间使用的 HTTPS 开发证书，该证书作为首次运行体验的一部分安装。 根据运行ASP.NET核心 Web 应用程序的方式，它将侦听其他端口：

* 微软.SCIM.网络主机示例：https://localhost:5001
* IIS 快递：https://localhost:44359/

有关 ASP.NET 核心中的 HTTPS 的详细信息，请使用以下链接：[在 ASP.NET核心中强制实施 HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>处理终结点身份验证

来自 Azure Active Directory 的请求包括 OAuth 2.0 持有者令牌。 接收请求的任何服务都应将颁发者验证为预期的 Azure 活动目录租户的 Azure 活动目录。

在令牌中，颁发者由 iss 声明标识，如`"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`。 在此示例中，声明值的基本地址`https://sts.windows.net`， 将 Azure 活动目录标识为颁发者， 而相对地址段_cbb1a5ac-f33b-45fa-f37db0fed422_， 是颁发令牌的 Azure 活动目录租户的唯一标识符。

令牌的受众将是库中应用程序的应用程序模板 ID，在单个租户中注册的每个应用程序都可能会收到与 SCIM 请求相同的`iss`声明。 库中每个应用程序的应用程序模板 ID 各不相同，请与库[ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com)应用程序有关的应用程序模板 ID 的问题联系。 所有自定义应用的应用程序模板 ID 为_8adf8e6e-67b2-4cf2-a259-e3dc5476c621_。

在示例代码中，使用 Microsoft.AspNetCore.身份验证.JwtBearer 包对请求进行身份验证。 以下代码强制强制使用 Azure Active Directory 为指定租户发出的无记名令牌对对服务的任何终结点的请求进行身份验证：

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

使用提供的[邮递员测试](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)并使用本地主机执行本地调试也需要承载令牌。 示例代码使用 ASP.NET 酷环境在开发阶段更改身份验证选项，并启用使用自签名令牌。

有关 ASP.NET 核心中的多个环境的详细信息，请使用以下链接：[在 ASP.NET核心中使用多个环境](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

以下代码强制强制对服务的任何终结点的请求使用使用自定义密钥签名的承载令牌进行身份验证：

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

向令牌控制器发送 GET 请求以获取有效的无记名令牌，方法_GenerateJSONWebToken_负责创建一个令牌，匹配为开发配置的参数：

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

***示例 1.查询匹配用户的服务***

Azure Active Directory 会在服务中查询是否有某个用户的 externalId 属性值与 Azure AD 中用户的 mailNickname 属性值匹配。 查询表示为超文本传输协议 （HTTP） 请求，例如，其中 jyoung 是 Azure 活动目录中用户的邮件昵称的示例。

>[!NOTE]
> 这只是一个示例。 并非所有用户都将具有 mailNickname 属性，并且用户拥有的值在目录中可能并非唯一。 此外，用于匹配的属性（在本例中为外部 Id）在[Azure AD 属性映射中](customize-application-attributes.md)可配置。

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

在示例代码中，请求将转换为对服务提供者的 QueryAsync 方法的调用。 以下是该方法的签名： 

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

在示例查询中，对于具有外部 Id 属性给定值的用户，传递给 QueryAsync 方法的参数的值为：

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***示例 2.预配用户***

如果具有与用户的邮件昵称属性值匹配的外部 Id 属性值的用户对 Web 服务的查询的响应不返回任何用户，则 Azure Active Directory 请求该服务预配与该用户对应的用户在 Azure 活动目录中。  以下是此类请求的示例： 

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

在示例代码中，请求将转换为对服务提供者的 CreateAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

在预配用户的请求中，资源参数的值是 Microsoft.SCIM.Core2EnterpriseUser 类的实例，该类在 Microsoft.SCIM.Schemas 库中定义。  如果预配用户的请求成功，则该方法的实现应返回 Microsoft.SCIM.Core2EnterpriseUser 类的实例，其中标识符属性的值设置为新预配的唯一标识符用户。  

***示例 3.查询用户的当前状态*** 

为了更新存在于前端为 SCIM 的标识存储中的已知用户，Azure Active Directory 将通过类似于下面的请求向服务请求该用户的当前状态来继续处理： 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

在示例代码中，请求将转换为对服务提供程序的检索 Async 方法的调用。 以下是该方法的签名： 

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

***示例 4.查询要更新的引用属性的值*** 

如果要更新引用属性，则 Azure Active Directory 会查询服务，以确定服务前面的标识存储中的引用属性的当前值是否已与 Azure Active 中该属性的值匹配目录。 对于用户，以这种方式查询当前值的唯一属性是 manager 属性。 下面是一个请求的示例，用于确定用户对象的管理器属性当前是否具有特定值：在示例代码中，请求将转换为对服务提供者的 QueryAsync 方法的调用。 作为参数自变量值提供的对像属性值如下： 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

在这里，索引 x 的值可以是 0，索引 y 的值可以是 1，或者 x 的值可以是 1，y 的值可以是 0，具体取决于筛选器查询参数的表达式的顺序。   

***示例 5.从 Azure AD 请求 SCIM 服务以更新用户*** 

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

在示例代码中，请求将转换为对服务提供程序的 UpdateAsync 方法的调用。 以下是该方法的签名： 

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

***示例 6.取消预配用户***

要从 SCIM 服务前面的标识存储中取消预配用户，Azure AD 会发送请求，例如：

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

在示例代码中，请求将转换为对服务提供程序的 DeleteAsync 方法的调用。 以下是该方法的签名： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

作为资源标识符参数的值提供的对象在取消预配用户的请求示例中具有这些属性值： 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>第 4 步：将 SCIM 终结点与 Azure AD SCIM 客户端集成

Azure AD 可以配置为自动将分配的用户和组预配到实现[SCIM 2.0 协议](https://tools.ietf.org/html/rfc7644)的特定配置文件的应用程序。 配置文件的细节记录在[步骤 2：了解 Azure AD SCIM 实现](#step-2-understand-the-azure-ad-scim-implementation)。

请咨询应用程序提供者，或参阅应用程序提供者文档中的说明，以了解是否符合这些要求。

> [!IMPORTANT]
> Azure AD SCIM 实现构建在 Azure AD 用户预配服务之上，该服务旨在让用户在 Azure AD 和目标应用程序之间保持同步，并实现一组非常特定的标准操作。 了解 Azure AD SCIM 客户端的行为非常重要。 有关详细信息，请参阅预配周期部分：[预配的工作原理](how-provisioning-works.md)中[的初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。

### <a name="getting-started"></a>入门

支持本文所述 SCIM 配置文件的应用程序可以使用 Azure AD 应用程序库中的“非库应用程序”功能连接到 Azure Active Directory。 连接后，Azure AD 将每隔 40 分钟运行同步过程，此过程将为分配的用户和组查询应用程序的 SCIM 终结点，并根据分配详细信息创建或修改这些用户和组。

**连接到支持 SCIM 的应用程序：**

1. 登录到 Azure[活动目录门户](https://aad.portal.azure.com)。 请注意，您可以通过注册[开发人员程序](https://developer.microsoft.com/office/dev-program)获得使用 P2 许可证的 Azure 活动目录的免费试用版
2. 从左侧窗格中选择**企业应用程序**。 将显示所有已配置应用的列表，包括从库中添加的应用。
3. 选择 **= 新应用程序** > **所有** > **非库应用程序**。
4. 输入应用程序的名称，然后选择 **"添加"** 以创建应用对象。 新应用将添加到企业应用程序列表中，并打开到其应用管理屏幕。

   ![屏幕截图显示 Azure AD 应用程序库](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 应用程序库*

5. 在应用管理屏幕中，选择左侧面板中的**预配**。
6. 在“预配模式”菜单中，选择“自动”********。

   ![示例：Azure 门户中的应用预配页](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *在 Azure 门户中配置预配*

7. 在“租户 URL”字段中，输入应用程序的 SCIM 终结点的 URL****。 示例： `https://api.contoso.com/scim/`
8. 如果 SCIM 终结点需要来自非 Azure AD 颁发者的 OAuth 持有者令牌，可将所需的 OAuth 持有者令牌复制到可选的“密钥令牌”字段****。 如果此字段留空，Azure AD 包含从 Azure AD 为每个请求发出的 OAuth 承载令牌。 将 Azure AD 用作标识提供者的应用可以验证 Azure AD 颁发的此令牌。 
   > [!NOTE]
   > ***不***建议将此字段留空并依赖于 Azure AD 生成的令牌。 此选项主要用于测试目的。
9. 选择 **"测试连接**"可尝试 Azure 活动目录连接到 SCIM 终结点。 如果尝试失败，将显示错误信息。  

    > [!NOTE]
    > **测试连接**使用随机 GUID 作为在 Azure AD 配置中选择的匹配属性，针对不存在的用户查询 SCIM 终结点。 预期正确响应为“HTTP 200 正常”以及空的 SCIM ListResponse 消息。

10. 如果尝试连接到应用程序成功，请选择 **"保存**"以保存管理员凭据。
11. 在 **"映射"** 部分中，有两组可选[的属性映射](customize-application-attributes.md)：一组用于用户对象，一组为组对象。 分别选择它们，查看从 Azure Active Directory 同步到应用的属性。 选为“匹配”属性的特性用于匹配应用中的用户和组，以执行更新操作****。 选择“保存”，提交所有更改****。

    > [!NOTE]
    > 也可通过禁用“组”映射来选择性禁用组对象的同步。

12. “设置”下的“作用域”字段定义同步的用户和组********。 选择 **"仅同步仅分配的用户和组**（推荐）"以仅同步在"**用户和组**"选项卡中分配的用户和组。
13. 配置完成后，将**预配状态**设置为**On**。
14. 选择 **"保存"** 以启动 Azure AD 预配服务。
15. 如果仅同步分配的用户和组（建议），请确保选择 **"用户和组**"选项卡并分配要同步的用户或组。

初始周期开始后，您可以在左侧面板中选择**预配日志**以监视进度，显示应用上的预配服务完成的所有操作。 若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始周期执行的时间比以后同步长，只要服务在运行时，大约每 40 分钟发生一次同步。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>步骤 5：将应用程序发布到 Azure AD 应用程序库

如果要构建将由多个租户使用的应用程序，则可以使其在 Azure AD 应用程序库中可用。 这将使组织能够轻松发现应用程序并配置预配。 在 Azure AD 库中发布应用并使预配可供他人使用非常简单。 在[此处](../develop/howto-app-gallery-listing.md)查看步骤。 Microsoft 将与您合作，将您的应用程序集成到我们的库中，测试您的终结点，并发布载入[文档](../saas-apps/tutorial-list.md)供客户使用。 

### <a name="gallery-onboarding-checklist"></a>画廊入职检查表
按照下面的检查表操作，确保您的应用程序快速上载，并且客户具有流畅的部署体验。 信息将在您入用时从您这里收集。 
> [!div class="checklist"]
> * 支持[SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation)用户和组终结点（只需要一个，但建议同时使用两个终结点）
> * 每个租户每秒至少支持 25 个请求（必需）
> * 建立工程和支持联系人，指导客户在画廊后入职（必需）
> * 3 应用程序的非过期测试凭据（必需）
> * 支持 OAuth 授权代码授予或长寿命令牌，如下所述（必需）
> * 建立工程和支持联系点，支持客户在画廊入职后（必需）
> * 支持使用单个 PATCH 更新多个组成员身份（推荐） 
> * 公开记录 SCIM 终结点（推荐） 
> * [支持架构发现](https://tools.ietf.org/html/rfc7643#section-6)（推荐）


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>在应用程序库中预配连接器的授权
SCIM 规范未定义特定于 SCIM 的身份验证和授权方案。 它依赖于现有行业标准的使用。 Azure AD 预配客户端支持库中应用程序的两种授权方法。 

|授权方法|优点|缺点|支持|
|--|--|--|--|
|用户名和密码（Azure AD 不推荐或不支持）|易于实施|不安全 -[你的Pa$$word并不重要](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|支持库应用的逐案使用。 不支持非库应用。|
|长寿命无记名令牌|长寿命令牌不需要用户存在。 在设置预配时，管理员可以轻松使用它们。|如果不使用不安全的方法（如电子邮件），则很难与管理员共享长期存在的令牌。 |支持库和非库应用。 |
|OAuth 授权代码授予|访问令牌的寿命比密码短得多，并且具有长期承载令牌所没有的自动刷新机制。  在初始授权期间，必须存在真正的用户，从而增加一定程度的责任。 |要求用户在场。 如果用户离开组织，令牌将无效，需要再次完成授权。|支持库应用。 正在支持非库应用。|
|OAuth 客户端凭据授予|访问令牌的寿命比密码短得多，并且具有长期承载令牌所没有的自动刷新机制。 授权代码授予和客户端凭据授予都会创建相同类型的访问令牌，因此在这些方法之间移动对 API 是透明的。  预配可以完全自动化，无需用户交互即可静默请求新令牌。 ||库和非库应用不受支持。 支持在我们的积压中。|

[!NOTE] 不建议将令牌字段留空在 Azure AD 预配配置配置自定义应用 UI 中。 生成的令牌主要用于测试目的。

**OAuth 授权代码授予流：** 预配服务支持[授权代码授予](https://tools.ietf.org/html/rfc6749#page-24)。 在库中提交发布应用的请求后，我们的团队将与您合作收集以下信息：
*  授权 URL：客户端通过用户代理重定向从资源所有者处获取授权的 URL。 用户被重定向到此 URL 以授权访问。 
*  令牌交换 URL：客户端用于交换访问令牌的授权授予的 URL，通常使用客户端身份验证。
*  客户端 ID：授权服务器向注册客户端颁发客户端标识符，该标识符是表示客户端提供的注册信息的唯一字符串。  客户端标识符不是机密，但客户端标识符不是机密。它暴露给资源所有者，**不能**单独用于客户端身份验证。  
*  客户端密钥：客户端密钥是授权服务器生成的机密。 它应该是只有授权服务器知道的唯一值。 

请注意，由于客户端机密的暴露，不支持 OAuth v1。 支持 OAuth v2。  

最佳做法（建议但不是必需的）：
* 支持多个重定向 URL。 管理员可以从"portal.azure.com"和"aad.portal.azure.com"配置预配。 支持多个重定向 URL 将确保用户可以授权从任一门户进行访问。
* 支持多个机密，以确保顺利续订机密，而不会停机。 

**长期生活的 OAuth 承载代币：** 如果应用程序不支持 OAuth 授权代码授予流，则还可以生成比管理员可用于设置预配集成的长寿命 OAuth 承载令牌。 令牌应为永久，否则在令牌过期时将[隔离](application-provisioning-quarantine-status.md)预配作业。 此令牌的大小必须低于 1KB。  

有关其他身份验证和授权方法，请让我们知道[用户语音](https://aka.ms/appprovisioningfeaturerequest)。

### <a name="gallery-go-to-market-launch-check-list"></a>画廊上市启动检查列表
为了帮助提高我们联合整合的意识和需求，我们建议您更新现有文档并扩大营销渠道中的集成。  以下是一组清单活动，我们建议您完成以支持启动

* **销售和客户支持就绪。** 确保您的销售和支持团队了解并可以与集成功能对话。 向销售和支持团队简要介绍情况，为他们提供常见问题解答，并将集成到销售材料中。 
* **博客文章和/或新闻稿。** 制作一篇博客文章或新闻稿，描述联合整合、益处以及如何开始。 [示例：Imprivata 和 Azure 活动目录新闻稿](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **社交媒体。** 利用 Twitter、Facebook 或LinkedIn等社交媒体，向客户推广集成。 请务必包括@AzureAD，以便我们可以转推您的帖子。 [示例： Imprivata 推特帖子](https://twitter.com/azuread/status/1123964502909779968)
* **营销网站。** 创建或更新营销页面（例如集成页面、合作伙伴页面、定价页面等），以包括联合集成的可用性。 [示例：平板集成页面](https://pingboard.com/org-chart-for)、[智能表集成页面](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)[、Monday.com定价页面](https://monday.com/pricing/) 
* **技术文档。** 创建有关客户如何开始操作的帮助中心文章或技术文档。 [示例：特使 + 微软 Azure 活动目录集成。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **客户沟通。** 通过客户沟通（每月通讯、电子邮件活动、产品发布说明）提醒客户新集成。 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>允许 Azure AD 预配服务使用的 IP 地址发出 SCIM 请求

某些应用允许其应用的入站流量。 为了使 Azure AD 预配服务按预期运行，必须允许使用的 IP 地址。 有关每个服务标记/区域的 IP 地址列表，请参阅 JSON 文件 - [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 您可以根据需要将这些 IP 下载和编程到防火墙中。 Azure AD 预配的预留 IP 范围可以在"AzureActiveDirectoryDomain 服务"下找到。

## <a name="related-articles"></a>相关文章

* [自动将用户预配和取消预配到 SaaS 应用](user-provisioning.md)
* [为用户预配自定义属性映射](customize-application-attributes.md)
* [为属性映射编写表达式](functions-for-customizing-application-data.md)
* [用户预配的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帐户预配通知](user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
