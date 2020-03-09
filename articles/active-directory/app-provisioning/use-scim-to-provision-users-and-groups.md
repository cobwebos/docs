---
title: 开发 SCIM 终结点，以便将用户预配到 Azure AD 的应用
description: 跨域标识管理（SCIM）的系统标准化自动用户预配。 了解如何开发 SCIM 终结点，如何将 SCIM API 与 Azure Active Directory 集成，并开始自动将用户和组预配到你的云应用程序中。
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
ms.openlocfilehash: 42fc10c1e7e88e36e4d2174671702e043fb96538
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926844"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>生成 SCIM 终结点并使用 Azure Active Directory （Azure AD）配置用户预配

作为应用程序开发人员，你可以使用系统来实现跨域标识管理（SCIM）用户管理 API，以便在应用程序和 Azure AD 之间自动预配用户和组。 本文介绍如何生成 SCIM 终结点并与 Azure AD 预配服务集成。 SCIM 规范提供了用于预配的常见用户架构。 与 SAML 或 OpenID Connect 等联合标准结合使用时，SCIM 为管理员提供了一种基于标准的端到端解决方案，用于访问管理。

SCIM 是两个终结点的标准化定义：/Users 终结点和/Groups 终结点。 它使用常见的 REST 谓词来创建、更新和删除对象，并为常见属性（如组名称、用户名、名字、姓氏和电子邮件）预定义架构。 提供 SCIM 2.0 REST API 的应用可减少或消除使用专有用户管理 API 的难点。 例如，任何符合 SCIM 的客户端都知道如何将 JSON 对象的 HTTP POST 发送到/Users 终结点，以创建新的用户条目。 不需要对相同的基本操作使用略微不同的 API，符合 SCIM 标准的应用可以立即利用预先存在的客户端、工具和代码。 

![使用 SCIM 从 Azure AD 预配到应用](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0 （RFC [7642](https://tools.ietf.org/html/rfc7642)、 [7643](https://tools.ietf.org/html/rfc7643)、 [7644](https://tools.ietf.org/html/rfc7644)）中定义的用于管理的标准用户对象架构和 rest api 允许标识提供者和应用更轻松地彼此集成。 生成 SCIM 终结点的应用程序开发人员无需进行自定义工作即可与任何 SCIM 兼容的客户端集成。

自动预配到应用程序需要生成 SCIM 终结点，并将其与 Azure AD SCIM 客户端集成。 执行以下步骤，开始将用户和组预配到应用程序。 
    
  * **[步骤 1：设计用户和组架构。](#step-1-design-your-user-and-group-schema)** 确定应用程序所需的对象和属性，并确定它们如何映射到 Azure AD SCIM 实现所支持的用户和组架构。

  * **[步骤 2：了解 Azure AD SCIM 实现。](#step-2-understand-the-azure-ad-scim-implementation)** 了解如何实现 Azure AD SCIM 客户端，并为 SCIM 协议请求处理和响应建模。

  * **[步骤 3：生成 SCIM 终结点。](#step-3-build-a-scim-endpoint)** 终结点必须是 SCIM 2.0 兼容的，才能与 Azure AD 预配服务集成。 作为选项，你可以使用 Microsoft 公共语言基础结构（CLI）库和代码示例来生成终结点。 这些示例仅用于引用和测试;建议不对生产应用进行编码，以依赖于它们。

  * **[步骤 4：将 SCIM 终结点与 Azure AD SCIM 客户端集成。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 如果你的组织使用的第三方应用程序实现 Azure AD 支持的 SCIM 2.0 的配置文件，则可以立即开始自动预配和取消预配用户和组。

  * **[步骤 5：将应用程序发布到 Azure AD 应用程序库。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 使客户能够轻松发现应用程序并轻松配置预配。 

![将 SCIM 终结点与 Azure AD 集成的步骤](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>步骤 1：设计用户和组架构

每个应用程序都需要使用不同的属性来创建用户或组。 通过标识应用程序所需的对象（用户、组）和属性（名称、管理器、作业标题等）开始集成。 SCIM 标准定义了用于管理用户和组的架构。 核心用户架构只需要三个属性： **id** （服务提供商定义的标识符）、 **externalId** （客户端定义的标识符）和**元**数据（由服务提供商维护的只读元数据）。 所有其他属性都是可选的。 除核心用户架构外，SCIM 标准还定义了一个企业用户扩展和一个模型，用于扩展用户架构以满足应用程序的需求。 例如，如果你的应用程序需要用户的管理者，则可以使用企业用户架构收集用户的管理器和核心架构以收集用户的电子邮件。 若要设计架构，请执行以下步骤：
  1. 列出应用程序所需的属性。 将要求分解到身份验证所需的属性（例如，loginName 和电子邮件）、管理用户的生命周期所需的属性（例如状态/活动）以及特定应用程序的工作所需的其他属性（例如，管理器、标记），这可能会很有帮助。
  2. 检查是否已在核心用户架构或企业用户架构中定义了这些属性。 如果核心或企业用户架构中不包含所需的任何属性，则需要定义包含所需属性的用户架构扩展。 在下面的示例中，我们已向用户添加了一个扩展，以允许在用户上设置 "标记"。 最好只从核心和企业用户架构开始，以后再展开到其他自定义架构。  
  3. 将 SCIM 属性映射到 Azure AD 中的用户属性。 如果在 SCIM 终结点中定义的属性之一在 Azure AD 用户架构上没有明确的对应项，则很有可能不会在大多数租户上将数据存储在用户对象上。 考虑此特性是否可用于创建用户。 如果该属性对您的应用程序运行至关重要，请指导租户管理员扩展其架构或使用如下所示的扩展属性。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表 1：概述所需的属性 
| 步骤 1：确定应用需要的属性| 步骤 2：将应用程序要求映射到 SCIM 标准| 步骤 3：将 SCIM 特性映射到 Azure AD 特性|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|名称 lastName|lastName|
|workMail|电子邮件 [类型 eq "work"]。值|Mail|
|manager|manager|manager|
|tag|urn： ietf： params： scim：架构：扩展：2.0： CustomExtension：标记|extensionAttribute1|
|status|활성|isSoftDeleted （计算值未存储在用户上）|

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

### <a name="table-2-default-user-attribute-mapping"></a>表 2：默认用户属性映射
然后，你可以使用下表来了解应用程序所需的属性如何映射到 Azure AD 中的属性和 SCIM RFC。 您可以[自定义](customize-application-attributes.md)属性在 AZURE AD 和 SCIM 终结点之间的映射方式。 请注意，不需要同时支持用户和组或下面显示的所有属性。 它们是有关 Azure AD 中的特性如何经常映射到 SCIM 协议中的属性的参考。 

| Azure Active Directory 사용자 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |활성 |
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


### <a name="table-3-default-group-attribute-mapping"></a>表 3：默认组属性映射

| Azure Active Directory 그룹 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC 中定义了多个终结点。 可以开始启用/User 终结点，并从该处进行扩展。 使用自定义属性或架构频繁更改时，/Schemas 终结点非常有用。 它使客户端能够自动检索最新的架构。 支持组时，/Bulk 终结点特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 使用自定义属性或架构频繁更改时，/Schemas 终结点非常有用。 它使客户端能够自动检索最新的架构。 支持组时，/Bulk 终结点特别有用。 下表描述了 SCIM 标准中定义的各种终结点。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表 4：确定想要开发的终结点
|ENDPOINT|DESCRIPTION|
|--|--|
|/User|对用户对象执行 CRUD 操作。|
|/Group|对组对象执行 CRUD 操作。|
|/ServiceProviderConfig|提供有关支持的 SCIM 标准功能的详细信息，例如，支持的资源和身份验证方法。|
|/ResourceTypes|指定有关每个资源的元数据|
|/Schemas|每个客户端和服务提供商支持的属性集可能有所不同。 尽管一个服务提供商可能包括 "名称"、"标题" 和 "电子邮件"，但另一个服务提供商使用 "名称"、"标题" 和 "phoneNumbers"。 架构端点允许发现支持的属性。|
|/Bulk|使用大容量操作，可以在单个操作中对大型资源对象集合执行操作（例如，更新大型组的成员身份）。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>步骤 2：了解 Azure AD SCIM 实现
> [!IMPORTANT]
> Azure AD SCIM 구현 동작이 2018년 12월 18일에 마지막으로 업데이트되었습니다. 변경된 내용에 대한 자세한 내용은 [Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 규정 준수](application-provisioning-config-problem-scim-compatibility.md)를 참조하세요.

如果构建的应用程序支持 SCIM 2.0 用户管理 API，本部分将详细介绍如何实现 Azure AD SCIM 客户端。 它还演示如何为 SCIM 协议请求处理和响应建模。 实现 SCIM 终结点后，可以按照上一部分中所述的过程对其进行测试。

在[SCIM 2.0 协议规范](http://www.simplecloud.info/#Specification)中，应用程序必须满足以下要求：

* 支持根据[SCIM 协议的第 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)节，创建用户和组（可选）。  
* 支持根据[SCIM 协议的每节 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)修改修补请求的用户或组。  
* 支持检索之前创建的用户或组的已知资源，如[SCIM 协议的3.4.1 节所](https://tools.ietf.org/html/rfc7644#section-3.4.1)述。  
* 支持查询用户或组，如[SCIM 协议的3.4.2 节所](https://tools.ietf.org/html/rfc7644#section-3.4.2)述。  默认情况下，用户由其 `id` 检索并通过其 `username` 和 `externalid`进行查询，并 `displayName`查询组。  
* 支持按 ID 和管理器查询用户，如 SCIM 协议的3.4.2 节所述。  
* 支持按 ID 和成员查询组，这与 SCIM 协议的每节3.4.2 一样。  
* 接受单个持有者令牌，用于对应用程序进行 Azure AD 身份验证和授权。

实现 SCIM 终结点时，请遵循以下常规准则，以确保与 Azure AD 的兼容性：

* `id` 是所有资源的必需属性。 每个返回资源的响应都应确保每个资源都具有此属性，但 `ListResponse` 零个成员除外。
* 查询/筛选器请求的响应应始终是 `ListResponse`。
* 组是可选的，但仅当 SCIM 实现支持修补程序请求时才受支持。
* 不需要在修补程序响应中包含整个资源。
* Microsoft Azure AD 只使用以下运算符：  
    - `eq`
    - `and`
* 对于 SCIM 中的结构元素，无需区分大小写匹配，具体 `op` 操作值，如 https://tools.ietf.org/html/rfc7644#section-3.5.2 中所定义。 Azure AD `Add`、`Replace`和 `Remove`发出 "op" 的值。
* Microsoft Azure AD 请求获取随机用户和组，以确保终结点和凭据有效。 它也作为[Azure 门户](https://portal.azure.com)中的**测试连接**流的一部分完成。 
* 可在其上查询资源的属性应设置为[Azure 门户](https://portal.azure.com)的应用程序上的匹配属性。 有关详细信息，请参阅[自定义用户预配属性映射](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>用户预配和取消预配

下图显示了 Azure Active Directory 发送到 SCIM 服务的消息，以管理应用程序的标识存储中用户的生命周期。  

![显示用户预配和取消设置序列](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*用户预配和取消预配顺序*

### <a name="group-provisioning-and-deprovisioning"></a>组预配和取消预配

组预配和取消设置是可选的。 实现并启用后，下图显示 Azure AD 发送到 SCIM 服务的消息，以管理应用程序的标识存储中某个组的生命周期。  这些消息在以下两个方面与用户消息不同：

* 检索组的请求指定要从为响应请求而提供的任何资源中排除成员属性。  
* 참조 특성에 특정 값이 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![显示组预配和取消设置序列](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*组预配和取消预配顺序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 协议请求和响应
本部分提供 Azure AD SCIM 客户端发出的示例 SCIM 请求以及预期的响应。 为了获得最佳结果，应将应用编码为按此格式处理这些请求，并发出预期的响应。

> [!IMPORTANT]
> 要了解 Azure AD 用户预配服务如何以及何时发出下面所述的操作，请参阅 [预配周期部分：初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)[了预配的工作方式](how-provisioning-works.md)。

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
  - [按 DisplayName 获取组](#get-group-by-displayname)（[请求](#request-9) / [响应](#response-9)）
  - [更新组 [非成员属性]](#update-group-non-member-attributes) （[请求](#request-10)/
 [响应](#response-10)）
  - [更新组 [添加成员]](#update-group-add-members) （[请求](#request-11) /
[响应](#response-11)）
  - [更新组 [删除成员]](#update-group-remove-members) （[请求](#request-12) /
[响应](#response-12)）
  - [删除组](#delete-group)（[请求](#request-13) /
[响应](#response-13)）

### <a name="user-operations"></a>用户操作

* 用户可以通过 `userName` 或 `email[type eq "work"]` 属性进行查询。  

#### <a name="create-user"></a>사용자 만들기

###### <a name="request"></a>요청

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

##### <a name="response"></a>응답

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

###### <a name="request-1"></a>需要
*获取/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>响应（已找到用户）
*HTTP/1.1 200 正常*
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

###### <a name="request"></a>요청
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

##### <a name="request-2"></a>需要

*GET/Users？ filter = userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>回复

*HTTP/1.1 200 正常*
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

##### <a name="request-3"></a>需要

*获取/Users？ filter = 用户名 eq "不存在的用户"*

##### <a name="response-3"></a>回复

*HTTP/1.1 200 正常*
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

##### <a name="request-4"></a>需要

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

##### <a name="response-4"></a>回复

*HTTP/1.1 200 正常*
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

##### <a name="request-5"></a>需要

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

##### <a name="response-5"></a>回复

*HTTP/1.1 200 正常*
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

##### <a name="request-14"></a>需要

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

##### <a name="response-14"></a>回复

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
#### <a name="delete-user"></a>사용자 삭제

##### <a name="request-6"></a>需要

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>回复

*HTTP/1.1 204 无内容*

### <a name="group-operations"></a>组操作

* 组应始终使用空成员列表创建。
* `displayName` 属性可以查询组。
* 更新到组修补请求应在响应中生成*HTTP 204 无内容*。 不建议使用所有成员列表返回正文。
* 不需要支持返回组的所有成员。

#### <a name="create-group"></a>그룹 만들기

##### <a name="request-7"></a>需要

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

##### <a name="response-7"></a>回复

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

##### <a name="request-8"></a>需要

*GET/Groups/40734ae655284ad3abcc？ excludedAttributes = members HTTP/1。1*

##### <a name="response-8"></a>回复
*HTTP/1.1 200 正常*
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

##### <a name="request-9"></a>需要
*GET/Groups？ excludedAttributes = members & filter = displayName eq "displayName" HTTP/1。1*

##### <a name="response-9"></a>回复

*HTTP/1.1 200 正常*
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

##### <a name="request-10"></a>需要

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response-10"></a>回复

*HTTP/1.1 204 无内容*

### <a name="update-group-add-members"></a>更新组 [添加成员]

##### <a name="request-11"></a>需要

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

##### <a name="response-11"></a>回复

*HTTP/1.1 204 无内容*

#### <a name="update-group-remove-members"></a>更新组 [删除成员]

##### <a name="request-12"></a>需要

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

##### <a name="response-12"></a>回复

*HTTP/1.1 204 无内容*

#### <a name="delete-group"></a>그룹 삭제

##### <a name="request-13"></a>需要

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>回复

*HTTP/1.1 204 无内容*

### <a name="security-requirements"></a>安全性要求
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


## <a name="step-3-build-a-scim-endpoint"></a>步骤 3：生成 SCIM 终结点

现在，你已设计了架构并了解 Azure AD SCIM 实现，接下来可以开始开发 SCIM 终结点。 您可以依赖于由 SCIM commuinty 发布的多个开源 SCIM 库，而不是从头开始创建并完全生成实现。  
Azure AD 预配团队发布的开源 .NET Core[参考代码](https://aka.ms/SCIMReferenceCode)是可以立即开始开发的一种资源。 生成 SCIM 终结点后，需要对其进行测试。你可以使用作为引用代码一部分提供的[postman 测试](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)的集合，或通过[上面](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)提供的示例请求/响应来运行。  

注意:参考代码旨在帮助你开始生成 SCIM 终结点，并按 "原样" 提供。 来自社区的贡献欢迎使用来帮助生成和维护代码。 

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>步骤 4：将 SCIM 终结点与 Azure AD SCIM 客户端集成

可以将 Azure AD 配置为自动将已分配的用户和组预配到实现了[SCIM 2.0 协议](https://tools.ietf.org/html/rfc7644)的特定配置文件的应用程序。 步骤 2 [中记录了该配置文件的详细信息：了解 Azure AD SCIM 实现](#step-2-understand-the-azure-ad-scim-implementation)。

애플리케이션 공급자 또는 애플리케이션 공급자의 설명서를 통해 이러한 요구 사항과의 호환성을 확인합니다.

> [!IMPORTANT]
> Azure AD SCIM 实现基于 Azure AD 用户预配服务，该服务旨在使用户不断地在 Azure AD 和目标应用程序之间保持同步，并实现一组非常具体的标准操作。 了解这些行为以了解 Azure AD SCIM 客户端的行为非常重要。 有关详细信息，请参阅 [预配周期的部分：初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)[了预配的工作方式](how-provisioning-works.md)。

### <a name="getting-started"></a>시작

Azure AD 애플리케이션 갤러리에 있는 "비-갤러리 애플리케이션" 기능을 사용하여 이 문서에서 설명한 SCIM 프로필을 지원하는 애플리케이션을 Azure Active Directory에 연결할 수 있습니다. 일단 연결되면 Azure AD는 할당된 사용자 및 그룹에 대해 애플리케이션의 SCIM 엔드포인트를 쿼리하고, 할당 세부 정보에 따라 이러한 사용자 및 그룹을 만들거나 수정하는 동기화 프로세스를 40분마다 실행합니다.

**SCIM을 지원하는 애플리케이션을 연결하려면:**

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)。 请注意，你可以通过注册[开发人员计划](https://developer.microsoft.com/office/dev-program)来访问使用 P2 许可证的 Azure Active Directory 免费试用版
2. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
3. 选择 " **+ 新建应用程序** > **所有** > **的非库应用程序**。
4. 输入应用程序的名称，然后选择 "**添加**" 以创建应用对象。 新应用将添加到企业应用程序列表中，并打开到其应用管理屏幕。

   ![屏幕快照显示 Azure AD 应用程序库](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 应用程序库*

5. 在应用管理屏幕的左侧面板中，选择 "**预配**"。
6. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.

   示例：![Azure 门户中的应用设置页](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *在 Azure 门户中配置预配*

7. **테넌트 URL** 필드에 애플리케이션의 SCIM 엔드포인트 URL을 입력합니다. 예: https://api.contoso.com/scim/
8. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 如果此字段保留为空，则 Azure AD 包括每个请求 Azure AD 颁发的 OAuth 持有者令牌。 ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다. 
   > [!NOTE]
   > 建议***不要***将此字段留空，并依赖于 Azure AD 生成的令牌。 此选项主要用于测试目的。
9. 选择 "**测试连接**"，Azure Active Directory 尝试连接到 SCIM 终结点。 如果尝试失败，将显示错误消息。  

    > [!NOTE]
    > **테스트 연결**은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다.

10. 如果尝试连接到应用程序成功，请选择 "**保存**" 以保存管理员凭据。
11. 在 "**映射**" 部分中，有两个可选择的[属性映射](customize-application-attributes.md)集：一个用于用户对象，一个用于组对象。 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    > [!NOTE]
    > 필요에 따라 "그룹" 매핑을 사용하지 않도록 설정하여 그룹 개체의 동기화를 비활성화할 수 있습니다.

12. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. 选择 "**仅同步分配的用户和组**（推荐）"，以便仅同步 "**用户和组**" 选项卡中分配的用户和组。
13. 配置完成后，将设置**状态**设置为 **"开**"。
14. 选择 "**保存**" 以启动 Azure AD 预配服务。
15. 如果仅同步分配的用户和组（推荐），请确保选择 "**用户和组**" 选项卡并分配要同步的用户或组。

初始周期开始后，可以在左侧面板中选择 "**设置日志**" 来监视进度，其中显示了预配服务对应用执行的所有操作。 Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.

> [!NOTE]
> 初始周期的执行时间比后续同步长，只要服务正在运行，大约每隔40分钟就会进行一次同步。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>步骤 5：将应用程序发布到 Azure AD 应用程序库

如果要构建的应用程序将由多个租户使用，则可以将其提供给 Azure AD 应用程序库。 这样，组织就可以轻松发现应用程序并配置设置。 在 Azure AD 库中发布你的应用程序并使其可供其他人使用非常简单。 [여기](../develop/howto-app-gallery-listing.md)서 단계를 확인하세요. Microsoft 将与你合作，将你的应用程序集成到我们的库、测试终结点，并发布载入[文档](../saas-apps/tutorial-list.md)供客户使用。 

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
*  授权 URL：由客户端使用的 URL，通过用户代理重定向从资源所有者获取授权。 用户被重定向到此 URL 以授权访问。 
*  令牌交换 URL：客户端用于交换访问令牌授权的 URL，通常使用客户端身份验证。
*  客户端 ID：授权服务器向注册的客户端颁发客户端标识符，该标识符是表示客户端提供的注册信息的唯一字符串。  客户端标识符不是机密;它向资源所有者公开，**不得**单独用于客户端身份验证。  
*  客户端密钥:客户端密码是由授权服务器生成的机密。 它应该是唯一的唯一值，只是授权服务器。 

请注意，由于公开了客户端机密，因此不支持 OAuth v1。 支持 OAuth v2。  

最佳做法（建议但不需要）：
* 支持多个重定向 Url。 管理员可以配置 "portal.azure.com" 和 "aad.portal.azure.com" 中的预配。 支持多个重定向 Url 将确保用户可以从任一门户授予访问权限。
* 支持多个机密，以确保顺利地续订密钥，而无需停机。 

**生存期较长的 OAuth 持有者令牌：** 如果你的应用程序不支持 OAuth 授权代码授予流，你还可以生成一个长期的 OAuth 持有者令牌，而不是管理员可用于设置预配集成。 令牌应为永久标记，否则在令牌过期时将[隔离](application-provisioning-quarantine-status.md)设置作业。 此令牌的大小必须小于 1 KB。  

有关其他身份验证和授权方法，请在[UserVoice](https://aka.ms/appprovisioningfeaturerequest)上告诉我们。

### <a name="gallery-go-to-market-launch-check-list"></a>库走向市场启动检查列表
为了帮助推动对联合集成的认知和需求，我们建议您更新现有文档，并放大您的营销渠道中的集成。  下面是我们建议你完成以支持启动的一组清单活动

* **销售和客户支持就绪情况。** 确保你的销售和支持团队了解，并可以与集成功能进行交流。 简要介绍销售和支持团队，向他们提供 Faq 并将集成添加到销售材料中。 
* **博客文章和/或按版本。** 创建一个博客文章或按版本，介绍联合集成、优点和入门方式。 示例：[Imprivata 和 Azure Active Directory 按](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **社交媒体。** 利用你的社交媒体（如 Twitter、Facebook 或 LinkedIn）来促进与客户的集成。 请确保包含 @AzureAD 以便转推您的帖子。 示例：[Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **营销网站。** 创建或更新市场营销页面（例如集成页、合作伙伴页、定价页等）以包括联合集成的可用性。 示例：[Pingboard 集成页](https://pingboard.com/org-chart-for)， [Smartsheet 集成页](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)， [Monday.com 定价页](https://monday.com/pricing/) 
* **技术文档。** 创建帮助中心文章或有关客户如何开始使用的技术文档。 示例：[Envoy + Microsoft Azure Active Directory 集成。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **客户通信。** 通过您的客户通信（每月新闻稿、电子邮件活动、产品发行说明）通知客户新的集成。 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>允许 Azure AD 预配服务使用的 IP 地址发出 SCIM 请求

某些应用允许入站流量发送到应用。 为了使 Azure AD 预配服务能够按预期运行，必须允许使用的 IP 地址。 각 서비스 태그/지역의 IP 주소 목록은 JSON 파일 - [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. 你可以根据需要将这些 Ip 下载并编程到防火墙中。 可以在 "AzureActiveDirectoryDomainServices" 下找到 Azure AD 预配的保留 IP 范围。

## <a name="related-articles"></a>관련 문서

* [自动执行用户预配和取消预配到 SaaS 应用](user-provisioning.md)
* [사용자 프로비전을 위한 사용자 지정 특성 매핑](customize-application-attributes.md)
* [为属性映射编写表达式](functions-for-customizing-application-data.md)
* [用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [계정 프로비전 알림](user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
