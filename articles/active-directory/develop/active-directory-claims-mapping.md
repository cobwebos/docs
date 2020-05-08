---
title: 自定义 Azure AD 租户应用声明 (PowerShell)
titleSuffix: Microsoft identity platform
description: 本页介绍 Azure Active Directory 声明映射。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: d8be2c8cc70db963252054a39cad558c4c1b5bd2
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871218"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>如何：为租户中的特定应用自定义在令牌中发出的声明（预览版）

> [!NOTE]
> 此功能替换并取代了当前通过门户提供的[声明自定义](active-directory-saml-claims-customization.md)。 在同一应用程序中，如果使用门户以及本文档中详细介绍的 Graph/PowerShell 方法自定义声明，则为该应用程序颁发的令牌会忽略门户中的配置。 通过本文档中详细介绍的方法进行的配置不会在门户中进行反映。

此功能由租户管理员用来自定义以令牌形式针对其租户中的特定应用程序发出的声明。 可以使用声明映射策略执行以下操作：

- 选择在令牌中包含的声明。
- 创建尚未存在的声明类型。
- 选择或更改在特定声明中发出的数据的源。

> [!NOTE]
> 此功能目前以公共预览版提供。 应准备好还原或删除所做的任何更改。 在公共预览版推出期间，可在任何 Azure Active Directory (Azure AD) 订阅中使用此功能。 但是，在正式版推出后，某些功能可能需要使用 Azure AD Premium 订阅。 此功能支持配置适用于 WS-Fed、SAML、OAuth 和 OpenID Connect 协议的声明映射策略。

## <a name="claims-mapping-policy-type"></a>声明映射策略类型

在 Azure AD 中，**策略**对象表示针对组织中的单个应用程序或所有应用程序强制实施的一组规则。 每种类型的策略都有一个唯一的结构，其中的一组属性将应用于它们所分配到的对象。

声明映射策略是某种类型的**策略**对象，它修改为特定应用程序颁发的令牌中发出的声明。

## <a name="claim-sets"></a>声明集

有一些可定义如何以及何时在令牌中使用它们的特定声明集。

| 声明集 | 说明 |
|---|---|
| 核心声明集 | 存在于每个令牌中，与策略无关。 这些声明也被视为受限制的，无法修改。 |
| 基本声明集 | 包括默认情况下为令牌发出的声明（除了核心声明集之外）。 可以省略或通过使用声明映射策略来修改基本声明。 |
| 受限声明集 | 无法使用策略进行修改。 无法更改数据源，并且在生成这些声明时不应用任何转换。 |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>表 1：JSON Web 令牌 (JWT) 受限制声明集

| 声明类型（名称） |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| 电子邮件 |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| 角色 |
| scope |
| scp |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>表 2：SAML 受限制声明集

| 声明类型 (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>声明映射策略属性

若要控制要发出的声明以及数据的来源，请使用声明映射策略的属性。 如果未设置策略，则系统将颁发包括核心声明集、基本声明集以及应用程序已选择接收的任何[可选声明](active-directory-optional-claims.md)的令牌。

### <a name="include-basic-claim-set"></a>包括基本声明集

**字符串：** IncludeBasicClaimSet

**数据类型：** 布尔值（True 或 False）

**摘要：** 此属性确定是否在受此策略影响的令牌中包含基本声明集。

- 如果设置为 True，则会在受策略影响的令牌中发出基本声明集中的所有声明。 
- 如果设置为 False，基本声明集中的声明不包含在令牌中，除非在相同策略的声明架构属性中单独添加它们。

> [!NOTE] 
> 核心声明集中的声明存在于每个令牌中（与此属性的设置无关）。 

### <a name="claims-schema"></a>声明架构

**字符串：** ClaimsSchema

**数据类型：** 具有一个或多个声明架构条目的 JSON Blob

**摘要：** 此属性定义除了基本声明集与核心声明集之外，在受此策略影响的令牌中存在的声明。
对于此属性中定义的每个声明架构条目，都需要特定信息。 指定数据来源（**Value** 或 **Source/ID 对**）以及数据作为哪种声明发出（**声明类型**）。

### <a name="claim-schema-entry-elements"></a>声明架构条目元素

**Value：** Value 元素将静态值定义为要在声明中发出的数据。

**Source/ID 对：** Source 和 ID 元素定义声明中的数据的来源。 

将 Source 元素设置为下列值之一： 

- "user"：声明中的数据是 User 对象的属性。 
- "application"：声明中的数据是应用程序（客户端）服务主体的属性。 
- "resource"：声明中的数据是资源服务主体的属性。
- "audience"：声明中的数据是作为令牌受众的服务主体（客户端或资源服务主体）的属性。
- "company"：声明中的数据是资源租户的 Company 对象的属性。
- "transformation"：声明中的数据来自声明转换（请参阅本文后面的“声明转换”部分）。

如果 Source 是 transformation，则 **TransformationID** 元素也必须包含在此声明定义中。

ID 元素标识源中用于为声明提供值的属性。 下表列出对 Source 的每个值有效的 ID 值。

#### <a name="table-3-valid-id-values-per-source"></a>表 3：每个 Source 的有效 ID 值

| Source | ID | 说明 |
|-----|-----|-----|
| User | surname | 家族名称 |
| User | givenname | 名 |
| User | displayname | 显示名称 |
| User | objectid | ObjectID |
| User | mail | 电子邮件地址 |
| User | userprincipalname | 用户主体名称 |
| User | department|Department|
| User | onpremisessamaccountname | 本地 SAM 帐户名称 |
| User | netbiosname| NetBios 名称 |
| User | dnsdomainname | DNS 域名 |
| User | onpremisesecurityidentifier | 本地安全标识符 |
| User | companyname| 组织名称 |
| User | streetaddress | 街道地址 |
| User | postalcode | 邮政编码 |
| User | preferredlanguange | 首选语言 |
| User | onpremisesuserprincipalname | 本地 UPN |
| User | mailNickname | 邮件别名 |
| User | extensionattribute1 | 扩展属性 1 |
| User | extensionattribute2 | 扩展属性 2 |
| User | extensionattribute3 | 扩展属性 3 |
| User | extensionattribute4 | 扩展属性 4 |
| User | extensionattribute5 | 扩展属性 5 |
| User | extensionattribute6 | 扩展属性 6 |
| User | extensionattribute7 | 扩展属性 7 |
| User | extensionattribute8 | 扩展属性 8 |
| User | extensionattribute9 | 扩展属性 9 |
| User | extensionattribute10 | 扩展属性 10 |
| User | extensionattribute11 | 扩展属性 11 |
| User | extensionattribute12 | 扩展属性 12 |
| User | extensionattribute13 | 扩展属性 13 |
| User | extensionattribute14 | 扩展属性 14 |
| User | extensionattribute15 | 扩展属性 15 |
| User | othermail | 其他邮件 |
| User | country | 国家/地区 |
| User | city | 城市 |
| User | state | 状态 |
| User | jobtitle | 职务 |
| User | employeeid | 员工 ID |
| User | facsimiletelephonenumber | 传真电话号码 |
| application、resource、audience | displayname | 显示名称 |
| application、resource、audience | objectid | ObjectID |
| application、resource、audience | 标记 | 服务主体标记 |
| 公司 | tenantcountry | 租户的国家/地区 |

**TransformationID：** 仅当 Source 元素设置为“transformation”时，才必须提供 TransformationID 元素。

- 此元素必须与 **ClaimsTransformation** 属性（定义如何生成此声明的数据）中的转换条目的 ID 元素匹配。

**声明类型：** **JwtClaimType** 和 **SamlClaimType** 元素定义此声明架构条目引用的声明。

- JwtClaimType 必须包含要在 JWT 中发出的声明的名称。
- SamlClaimType 必须包含要在 SAML 令牌中发出的声明的 URI。

> [!NOTE]
> 受限声明集中的声明的 Name 和 URI 不能用于声明类型元素。 有关详细信息，请参阅本文后面的“例外和限制”部分。

### <a name="claims-transformation"></a>声明转换

**字符串：** ClaimsTransformation

**数据类型：** 具有一个或多个转换条目的 JSON Blob 

**摘要：** 使用此属性可将常见转换应用于源数据，以便为声明架构中指定的声明生成输出数据。

**ID：** 使用 ID 元素可在 TransformationID 声明架构条目中引用此转换条目。 就此策略中的每个转换条目来说，该值必须唯一。

**TransformationMethod：** TransformationMethod 元素用于标识为生成声明的数据而执行的操作。

根据选择的方法，需要一组输入和输出。 使用 **InputClaims**、**InputParameters** 和**OutputClaims** 元素定义输入和输出。

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>表 4：转换方法以及预期输入和输出

|TransformationMethod|预期输入|预期输出|说明|
|-----|-----|-----|-----|
|联接|string1、string2、分隔符|outputClaim|联接输入字符串（之间使用分隔符）。 例如：string1：“foo@bar.com”、string2：“sandbox”、separator：“.”会生成 outputClaim：“foo@bar.com.sandbox”|
|ExtractMailPrefix|mail|outputClaim|提取电子邮件地址的本地部分。 例如：mail：“foo@bar.com”会生成 outputClaim：“foo”。 如果未提供 \@ 符号，则按原样返回原始输入字符串。|

**InputClaims：** 使用 InputClaims 元素可将数据从声明架构条目传递给转换。 它具有两个属性：**ClaimTypeReferenceId** 和 **TransformationClaimType**。

- **ClaimTypeReferenceId** 与声明架构条目的 ID 元素联接在一起可查找相应的输入声明。 
- **TransformationClaimType** 用于向此输入提供唯一名称。 此名称必须与转换方法的预期输入之一匹配。

**InputParameters：** 使用 InputParameters 元素可将常数值传递给转换。 它具有两个属性：**Value** 和 **ID**。

- **Value** 是要传递的实际常数值。
- **ID** 用于为输入提供唯一名称。 此名称必须与转换方法的预期输入之一匹配。

**OutputClaims：** 使用 OutputClaims 元素可保存转换生成的数据，并将它绑定到声明架构条目。 它具有两个属性：**ClaimTypeReferenceId** 和 **TransformationClaimType**。

- **ClaimTypeReferenceId** 与声明架构条目的 ID 联接在一起可查找相应的输出声明。
- **TransformationClaimType** 用于为输出提供唯一名称。 此名称必须与转换方法的预期输出之一匹配。

### <a name="exceptions-and-restrictions"></a>例外和限制

**SAML NameID 和 UPN：** NameID 和 UPN 值所源自的属性以及允许使用的声明转换会受到限制。 请参阅表 5 和表 6 来查看允许的值。

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>表 5：允许作为 SAML NameID 数据源的属性

|Source|ID|说明|
|-----|-----|-----|
| User | mail|电子邮件地址|
| User | userprincipalname|用户主体名称|
| User | onpremisessamaccountname|本地 Sam 帐户名称|
| User | employeeid|员工 ID|
| User | extensionattribute1 | 扩展属性 1 |
| User | extensionattribute2 | 扩展属性 2 |
| User | extensionattribute3 | 扩展属性 3 |
| User | extensionattribute4 | 扩展属性 4 |
| User | extensionattribute5 | 扩展属性 5 |
| User | extensionattribute6 | 扩展属性 6 |
| User | extensionattribute7 | 扩展属性 7 |
| User | extensionattribute8 | 扩展属性 8 |
| User | extensionattribute9 | 扩展属性 9 |
| User | extensionattribute10 | 扩展属性 10 |
| User | extensionattribute11 | 扩展属性 11 |
| User | extensionattribute12 | 扩展属性 12 |
| User | extensionattribute13 | 扩展属性 13 |
| User | extensionattribute14 | 扩展属性 14 |
| User | extensionattribute15 | 扩展属性 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>表 6：允许用于 SAML NameID 的转换方法

| TransformationMethod | 限制 |
| ----- | ----- |
| ExtractMailPrefix | 无 |
| Join | 要联接的后缀必须是资源租户的已验证域。 |

### <a name="custom-signing-key"></a>自定义签名密钥

必须为服务主体对象分配自定义签名密钥，否则声明映射策略无法生效。 这可以确保确认令牌是由声明映射策略的创建者修改的，并防止应用程序被恶意参与者创建的声明映射策略破坏。 若要添加自定义签名密钥，可以使用 Azure PowerShell cmdlet `new-azureadapplicationkeycredential` 为应用程序对象创建对称密钥凭据。 有关此 Azure PowerShell cmdlet 的详细信息，请参阅 [New-AzureADApplicationKeyCredential](https://docs.microsoft.com/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0)。

启用了声明映射的应用必须通过将 `appid={client_id}` 追加到其 [OpenID 连接元数据请求](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)来验证其令牌签名密钥。 下面是你应该使用的 OpenID 连接元数据文档的格式： 

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>跨租户方案

声明映射策略不适用于来宾用户。 如果来宾用户尝试访问向其服务主体分配了声明映射策略的应用程序，则会颁发默认令牌（策略不会生效）。

## <a name="claims-mapping-policy-assignment"></a>声明映射策略分配

声明映射策略只能分配给服务主体对象。

### <a name="example-claims-mapping-policies"></a>声明映射策略示例

在 Azure AD 中，在可以为特定服务主体自定义令牌中发出的声明时，可以实现许多方案。 在此部分中，我们会演练几个常见方案，它们可帮助你理解如何使用声明映射策略类型。

#### <a name="prerequisites"></a>先决条件

以下示例将创建、更新、链接和删除服务主体的策略。 如果你是 Azure AD 新手，我们建议在继续学习这些示例之前，先[了解如何获取 Azure AD 租户](quickstart-create-new-tenant.md)。

若要开始，请执行以下步骤：

1. 首先请下载最新的 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 运行 Connect 命令，登录到 Azure AD 管理员帐户。 每次启动新会话都需要运行此命令。

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 若要查看组织中创建的所有策略，请运行以下命令。 在以下方案中，建议在大多数操作之后运行此命令，以检查是否按预期创建策略。

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>示例：创建并分配一个策略，以从颁发给服务主体的令牌中省略基本声明

在此示例中创建一个策略，它会从颁发给链接的服务主体的令牌中删除基本声明集。

1. 创建声明映射策略。 此策略（链接到特定服务主体）会从令牌中删除基本声明集。
   1. 若要创建该策略，请运行以下命令： 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。
   1. 若要查看组织的所有服务主体，可以[查询 MICROSOFT GRAPH API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>示例：创建并分配一个策略，以将 EmployeeID 和 TenantCountry 作为声明包含在颁发给服务主体的令牌中

在此示例中创建一个策略，它会向颁发给链接的服务主体的令牌添加 EmployeeID 和 TenantCountry。 EmployeeID 在 SAML 令牌和 JWT 中都作为名称声明类型发出。 TenantCountry 在 SAML 令牌和 JWT 中都作为国家/地区声明类型发出。 在此示例中，我们继续在令牌中包含基本声明集。

1. 创建声明映射策略。 此策略（链接到特定服务主体）向令牌添加 EmployeeID 和 TenantCountry 声明。
   1. 若要创建该策略，请运行以下命令：  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。 
   1. 若要查看组织的所有服务主体，可以[查询 MICROSOFT GRAPH API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>示例：创建并分配一个策略，以在颁发给服务主体的令牌中使用声明转换

在此示例中创建一个策略，它会向颁发给链接的服务主体的 JWT 发出自定义声明“JoinedData”。 此声明包含通过将用户对象的 extensionattribute1 属性中存储的数据与“.sandbox”联接所创建的值。 在此示例中，我们在令牌中排除基本声明集。

1. 创建声明映射策略。 此策略（链接到特定服务主体）向令牌添加 EmployeeID 和 TenantCountry 声明。
   1. 若要创建该策略，请运行以下命令：
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. 若要查看新策略并获取其 ObjectId，请运行以下命令： 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。 
   1. 若要查看组织的所有服务主体，可以[查询 MICROSOFT GRAPH API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令： 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>另请参阅

若要了解如何通过 Azure 门户自定义 SAML 令牌中颁发的声明，请参阅[如何：自定义用于企业应用程序的 saml 令牌中颁发的声明](active-directory-saml-claims-customization.md)
