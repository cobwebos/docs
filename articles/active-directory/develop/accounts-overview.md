---
title: Android 上的 Microsoft 标识平台帐户和租户配置文件 | Azure
description: 适用于 Android 的 Microsoft 标识平台帐户概述
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: 404ffbc09a69b623a421bd0c01550d72e5c03158
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115979"
---
# <a name="accounts--tenant-profiles-android"></a>帐户和租户配置文件 (Android)

本文概述 Microsoft 标识平台中的 `account`。

Microsoft 身份验证库 (MSAL) API 将术语“用户”替换成了术语“帐户”。   原因之一在于，一个用户（人或软件代理）可能具有或者使用多个帐户。 这些帐户可能位于用户自己的组织中，和/或位于用户所属的其他组织中。

Microsoft 标识平台中的帐户包括：

- 唯一标识符。  
- 用于演示帐户所有权/控制权的一个或多个凭据。
- 一个或多个配置文件，其中包括如下所述的属性：
  - 图片、名字、姓氏、职务、办公地点
- 帐户具有颁发机构源或记录系统。 帐户在此系统中创建，与该帐户关联的凭据在此系统中存储。 在多租户系统（例如 Microsoft 标识平台）中，记录系统是创建帐户的 `tenant`。 此租户也称为 `home tenant`。
- Microsoft 标识平台中的帐户具有以下记录系统：
  - Azure Active Directory，包括 Azure Active Directory B2C。
  - Microsoft 帐户 (Live)。
- Microsoft 标识平台外部的记录系统中的帐户将在 Microsoft 标识平台中表示，这些帐户包括：
  - 来自连接的本地目录 (Windows Server Active Directory) 的标识。
  - 来自 LinkedIn、GitHub 等的外部标识。
  在这种情况下，帐户同时具有来源记录系统和 Microsoft 标识平台中的记录系统。
- Microsoft 标识平台允许使用一个帐户访问属于多个组织（Azure Active Directory 租户）的资源。
  - 若要指出来自某个记录系统（AAD 租户 A）的帐户有权访问另一个记录系统（AAD 租户 B）中的资源，必须在定义该资源的租户中表示该帐户。 为此，可以在系统 B 中创建系统 A 中帐户的本地记录。
  - 此本地记录（帐户的表示形式）将绑定到原始帐户。
  - MSAL 将此本地记录公开为 `Tenant Profile`。
  - 租户配置文件可以包含适用于本地上下文的不同属性，例如职务、办公地点、联系信息等。
- 由于一个帐户可能存在于一个或多个租户中，因此一个帐户可以有多个配置文件。

> [!NOTE]
> MSAL 将 Microsoft 帐户系统（Live、MSA）视为 Microsoft 标识平台中的另一个租户。 Microsoft 帐户租户的租户 ID 为：`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>帐户概述示意图

![帐户概述示意图](./media/accounts-overview/accounts-overview.svg)

在上图中：

- 帐户 `bob@contoso.com` 在本地 Windows Server Active Directory（来源本地记录系统）中创建。
- 帐户 `tom@live.com` 在 Microsoft 帐户租户中创建。
- `bob@contoso.com` 有权访问以下 Azure Active Directory 租户中的至少一个资源：
  - contoso.com（云记录系统 - 已链接到本地记录系统）
  - fabrikam.com
  - woodgrovebank.com
  - `bob@contoso.com` 的租户配置文件在上述每个租户中存在。
- `tom@live.com` 有权访问以下 Microsoft 租户中的资源：
  - contoso.com
  - fabrikam.com
  - `tom@live.com` 的租户配置文件在上述每个租户中存在。
- 其他租户中有关 Tom 和 Bob 的信息可能与记录系统中的信息不同。 他们在职务、办公地点等属性方面可能不同。 他们可能是每个组织（Azure Active Directory 租户）中的组和/或角色的成员。 我们将此信息称为 bob@contoso.com 租户配置文件。

在示意图中，bob@contoso.com 和 tom@live.com 有权访问不同 Azure Active Directory 租户中的资源。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../external-identities/add-users-administrator.md)。

## <a name="accounts-and-single-sign-on-sso"></a>帐户和单一登录 (SSO)

MSAL 令牌缓存为每个帐户存储单个刷新令牌。** 该刷新令牌可用于以静默方式从多个 Microsoft 标识平台租户请求访问令牌。 在设备上安装中介后，帐户将由该中介管理，并可能会实现设备范围的单一登录。

> [!IMPORTANT]
> 企业到消费者 (B2C) 帐户和刷新令牌的行为与 Microsoft 标识平台中其他帐户和令牌不同。 有关详细信息，请参阅 [B2C 策略和帐户](#b2c-policies--accounts)。

## <a name="account-identifiers"></a>帐户标识符

MSAL 帐户 ID 并非帐户对象 ID。 帐户 ID 旨在用于分析，和/或传达相应帐户在 Microsoft 标识平台中具有唯一性这一概念，此外没有其他作用。

为了与 Azure AD 身份验证库 (ADAL) 兼容并简化从 ADAL 到 MSAL 的迁移，MSAL 可以使用 MSAL 缓存中提供的帐户的任何有效标识符查找帐户。  例如，以下代码始终检索 tom@live.com 的同一帐户对象，因为每个标识符都是有效的：

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>访问有关帐户的声明

除了请求访问令牌以外，MSAL 还始终从每个租户请求 ID 令牌。 为此，它始终请求以下范围：

- openid
- 个人资料

ID 令牌包含声明列表。 `Claims` 是有关帐户的名称/值对，用于发出请求。

如前所述，帐户所在的每个租户可以存储有关该帐户的不同信息，包括但不限于职务、办公地点等属性。

尽管一个帐户可以是多个组织中的成员或来宾，但 MSAL 不会查询服务来获取该帐户所属的租户列表， 而是根据发出的令牌请求的结果，构建帐户所在的租户列表。

在帐户对象中公开的声明始终是来自帐户的 'home tenant'/{authority} 的声明。 如果该帐户尚未用于请求其主租户的令牌，则 MSAL 无法通过帐户对象提供声明。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 若要查看帐户对象中提供的声明列表，请参阅 [id_token 中的声明](./id-tokens.md#claims-in-an-id_token)

> [!TIP]
> 若要在 id_token 中包含其他声明，请参阅可选声明文档：[如何：向 Azure AD 应用提供可选声明](./active-directory-optional-claims.md)

### <a name="access-tenant-profile-claims"></a>访问租户配置文件声明

若要访问显示在其他租户中的有关帐户的声明，首先需要将帐户对象强制转换为 `IMultiTenantAccount`。 所有帐户都可以是多租户帐户，但通过 MSAL 提供的租户配置文件数取决于使用当前帐户从哪些租户请求了令牌。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C 策略和帐户

帐户的刷新令牌不会在 B2C 策略之间共享。 因此，无法使用令牌进行单一登录。 但这并不意味着单一登录无法实现。 而是意味着，单一登录必须使用交互式体验，在其中可以通过 Cookie 实现单一登录。

这也意味着，对于 MSAL，如果使用不同的 B2C 策略获取令牌，则会将这些帐户视为独立的帐户 - 每个帐户具有自身的标识符。 若要使用某个帐户通过 `acquireTokenSilent` 请求令牌，需要从帐户列表中，选择与用于请求令牌的策略匹配的帐户。 例如：

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```