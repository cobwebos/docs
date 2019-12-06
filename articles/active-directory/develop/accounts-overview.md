---
title: Microsoft 标识平台帐户和租户配置文件（Android） |Microsoft
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
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9af7d8c5a1793b34dd609c2cfd68fb468884ef8f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845716"
---
# <a name="accounts--tenant-profiles-android"></a>帐户和租户配置文件 (Android)

本文概述了 Microsoft 标识平台中的 `account`。

Microsoft 身份验证库（MSAL） API 将*用户*替换为术语 "*帐户*"。 原因之一是用户（人或软件代理）可能具有多个帐户，或者可以使用多个帐户。 这些帐户可以在用户自己的组织中，也可以位于用户所属的其他组织中。

Microsoft 标识平台中的帐户包括：

- 一个唯一标识符。  
- 用于演示帐户所有权/控制的一个或多个凭据。
- 一个或多个配置文件，包括以下属性：
  - 图片，名称，系列名称，职务，办公室位置
- 帐户有一个或一条记录的源。 这是在其中创建帐户的系统，以及与该帐户关联的凭据的存储位置。 在多租户系统（如 Microsoft 标识平台）中，记录的系统是在其中创建帐户的 `tenant`。 此租户也称为 `home tenant`。
- Microsoft 标识平台中的帐户具有以下记录系统：
  - Azure Active Directory，包括 Azure Active Directory B2C。
  - Microsoft 帐户（Live）。
- Microsoft 标识平台之外的记录系统中的帐户在 Microsoft 标识平台中表示，包括：
  - 连接的本地目录的标识（Windows Server Active Directory）
  - 来自 LinkedIn、GitHub 等的外部标识。
  在这些情况下，帐户在 Microsoft 标识平台中有源系统记录和记录系统。
- Microsoft 标识平台允许使用一个帐户来访问属于多个组织（Azure Active Directory 租户）的资源。
  - 若要记录某个系统记录（AAD 租户 A）的帐户有权访问另一个记录系统中的资源（AAD 租户 B），该帐户必须在定义该资源的租户中表示。 这是通过从系统 B 中的系统 A 创建帐户的本地记录来完成的。
  - 此本地记录（这是帐户的表示形式）绑定到原始帐户。
  - MSAL 公开此本地记录作为 `Tenant Profile`。
  - 租户配置文件可以具有适用于本地上下文的不同属性，例如职务、办公室位置、联系信息等。
- 由于一个帐户可能出现在一个或多个租户中，因此一个帐户可以有多个配置文件。

> [!NOTE]
> MSAL 将 Microsoft 帐户系统（实时、MSA）视为 Microsoft 标识平台中的其他租户。 Microsoft 帐户租户的租户 id 是： `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>帐户概述关系图

![帐户概述关系图](./media/accounts-overview/accounts-overview.png)

在上图中：

- 帐户 `bob@contoso.com` 是在本地 Windows Server Active Directory （源本地系统记录）中创建的。
- `tom@live.com` 在 Microsoft 帐户租户中创建帐户。
- `bob@contoso.com` 有权访问以下 Azure Active Directory 租户中的至少一个资源：
  - contoso.com （记录的云系统-链接到本地系统记录）
  - fabrikam.com
  - woodgrovebank.com
  - 每个租户中都存在 `bob@contoso.com` 的租户配置文件。
- `tom@live.com` 可以访问以下 Microsoft 租户中的资源：
  - contoso.com
  - fabrikam.com
  - 每个租户中都存在 `tom@live.com` 的租户配置文件。
- 有关其他租户中的 Tom 和 Bob 的信息可能与记录系统中的不同。 它们可能会不同，如职务、办公室位置等。 它们可能是每个组织（Azure Active Directory 租户）中的组和/或角色的成员。 我们将此信息称为 bob@contoso.com 租户配置文件。

在关系图中，bob@contoso.com 和 tom@live.com 有权访问不同 Azure Active Directory 租户中的资源。 有关详细信息，请参阅[在 Azure 门户中添加 AZURE ACTIVE DIRECTORY B2B 协作用户](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

## <a name="accounts-and-single-sign-on-sso"></a>帐户和单一登录（SSO）

MSAL 令牌缓存为每个帐户存储一个*刷新令牌*。 此刷新令牌可用于以无提示方式从多个 Microsoft 标识平台租户请求访问令牌。 在设备上安装 broker 后，该帐户将由代理进行管理，并且可能会发生设备范围的单一登录。

> [!IMPORTANT]
> 企业对消费者（B2C）帐户和刷新令牌行为与其他 Microsoft 标识平台不同。 有关详细信息，请参阅[B2C 策略 & 帐户](#b2c-policies--accounts)。

## <a name="account-identifiers"></a>帐户标识符

MSAL 帐户 ID 不是帐户对象 ID。 这并不是为了在 Microsoft 标识平台中传达除唯一性之外的任何内容。

为了与 Azure AD 身份验证库（ADAL）兼容，若要简化从 ADAL 到 MSAL 的迁移，MSAL 可以使用 MSAL 缓存中提供的帐户的任何有效标识符查找帐户。  例如，以下内容将始终检索 tom@live.com 的相同帐户对象，因为每个标识符都是有效的：

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>访问有关帐户的声明

除了请求访问令牌外，MSAL 还始终从每个租户请求一个 ID 令牌。 它通过总是请求以下范围来实现此操作：

- openid
- profile

ID 令牌包含声明的列表。 `Claims` 是有关帐户的名称/值对，用于发出请求。

如前所述，帐户所在的每个租户都可能存储有关该帐户的不同信息，包括但不限于以下属性：职务、办公室位置等。

尽管某个帐户可以是多个组织中的成员或来宾，但 MSAL 不会查询服务来获取该帐户是其成员的租户的列表。 相反，MSAL 会生成帐户所在的租户列表，这是由于发出的令牌请求引起的。

帐户对象上公开的声明始终是帐户的 "home 租户"/{authority} 的声明。 如果该帐户尚未用于请求其主租户的令牌，则 MSAL 无法通过 account 对象提供声明。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 若要查看 account 对象中提供的声明列表，请参阅[id_token 中的声明](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> 若要在 id_token 中包括其他声明，请参阅[如何：向 Azure AD 应用提供可选声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)中的可选声明文档

### <a name="access-tenant-profile-claims"></a>访问租户配置文件声明

若要访问其他租户中的帐户的声明，首先需要将帐户对象强制转换为 `IMultiTenantAccount`。 所有帐户都可以是多租户，但通过 MSAL 提供的租户配置文件的数量基于你使用当前帐户请求令牌的租户。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C 策略 & 帐户

刷新帐户的令牌不会跨 B2C 策略共享。 因此，无法使用令牌进行单一登录。 这并不意味着无法进行单一登录。 这意味着单一登录必须使用一个交互式体验，其中 cookie 可用于启用单一登录。

这也意味着，对于 MSAL，如果你使用不同的 B2C 策略获取令牌，则会将它们视为单独的帐户-每个帐户都有其自己的标识符。 如果要使用 `acquireTokenSilent`来请求令牌，则需要从帐户列表中选择帐户，该帐户与用于令牌请求的策略相匹配。 例如：

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
