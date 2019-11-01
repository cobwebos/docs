---
title: SCIM 用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何构建 SCIM 终结点，如何将 SCIM API 与 Azure Active Directory 集成，并在应用程序中开始自动执行预配用户和组。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9c15a462692c257fac759998698679d9e59dc53
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242293"
---
# <a name="scim-user-provisioning-with-azure-active-directory"></a>SCIM 用户预配与 Azure Active Directory

用于跨域标识管理（[SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)）的系统是标准化协议和架构，旨在促进在系统之间管理标识的一致性。 当应用程序为用户管理支持 SCIM 终结点时，Azure AD 用户预配服务可以向此终结点发送请求来创建、修改或删除已分配的用户和组。

Azure AD 支持[预先集成的自动用户预配](../saas-apps/tutorial-list.md)的许多应用程序实现 SCIM 作为接收用户更改通知的方法。  除此之外，客户还可以使用 Azure 门户中的通用 "非库" 集成选项连接支持[SCIM 2.0 协议规范](https://tools.ietf.org/html/rfc7644)的特定配置文件的应用程序。

本文的主要重点是在 SCIM 2.0 的个人资料中，Azure AD 实现为非库应用的泛型 SCIM 连接器的一部分。 但是，成功测试支持 SCIM 和通用 Azure AD 连接器的应用程序是将 Azure AD 库中列出的应用程序作为支持用户预配的一步。 有关 Azure AD 应用程序库中列出应用程序的详细信息，请参阅[如何：在 Azure AD 应用程序库中列出应用程序](../develop/howto-app-gallery-listing.md)。

> [!IMPORTANT]
> Azure AD SCIM 实现的行为最近于 2018 年 12 月 18 日更新。 有关更改内容的信息，请参阅 [Azure AD 用户预配服务 SCIM 2.0 协议合规性](application-provisioning-config-problem-scim-compatibility.md)。

![显示从 Azure AD 到应用程序或标识存储的预配][0]<br/>
*图1：从 Azure Active Directory 预配到实现 SCIM 的应用程序或标识存储*

本文分为四个部分：

* **[将用户和组预配到支持 SCIM 2.0 的第三方应用程序](#provisioning-users-and-groups-to-applications-that-support-scim)** -如果你的组织使用第三方应用程序来实现 Azure AD 支持的 SCIM 2.0 的配置文件，则可以开始自动预配和立即取消预配用户和组。
* **[了解 AZURE AD SCIM 实现](#understanding-the-azure-ad-scim-implementation)** -如果构建的应用程序支持 SCIM 2.0 用户管理 API，本部分将详细介绍如何实现 Azure AD SCIM 客户端，以及如何为 SCIM 协议建模请求处理和响应。
* **[使用 MICROSOFT CLI 库生成 SCIM 终结点](#building-a-scim-endpoint-using-microsoft-cli-libraries)** -公共语言基础结构（CLI）库和代码示例演示了如何开发 SCIM 终结点和转换 SCIM 消息。  
* **[用户和组架构引用](#user-and-group-schema-reference)** -描述非库应用 Azure AD SCIM 实现所支持的用户和组架构。

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>将用户和组预配到支持 SCIM 的应用程序

可以将 Azure AD 配置为自动将已分配的用户和组预配到实现了[SCIM 2.0 协议](https://tools.ietf.org/html/rfc7644)的特定配置文件的应用程序。 配置文件的具体内容记录在[了解 AZURE AD SCIM 实现](#understanding-the-azure-ad-scim-implementation)中。

请咨询应用程序提供者，或参阅应用程序提供者文档中的说明，以了解是否符合这些要求。

> [!IMPORTANT]
> Azure AD SCIM 实现基于 Azure AD 用户预配服务，该服务旨在使用户不断地在 Azure AD 和目标应用程序之间保持同步，并实现一组非常具体的标准操作。 了解这些行为以了解 Azure AD SCIM 客户端的行为非常重要。 有关详细信息，请参阅[用户预配期间会发生什么情况？](user-provisioning.md#what-happens-during-provisioning)。

### <a name="getting-started"></a>入门

支持本文所述 SCIM 配置文件的应用程序可以使用 Azure AD 应用程序库中的“非库应用程序”功能连接到 Azure Active Directory。 连接后，Azure AD 将每隔 40 分钟运行同步过程，此过程将为分配的用户和组查询应用程序的 SCIM 终结点，并根据分配详细信息创建或修改这些用户和组。

**连接到支持 SCIM 的应用程序：**

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)。 请注意，你可以通过注册[开发人员计划](https://developer.microsoft.com/office/dev-program)来访问使用 P2 许可证的 Azure Active Directory 免费试用版
1. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择 " **+ 新建应用程序** > **所有** > **的非库应用程序**。
1. 输入应用程序的名称，然后选择 "**添加**" 以创建应用对象。 新应用将添加到企业应用程序列表中，并打开到其应用管理屏幕。

   ![屏幕快照显示 Azure AD 应用程序库][1]<br/>
   *图2： Azure AD 应用程序库*

1. 在应用管理屏幕的左侧面板中，选择 "**预配**"。
1. 在“预配模式”菜单中，选择“自动”。

   ![示例： Azure 门户中的应用设置页][2]<br/>
   *图3：在 Azure 门户中配置预配*

1. 在“租户 URL”字段中，输入应用程序的 SCIM 终结点的 URL。 示例： https://api.contoso.com/scim/
1. 如果 SCIM 终结点需要来自非 Azure AD 颁发者的 OAuth 持有者令牌，可将所需的 OAuth 持有者令牌复制到可选的“密钥令牌”字段。 如果此字段保留为空，则 Azure AD 包括每个请求 Azure AD 颁发的 OAuth 持有者令牌。 将 Azure AD 用作标识提供者的应用可以验证 Azure AD 颁发的此令牌。
1. 选择 "**测试连接**"，Azure Active Directory 尝试连接到 SCIM 终结点。 如果尝试失败，将显示错误消息。  

    > [!NOTE]
    > **测试连接**使用随机 GUID 作为在 Azure AD 配置中选择的匹配属性，针对不存在的用户查询 SCIM 终结点。 预期正确响应为“HTTP 200 正常”以及空的 SCIM ListResponse 消息。

1. 如果尝试连接到应用程序成功，请选择 "**保存**" 以保存管理员凭据。
1. 在“映射”部分中有两个可选的属性映射集：一个用于用户对象，一个用于组对象。 分别选择它们，查看从 Azure Active Directory 同步到应用的属性。 选为“匹配”属性的特性用于匹配应用中的用户和组，以执行更新操作。 选择“保存”，提交所有更改。

    > [!NOTE]
    > 也可通过禁用“组”映射来选择性禁用组对象的同步。

1. “设置”下的“作用域”字段定义同步的用户和组。 选择 "**仅同步分配的用户和组**（推荐）"，以便仅同步 "**用户和组**" 选项卡中分配的用户和组。
1. 配置完成后，将设置**状态**设置为 **"开**"。
1. 选择 "**保存**" 以启动 Azure AD 预配服务。
1. 如果仅同步分配的用户和组（推荐），请确保选择 "**用户和组**" 选项卡并分配要同步的用户或组。

初始周期开始后，可以在左侧面板中选择 "**审核日志**" 来监视进度，其中显示了预配服务对应用执行的所有操作。 若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始周期的执行时间比后续同步长，只要服务正在运行，大约每隔40分钟就会进行一次同步。

**若要将应用程序发布到 Azure AD 应用程序库：**

如果要生成将使用多个租户的应用程序，可以在 Azure AD 应用程序库中使用它。 这样，组织就可以轻松发现应用程序并配置设置。 在 Azure AD 库中发布你的应用程序并使其可供其他人使用非常简单。 在[此处](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)查看步骤。 
## <a name="understanding-the-azure-ad-scim-implementation"></a>了解 Azure AD SCIM 实现

如果构建的应用程序支持 SCIM 2.0 用户管理 API，本部分将详细介绍如何实现 Azure AD SCIM 客户端，以及如何对 SCIM 协议请求处理和响应进行建模。 实现 SCIM 终结点后，可以按照上一部分中所述的过程对其进行测试。

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
* 可在其上查询资源的属性应设置为[Azure 门户](https://portal.azure.com)的应用程序上的匹配属性。 有关详细信息，请参阅[自定义用户预配属性映射](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>用户预配和取消预配

下图显示了 Azure Active Directory 发送到 SCIM 服务的消息，以管理应用程序的标识存储中用户的生命周期。  

![显示用户预配和取消预配序列][4]<br/>
*图4：用户预配和取消预配顺序*

### <a name="group-provisioning-and-de-provisioning"></a>组预配和取消预配

组预配和取消设置是可选的。 实现并启用后，下图显示 Azure AD 发送到 SCIM 服务的消息，以管理应用程序的标识存储中某个组的生命周期。  这些消息在以下两个方面与用户消息不同：

* 检索组的请求指定要从为响应请求而提供的任何资源中排除成员属性。  
* 确定引用属性是否具有特定值的请求是有关成员属性的请求。  

![显示组预配和取消预配顺序][5]<br/>
*图5：组预配和取消预配顺序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 协议请求和响应
本部分提供 Azure AD SCIM 客户端发出的示例 SCIM 请求以及预期的响应。 为了获得最佳结果，应将应用编码为按此格式处理这些请求，并发出预期的响应。

> [!IMPORTANT]
> 要了解 Azure AD 用户预配服务如何以及何时发出下面所述的操作，请参阅[用户预配期间会发生什么情况？](user-provisioning.md#what-happens-during-provisioning)

- [用户操作](#user-operations)
  - [创建用户](#create-user)
    - [请求](#request)
    - [响应](#response)
  - [获取用户](#get-user)
    - [请求](#request-1)
    - [响应](#response-1)
  - [按查询获取用户](#get-user-by-query)
    - [请求](#request-2)
    - [响应](#response-2)
  - [按查询获取用户-零个结果](#get-user-by-query---zero-results)
    - [请求](#request-3)
    - [响应](#response-3)
  - [更新用户 [多值属性]](#update-user-multi-valued-properties)
    - [请求](#request-4)
    - [响应](#response-4)
  - [更新用户 [单值属性]](#update-user-single-valued-properties)
    - [请求](#request-5)
    - [响应](#response-5)
  - [删除用户](#delete-user)
    - [请求](#request-6)
    - [响应](#response-6)
- [组操作](#group-operations)
  - [创建组](#create-group)
    - [请求](#request-7)
    - [响应](#response-7)
  - [获取组](#get-group)
    - [请求](#request-8)
    - [响应](#response-8)
  - [通过 displayName 获取组](#get-group-by-displayname)
    - [请求](#request-9)
    - [响应](#response-9)
  - [更新组 [非成员属性]](#update-group-non-member-attributes)
    - [请求](#request-10)
    - [响应](#response-10)
  - [更新组 [添加成员]](#update-group-add-members)
    - [请求](#request-11)
    - [响应](#response-11)
  - [更新组 [删除成员]](#update-group-remove-members)
    - [请求](#request-12)
    - [响应](#response-12)
  - [删除组](#delete-group)
    - [请求](#request-13)
    - [响应](#response-13)

### <a name="user-operations"></a>用户操作

* 用户可以通过 `userName` 或 `email[type eq "work"]` 属性进行查询。  

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

##### <a name="request-2"></a>需要

*GET/Users？ filter = userName eq "Test_User_dfeef4c5-5681-4387-b016"*

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

#### <a name="delete-user"></a>删除用户

##### <a name="request-6"></a>需要

*删除/Users/5171a35d82074e068ce2 HTTP/1。1*

##### <a name="response-6"></a>回复

*HTTP/1.1 204 无内容*

### <a name="group-operations"></a>组操作

* 组应始终使用空成员列表创建。
* `displayName` 属性可以查询组。
* 更新到组修补请求应在响应中生成*HTTP 204 无内容*。 不建议使用所有成员列表返回正文。
* 不需要支持返回组的所有成员。

#### <a name="create-group"></a>创建组

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

#### <a name="delete-group"></a>删除组

##### <a name="request-13"></a>需要

*删除/Groups/cdb1ce18f65944079d37 HTTP/1。1*

##### <a name="response-13"></a>回复

*HTTP/1.1 204 无内容*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>使用 Microsoft CLI 库生成 SCIM 终结点

通过创建与 Azure Active Directory 进行交互的 SCIM web 服务，你可以为几乎任何应用程序或标识存储启用自动用户预配。

工作方式如下：

1. Azure AD 提供了一个名为 Microsoft.systemforcrossdomainidentitymanagement 的公共语言基础结构（CLI）库，其中包含下面的代码示例。 系统集成商和开发人员可以使用此库来创建和部署基于 SCIM 的 web 服务终结点，该终结点可将 Azure AD 连接到任何应用程序的标识存储。
2. 将在 Web 服务中实现映射，以将标准化用户架构映射到用户架构和应用程序所需的协议。 
3. 终结点 URL 在 Azure AD 中注册为应用程序库中自定义应用程序的一部分。
4. 用户和组在 Azure AD 中分配到此应用程序。 分配后，它们将被放入队列中，以同步到目标应用程序。 处理队列的同步过程每隔 40 分钟运行一次。

### <a name="code-samples"></a>代码示例

为了简化此过程，我们提供了[代码示例](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)，用于创建 SCIM web 服务终结点并演示自动预配。 该示例是一个提供程序，它维护一个文件，其中包含以逗号分隔的行表示用户和组的值。

**先决条件**

* Visual Studio 2013 或更高版本
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* 支持将 ASP.NET Framework 4.5 用作 SCIM 终结点的 Windows 计算机。 必须能够从云访问此计算机。
* [具有 Azure AD Premium 试用版或许可版的 Azure 订阅](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>入门

实现可以接受来自 Azure AD 的预配请求的 SCIM 终结点的最简单方法是构建和部署将预配的用户输出逗号分隔值 (CSV) 文件的代码示例。

#### <a name="to-create-a-sample-scim-endpoint"></a>创建示例 SCIM 终结点

1. 从 [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) 下载代码示例包
1. 将包解压缩，并将其放在 Windows 计算机上的某个位置，例如 C:\AzureAD-BYOA-Provisioning-Samples\。
1. 在此文件夹中，启动 Visual Studio 中的 FileProvisioning\Host\FileProvisioningService.csproj 项目。
1.  > **NuGet 包管理器**" > **包管理器控制台**中选择"**工具**"，然后对 FileProvisioningService 项目执行以下命令以解析解决方案引用：

   ```powershell
    Update-Package -Reinstall
   ```

1. 构建 FileProvisioningService 项目。
1. 在 Windows 中启动命令提示符应用程序（以管理员身分），并使用 cd 命令将目录切换到 \AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug 文件夹。
1. 运行以下命令，将 `<ip-address>` 替换为 Windows 计算机的 IP 地址或域名：

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. 在 windows " **Windows 设置**" 下的 " > **网络 & Internet 设置**" 下，选择 " **Windows 防火墙**" > "**高级" 设置**，并创建允许入站访问端口9000的**入站规则**。
1. 如果 Windows 计算机位于路由器后面，则需要将路由器配置为在向 internet 公开的端口9000和 Windows 计算机上的端口9000之间运行网络访问转换。 此配置是 Azure AD 在云中访问此终结点所必需的。

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>在 Azure AD 中注册示例 SCIM 终结点

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com)。 
1. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择 " **+ 新建应用程序** > **所有** > **的非库应用程序**。
1. 输入应用程序的名称，然后选择 "**添加**" 以创建应用对象。 创建的应用程序对象代表要预配和实现单一登录的目标应用，而不仅是 SCIM 终结点。
1. 在应用管理屏幕的左侧面板中，选择 "**预配**"。
1. 在“预配模式”菜单中，选择“自动”。    
1. 在“租户 URL”字段中，输入应用程序的 SCIM 终结点的 URL。 示例： https://api.contoso.com/scim/

1. 如果 SCIM 终结点需要来自非 Azure AD 颁发者的 OAuth 持有者令牌，可将所需的 OAuth 持有者令牌复制到可选的“密钥令牌”字段。 如果此字段保留为空，则 Azure AD 包括每个请求 Azure AD 颁发的 OAuth 持有者令牌。 将 Azure AD 用作标识提供者的应用可以验证 Azure AD 颁发的此令牌。
1. 选择 "**测试连接**"，Azure Active Directory 尝试连接到 SCIM 终结点。 如果尝试失败，将显示错误消息。  

    > [!NOTE]
    > **测试连接**使用随机 GUID 作为在 Azure AD 配置中选择的匹配属性，针对不存在的用户查询 SCIM 终结点。 预期正确响应为“HTTP 200 正常”以及空的 SCIM ListResponse 消息

1. 如果尝试连接到应用程序成功，请选择 "**保存**" 以保存管理员凭据。
1. 在“映射”部分中有两个可选的属性映射集：一个用于用户对象，一个用于组对象。 分别选择它们，查看从 Azure Active Directory 同步到应用的属性。 选为“匹配”属性的特性用于匹配应用中的用户和组，以执行更新操作。 选择“保存”，提交所有更改。
1. “设置”下的“作用域”字段定义同步的用户或组。 选择 **"仅同步分配的用户和组**（推荐）"，以便仅同步 "**用户和组**" 选项卡中分配的用户和组。
1. 配置完成后，将设置**状态**设置为 **"开**"。
1. 选择 "**保存**" 以启动 Azure AD 预配服务。
1. 如果仅同步分配的用户和组（推荐），请确保选择 "**用户和组**" 选项卡并分配要同步的用户或组。

初始周期开始后，可以在左侧面板中选择 "**审核日志**" 来监视进度，其中显示了预配服务对应用执行的所有操作。 若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](check-status-user-account-provisioning.md)。

验证此示例的最后一步是打开 Windows 计算机上 \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug 文件夹中的 TargetFile.csv 文件。 运行预配过程后，此文件会显示所有已分配和预配的用户与组的详细信息。

### <a name="development-libraries"></a>开发库

若要开发自己的符合 SCIM 规范的 Web 服务，请先熟悉 Microsoft 提供的、有助于加速开发过程的以下库：

* 提供公共语言基础结构 (CLI) 库以配合基于该基础结构的语言，例如 C#。 其中一个库 Microsoft.systemforcrossdomainidentitymanagement 声明了一个接口每 microsoft.systemforcrossdomainidentitymanagement.iprovider.startupbehavior，如下图中所示：。 使用这些库的开发人员将对某个类（一般称为提供程序）实现该接口。 这些库允许开发人员部署符合 SCIM 规范的 web 服务。 Web 服务可以位于 Internet Information Services 或任何可执行 CLI 程序集中。 请求将转换为对提供程序方法的调用，这些方法由开发者编程，以便对某些标识存储执行操作。
  
   ![细目：请求转换为对提供程序方法的调用][3]
  
* [快速路由处理程序](https://expressjs.com/guide/routing.html)用于分析代表对 node.js Web 服务的调用（由 SCIM 规范定义）的 node.js 请求对象。

### <a name="building-a-custom-scim-endpoint"></a>构建自定义 SCIM 终结点

使用 CLI 库的开发人员可以将其服务托管在任何可执行的 CLI 程序集中，或在 Internet Information Services 中。 以下代码示例用于将服务托管在地址为 http://localhost:9000: 的可执行程序集中： 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

此服务必须具有 HTTP 地址，其服务器身份验证证书的根证书颁发机构是下列名称之一： 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

可以使用网络 shell 实用程序将服务器身份验证证书绑定到 Windows 主机上的某个端口： 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

此处，为 certhash 参数提供的值为证书指纹，为 appid 参数提供的值为任意全局唯一标识符。  

若要在 Internet Information Services 中承载服务，开发人员需要在程序集的默认命名空间中使用名为 Startup 的类生成 CLI 代码库程序集。  以下是这种类的示例： 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>处理终结点身份验证

来自 Azure Active Directory 的请求包括 OAuth 2.0 持有者令牌。   接收请求的任何服务应对颁发者进行身份验证，使其能够 Azure Active Directory 用于预期的 Azure Active Directory 租户，以便访问 Azure Active Directory Graph web 服务。  在令牌中，颁发者由 iss 声明标识，如 "iss"： "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ "。  在此示例中，声明值的基址 https://sts.windows.net 将 Azure Active Directory 标识为颁发者，而相对地址段 cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 是 Azure Active Directory 租户的唯一标识符，已颁发令牌。 令牌的受众将是库中应用的应用程序模板 ID。 所有自定义应用的应用程序模板 ID 都是8adf8e6e-67b2-4cf2-a259-e3dc5476c621。 库中每个应用的应用程序模板 ID 各不相同。 有关库应用程序的应用程序模板 ID 的问题，请联系 ProvisioningFeedback@microsoft.com。 在单个租户中注册的每个应用程序可能会收到与 SCIM 请求相同的 `iss` 声明。

使用 Microsoft 提供的 CLI 库生成 SCIM 服务的开发人员可以通过执行以下步骤，使用 Owin 包对来自 Azure Active Directory 的请求进行身份验证： 

1. 在提供程序中，通过每次启动服务时让服务返回要调用的方法来实现 Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior 属性： 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

1. 将以下代码添加到该方法，以对任何服务的终结点发出请求，该服务的任何终结点均已通过 Azure Active Directory 为指定租户颁发的令牌进行身份验证，以便访问 Azure AD Graph web 服务： 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>处理用户的预配和取消预配

1. Azure Active Directory 会在服务中查询是否有某个用户的 externalId 属性值与 Azure AD 中用户的 mailNickname 属性值匹配。 查询表示为超文本传输协议（HTTP）请求（如本示例），其中 jyoung 是 Azure Active Directory 中用户的 mailNickname 的示例。

    >[!NOTE]
    > 这只是一个示例。 并非所有用户都具有 mailNickname 属性，并且用户在目录中的值可能不唯一。 此外，用于匹配（在本例中为 externalId）的属性可在[Azure AD 属性映射](customize-application-attributes.md)中进行配置。

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   如果服务是使用 Microsoft 提供的用于实现 SCIM 服务的 CLI 库生成的，则会将该请求转换为对服务提供程序的查询方法的调用。  以下是该方法的签名： 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   以下是 Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 接口的定义： 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   如果使用 Microsoft 提供的、用于实现 SCIM 服务的公共语言基础结构库构建了服务，则将请求转换为对服务提供者的 Query 方法调用。  以下是该方法的签名： 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   以下是 Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 接口的定义： 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   在查询具有给定 externalId 属性值的用户的下列示例中，传递给 Query 方法的参数值是： 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

1. 如果对 externalId 属性值与用户的 mailNickname 属性值相匹配的用户对 web 服务的查询的响应未返回任何用户，则 Azure Active Directory 请求服务设置与该用户相对应的用户在 Azure Active Directory。  以下是此类请求的示例： 

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
   Microsoft 提供的用于实现 SCIM 服务的 CLI 库将该请求转换为对服务提供者的 Create 方法的调用。  Create 方法具有此签名： 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   如果请求预配用户，资源参数的值将是 Microsoft.SystemForCrossDomainIdentityManagement 的实例。 Core2EnterpriseUser 类，在 Microsoft.SystemForCrossDomainIdentityManagement.Schemas 库中定义。  如果预配用户的请求成功，则方法的实现应返回 Microsoft.SystemForCrossDomainIdentityManagement 的实例。 Core2EnterpriseUser 类，其 Identifier 属性值设置为新预配用户的唯一标识符。  

1. 为了更新存在于前端为 SCIM 的标识存储中的已知用户，Azure Active Directory 将通过类似于下面的请求向服务请求该用户的当前状态来继续处理： 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   在使用 Microsoft 提供的、用于实现 SCIM 服务的 CLI 库生成的服务中，请求将转换为对服务提供者的检索方法的调用。  以下是 Retrieve 方法的签名： 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   在检索用户当前状态的请求示例中，作为参数自变量值提供的对象属性值如下所示： 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. 如果要更新引用属性，Azure Active Directory 会查询服务，以确定服务前端的标识存储中的引用属性的当前值是否已经与 Azure Active 中该属性的值相匹配文件夹. 对于用户，以这种方式查询当前值的唯一属性是 manager 属性。 确定特定用户对象的 manager 属性当前是否具有特定值的请求示例如下： 

   如果服务是使用 Microsoft 提供的用于实现 SCIM 服务的 CLI 库生成的，则会将该请求转换为对服务提供程序的查询方法的调用。 作为参数自变量值提供的对像属性值如下： 
  
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

1. 以下是从 Azure Active Directory 向 SCIM 服务发出更新用户请求的示例： 
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
   用于实现 SCIM 服务的 Microsoft CLI 库将请求转换为对服务提供程序的更新方法的调用。 以下是 Update 方法的签名： 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   如果使用 Microsoft 提供的、用于实现 SCIM 服务的公共语言基础结构库构建了服务，则将请求转换为对服务提供者的 Query 方法调用。 作为参数自变量值提供的对像属性值如下： 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.Parameters.alternatefilter.elementat. .Value.elementat （x）.Comparisonvalue： "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.Parameters.alternatefilter.elementat. .Value.elementat （y）.Comparisonvalue： "2819c223-7f76-453a-919d-7f76-453a-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  此处，索引 x 的值可以是0，并且索引 y 的值可以是1，或者 x 的值可以是1，而 y 的值可以是0，具体取决于筛选器查询参数表达式的顺序。   

1. 以下是从 Azure Active Directory 向 SCIM 服务发出更新用户请求的示例： 

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

   用于实现 SCIM 服务的 Microsoft 通用语言基础结构库将请求转换为对服务提供者的 Update 方法调用。 以下是 Update 方法的签名： 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
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

1. 若要从 SCIM 服务面对的标识存储中取消预配用户，Azure AD 会发送如下请求： 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   如果使用 Microsoft 提供的、用于实现 SCIM 服务的公共语言基础结构库构建了服务，则将请求转换为对服务提供者的 Delete 方法调用。   该方法具有以下签名： 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   在取消预配用户的请求示例中，作为 resourceIdentifier 参数值提供的对象具有以下属性值： 

1. 若要从 SCIM 服务面对的标识存储中取消预配用户，Azure AD 会发送如下请求： 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   如果服务是使用 Microsoft 提供的用于实现 SCIM 服务的 CLI 库生成的，则会将该请求转换为对服务提供者的 Delete 方法的调用。   该方法具有以下签名： 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   在取消预配用户的请求示例中，作为 resourceIdentifier 参数值提供的对象具有以下属性值： 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>用户和组架构引用

Azure Active Directory 可将两种类型的资源预配到 SCIM Web 服务。  这些类型的资源是用户和组。  

用户资源由以下协议规范中包含的架构标识符 `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`确定： https://tools.ietf.org/html/rfc7643 。  表1提供了 Azure Active Directory 中用户的属性的默认映射。  

组资源由架构标识符 `urn:ietf:params:scim:schemas:core:2.0:Group` 予以标识。 表2显示 Azure Active Directory 中组的属性到组资源的属性的默认映射。  

### <a name="table-1-default-user-attribute-mapping"></a>表 1：默认用户属性映射

| Azure Active Directory 用户 | “urn:ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |活动 |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>表 2：默认组属性映射

| Azure Active Directory 组 | urn： ietf： params： scim：架构： core：2.0：组 |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |ID |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>允许 Azure AD 预配服务使用的 IP 地址发出 SCIM 请求

某些应用允许入站流量发送到应用。 为了使 Azure AD 预配服务能够按预期运行，必须允许使用的 IP 地址。 有关每个服务标记/区域的 IP 地址列表，请参阅 JSON 文件- [AZURE IP 范围和服务标记–公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 你可以根据需要将这些 Ip 下载并编程到防火墙中。 可以在 "AzureActiveDirectoryDomainServices" 下找到 Azure AD 预配的保留 IP 范围。

## <a name="related-articles"></a>相关文章

* [在 SaaS 应用中自动预配和取消预配用户](user-provisioning.md)
* [为用户预配自定义属性映射](customize-application-attributes.md)
* [为属性映射编写表达式](functions-for-customizing-application-data.md)
* [用于用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帐户预配通知](user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
