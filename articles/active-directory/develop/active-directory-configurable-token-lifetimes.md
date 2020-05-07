---
title: 可配置 Azure AD 令牌生存期
titleSuffix: Microsoft identity platform
description: 了解如何设置 Azure AD 颁发的令牌的生存期。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3e66cd6a05a7c616b22eefffdd9d132aa0f4d36d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853968"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Azure Active Directory 中可配置的令牌生存期（预览版）

可以指定 Azure Active Directory (Azure AD) 颁发的令牌的生存期。 可以针对组织中的所有应用、多租户（多组织）应用程序或者组织中的特定服务主体设置生存期。

> [!IMPORTANT]
> 在预览期间收到客户的来信后，我们实现了 Azure AD 条件性访问中的[身份验证会话管理功能](https://go.microsoft.com/fwlink/?linkid=2083106)。 可以使用此新功能，通过设置登录频率来配置刷新令牌生存期。 5月30日之后2020，任何新租户都无法使用可配置的令牌生存期策略来配置会话和刷新令牌。 弃用将在此之后的几个月内发生，这意味着我们将停止考虑现有会话和刷新令牌策略。 你仍可以在弃用后配置访问令牌生存期。

在 Azure AD 中，策略对象表示针对组织中的单个应用程序或所有应用程序强制实施的一组规则。 每种策略类型都有一个唯一的结构，其中的一组属性将应用于它们所分配到的对象。

可将某个策略指定为组织的默认策略。 该策略将应用到组织中的任何应用程序，只要此策略不被更高优先级的策略覆盖即可。 此外，还可以将策略分配到特定的应用程序。 优先顺序根据策略类型的不同而异。

> [!NOTE]
> 可配置的令牌生存期策略仅适用于访问 SharePoint Online 和 OneDrive for business 资源的移动和桌面客户端，不适用于 web 浏览器会话。
> 若要管理 SharePoint Online 和 OneDrive for business 的 web 浏览器会话的生存期，请使用[条件访问会话生存期](../conditional-access/howto-conditional-access-session-lifetime.md)功能。 了解有关配置空闲会话超时的详细信息，请参阅 [SharePoint Online 博客](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)。

## <a name="token-types"></a>令牌类型

可为刷新令牌、访问令牌、SAML 令牌、会话令牌和 ID 令牌设置令牌生存期策略。

### <a name="access-tokens"></a>访问令牌

客户端使用访问令牌来访问受保护的资源。 访问令牌仅可用于用户、客户端和资源的特定组合。 访问令牌不能吊销，在过期日期之前保持有效。 获取了访问令牌的恶意行动者在访问令牌的生存期内一直可以使用它。 调整访问令牌生存期的利弊是可以提高系统性能，但也会增加客户端在用户帐户禁用后保留访问权限的时间。 通过减少客户端获取刷新访问令牌的次数可以实现系统性能的提升。  默认值为 1 小时 - 在 1 小时后，客户端必须使用刷新令牌（通常以无提示方式）获取新的刷新令牌和访问令牌。 

### <a name="saml-tokens"></a>SAML 令牌

SAML 令牌由许多基于 web 的 SAAS 应用程序使用，并使用 Azure Active Directory 的 SAML2 协议终结点获得。 使用 WS 联合身份验证的应用程序也使用它们。 令牌的默认生存期为1小时。 从应用程序的角度来看，令牌的有效期由标记中`<conditions …>`元素的 NotOnOrAfter 值指定。 令牌的有效期结束后，客户端必须启动新的身份验证请求，此请求通常在未通过单一登录（SSO）会话令牌生成的交互式登录时得到满足。

可以使用中的`AccessTokenLifetime`参数更改 NotOnOrAfter 的值。 `TokenLifetimePolicy` 它将设置为策略中配置的生存期（如果有）加上5分钟的时钟偏差系数。

请注意，在`<SubjectConfirmationData>`元素中指定的主题确认 NotOnOrAfter 不受令牌生存期配置的影响。 

### <a name="refresh-tokens"></a>刷新令牌

当客户端获取访问令牌来访问受保护资源时，它同时还会收到一个刷新令牌。 当前访问令牌过期时，可以使用刷新令牌获取新的访问/刷新令牌对。 刷新令牌绑定到用户和客户端的组合。 刷新令牌可以[随时吊销](access-tokens.md#token-revocation)，每次使用该令牌时，系统都会检查其有效性。  使用刷新令牌获取新访问令牌时，不会撤销刷新令牌 - 但最佳做法是获取新令牌后安全删除旧令牌。 

必须在机密客户端与公共客户端之间做出区分，因为这会影响刷新令牌可以使用的时长。 有关不同类型的客户端的详细信息，请参阅 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>机密客户端刷新令牌的令牌生存期
机密客户端是能够安全存储客户端密码（机密）的应用程序。 它们可证实请求来自安全的客户端应用程序而不是来自恶意行动者。 例如，Web 应用是机密客户端，因为它可以在 Web 服务器上存储客户端机密， 因此不会公开。 由于这些流程更加安全，因此颁发给这些流程的刷新令牌的默认生存期为 `until-revoked`，无法使用策略更改，并且不会在自愿密码重置时撤销。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>公共客户端刷新令牌的令牌生存期

公共客户端无法安全地存储客户端密码（机密）。 例如，iOS/Android 应用不能对资源所有者的机密进行模糊处理，因此被视为公共客户端。 可以针对资源设置策略，防止使用公共客户端中超过指定期限的刷新令牌获取新的访问权限/刷新令牌对。 （为此，请使用 "刷新令牌最大非活动时间`MaxInactiveTime`" 属性（）。）你还可以使用策略设置一个时间段，超过该时间段后，刷新令牌不再被接受。 （为此，请使用 "刷新令牌最大期限" 属性。）可以调整刷新令牌的生存期，控制用户在使用公共客户端应用程序时需要重新输入凭据而不是以无提示方式重新进行身份验证的时间和频率。

> [!NOTE]
> "最大期限" 属性是可以使用单个令牌的时间长度。 

### <a name="id-tokens"></a>ID 令牌
ID 令牌将传递给网站和本机客户端。 ID 令牌包含有关用户的配置文件信息。 ID 令牌绑定到用户和客户端的特定组合。 在过期日期之前，ID 令牌保持有效。 通常，Web 应用程序会将应用程序中用户的会话生存期与针对该用户颁发的 ID 令牌的生存期进行匹配。 可以调整 ID 令牌的生存期，控制 Web 应用程序使应用程序会话过期的频率，以及要求用户在 Azure AD 上重新进行身份验证（以无提示方式或交互方式）的频率。

### <a name="single-sign-on-session-tokens"></a>单一登录会话令牌
当用户通过 Azure AD 进行身份验证时，系统将在用户的浏览器与 Azure AD 之间建立单一登录会话 (SSO)。 SSO 令牌采用 Cookie 形式，代表此会话。 SSO 会话令牌未绑定到特定的资源/客户端应用程序。 SSO 会话令牌可以吊销，每次使用它们时，系统都会检查其有效性。

Azure AD 使用两种 SSO 会话令牌：持久性和非持久性会话令牌。 浏览器将持久性会话令牌存储为持久性 Cookie， 将非持久性会话令牌存储为会话 Cookie。 （关闭浏览器后会销毁会话 cookie。）通常，存储非持久性会话令牌。 但如果用户在身份验证期间选择“使我保持登录状态”复选框，则存储的是持久性会话令牌****。

非持久性会话令牌的生存期为 24 小时。 持久性令牌的生存期为 180 天。 只要在其有效期内使用 SSO 会话令牌，有效期就会延长24小时或180天，具体取决于标记类型。 如果 SSO 会话令牌在其有效期内未被使用，则将它视为过期，不再被系统接受。

颁发第一个会话令牌后，可以使用策略来设置一个时间段，超过该时间段后，该会话令牌不再被接受。 （为此，请使用 "会话令牌最大期限" 属性。）可以调整会话令牌的生存期，控制用户在使用 web 应用程序时需要重新输入凭据而不是以无提示方式进行身份验证的时间和频率。

### <a name="token-lifetime-policy-properties"></a>令牌生存期策略属性
令牌生存期策略是一种策略对象，其中包含令牌生存期规则。 使用策略的属性控制指定的令牌生存期。 如果未设置策略，系统将强制实施默认生存期值。

### <a name="configurable-token-lifetime-properties"></a>可配置的令牌生存期属性
| properties | 策略属性字符串 | 影响 | 默认 | 最小值 | 最大值 |
| --- | --- | --- | --- | --- | --- |
| 访问令牌生存期 |AccessTokenLifetime<sup>2</sup> |访问令牌、ID 令牌、SAML2 令牌 |1 小时 |10 分钟 |1 天 |
| 刷新令牌最大非活动时间 |MaxInactiveTime |刷新令牌 |90 天 |10 分钟 |90 天 |
| 单因素刷新令牌最大期限 |MaxAgeSingleFactor |刷新令牌（适用于任何用户） |直到吊销 |10 分钟 |直到吊销<sup>1</sup> |
| 多因素刷新令牌最大期限 |MaxAgeMultiFactor |刷新令牌（适用于任何用户） |直到吊销 |10 分钟 |直到吊销<sup>1</sup> |
| 单因素会话令牌最大期限 |MaxAgeSessionSingleFactor |会话令牌（持久性和非持久性） |直到吊销 |10 分钟 |直到吊销<sup>1</sup> |
| 多因素会话令牌最大期限 |MaxAgeSessionMultiFactor |会话令牌（持久性和非持久性） |直到吊销 |10 分钟 |直到吊销<sup>1</sup> |

* <sup>1</sup>365 天是可针对这些属性设置的最大显式时间长短。
* <sup>2</sup>若要确保 Microsoft 团队 Web 客户端工作，建议将 AccessTokenLifetime 保留为大于15分钟的 Microsoft 团队。

### <a name="exceptions"></a>异常
| properties | 影响 | 默认 |
| --- | --- | --- |
| 刷新令牌最大期限（针对吊销信息不足的联合用户颁发<sup>1</sup>） |刷新令牌（针对吊销信息不足的联合用户颁发<sup>1</sup>） |12 小时 |
| 刷新令牌最大非活动时间（针对机密客户端颁发） |刷新令牌（针对机密客户端颁发） |90 天 |
| 刷新令牌最大期限（针对机密客户端颁发） |刷新令牌（针对机密客户端颁发） |直到吊销 |

* <sup>1</sup>吊销信息不足的联合用户包括未同步“LastPasswordChangeTimestamp”属性的任何用户。 因为 AAD 无法验证何时吊销绑定旧凭据（例如已更改的密码）的令牌，必须更频繁地重新检查以确保用户和关联的令牌状态仍然良好，所以为用户提供此短暂的最大期限。 若要改善此体验，租户管理员必须确保同步“LastPasswordChangeTimestamp”属性（使用 Powershell 或通过 AADSync 可以对用户对象设置此操作）。

### <a name="policy-evaluation-and-prioritization"></a>策略评估和优先级
可以创建令牌生存期策略并将其分配到特定的应用程序、组织和服务主体。 可将多个策略应用到特定的应用程序。 生效的令牌生存期策略遵循以下规则：

* 如果将某个策略显式分配到服务主体，将强制实施该策略。
* 如果未将策略显式分配到服务主体，则强制实施显式分配到服务主体的父组织的策略。
* 如果未将策略显式分配到服务主体或组织，则强制实施分配到应用程序的策略。
* 如果尚未将策略分配到服务主体、组织或应用程序对象，则强制实施默认值。 （请参阅[可配置的令牌生存期属性](#configurable-token-lifetime-properties)中的表格。）

有关应用程序对象与服务主体对象之间的关系的详细信息，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

使用令牌时，系统会评估其有效性。 所访问的应用程序中具有最高优先级的策略会生效。

此处使用的所有时间范围，将根据 C# [TimeSpan](/dotnet/api/system.timespan) 对象 - D.HH:MM:SS 设置格式。  因此，80 天和 30 分钟将表示为 `80.00:30:00`。  如果为零，则可删除前面的 D，因此，90 分钟将表示为 `00:90:00`。  

> [!NOTE]
> 下面是一个示例场景。
>
> 某个用户想要访问两个 Web 应用程序：Web 应用程序 A 和 Web 应用程序 B。
> 
> 因素：
> * 这两个 Web 应用程序在同一个父组织中。
> * 会话令牌最大期限为 8 小时的令牌生存期策略 1 设置为父组织的默认策略。
> * Web 应用程序 A 是一个常规用途的 Web 应用程序，未链接到任何策略。
> * Web 应用程序 B 用于高度敏感的流程。 该应用程序的服务主体已链接到会话令牌最大期限为 30 分钟的令牌生存期策略 2。
>
> 下午 12:00，用户启动新的浏览器会话，尝试访问 Web 应用程序 A。用户已重定向到 Azure AD 并需要登录。 这会在浏览器中创建一个包含会话令牌的 Cookie。 用户使用可用于访问应用程序的 ID 令牌重定向回到 Web 应用程序 A。
>
> 下午 12:15，用户尝试访问 Web 应用程序 B。浏览器重定向到 Azure AD 并检测会话 Cookie。 Web 应用程序 B 的服务主体已链接到令牌生存期策略 2，但同时也属于使用默认令牌生存期策略 1 的父组织。 由于链接到服务主体的策略优先级高于组织默认策略，因此令牌生存期策略 2 生效。 会话令牌最初是在过去 30 分钟内颁发的，因此被视为有效。 用户使用授予权限的 ID 令牌重定向回到 Web 应用程序 B。
>
> 下午 1:00，用户尝试访问 Web 应用程序 A。该用户已重定向到 Azure AD。 Web 应用程序 A 未链接到任何策略，但由于它在使用默认令牌生存期策略 1 的组织中，因此，该策略会生效。 已检测到最初在过去 8 小时内颁发的会话 Cookie。 用户已使用新 ID 令牌以无提示方式重定向回到 Web 应用程序 A。 用户不需要进行身份验证。
>
> 随后，用户立即尝试访问 Web 应用程序 B。用户已重定向到 Azure AD。 如前所述，令牌生存期策略 2 生效。 由于颁发的令牌超过 30 分钟，系统会提示用户重新输入其凭据。 颁发新的会话令牌和 ID 令牌。 然后，用户可以访问 Web 应用程序 B。
>
>

## <a name="configurable-policy-property-details"></a>可配置的策略属性详细信息
### <a name="access-token-lifetime"></a>访问令牌生存期
**字符串：** AccessTokenLifetime

**影响：** 访问令牌，ID 令牌，SAML 令牌

**摘要：** 此策略控制此资源的访问令牌和 ID 令牌有效期时间长短。 降低“访问令牌生存期”属性可以缓解恶意行动者长时间使用访问令牌或 ID 令牌的风险。 （这些标记不能撤消。）权衡是因为必须更频繁地更换令牌，否则会对性能产生负面影响。

### <a name="refresh-token-max-inactive-time"></a>刷新令牌最大非活动时间
**字符串：** MaxInactiveTime

**影响：** 刷新令牌

**摘要：** 此策略控制在尝试访问资源时，在客户端无法再使用刷新令牌来检索新的访问/刷新令牌对之前的刷新令牌的生存期。 由于使用刷新令牌时通常会返回一个新的刷新令牌，因此，如果客户端在指定的时间段尝试使用当前刷新令牌访问任何资源，此策略会阻止访问。

此策略将强制客户端上处于非活动状态的用户重新进行身份验证，然后才能检索新的刷新令牌。

设置的“刷新令牌最大非活动时间”属性必须必须小于“单因素令牌最大期限”和“多因素刷新令牌最大期限”属性。

### <a name="single-factor-refresh-token-max-age"></a>单因素刷新令牌最大期限
**字符串：** MaxAgeSingleFactor

**影响：** 刷新令牌

**摘要：** 此策略控制用户在上次仅使用一个因素成功完成身份验证后，可以使用刷新令牌获取新访问/刷新令牌对的时间长短。 用户完成身份验证并收到新刷新令牌后，可在指定的时间段内使用刷新令牌流。 （前提是当前刷新令牌未吊销，并且未使用时间超过非活动时间。）此时，将强制用户重新进行身份验证，以接收新的刷新令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个小于“多因素刷新令牌最大期限”属性的值。

### <a name="multi-factor-refresh-token-max-age"></a>多因素刷新令牌最大期限
**字符串：** MaxAgeMultiFactor

**影响：** 刷新令牌

**摘要：** 此策略控制用户在上次使用多个因素成功完成身份验证后，可以使用刷新令牌获取新访问/刷新令牌对的时间长短。 用户完成身份验证并收到新刷新令牌后，可在指定的时间段内使用刷新令牌流。 （前提是当前刷新令牌未吊销，并且未使用时间超过非活动时间。）此时，将强制用户重新进行身份验证，以接收新的刷新令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个大于“单因素刷新令牌最大期限”属性的值。

### <a name="single-factor-session-token-max-age"></a>单因素会话令牌最大期限
**字符串：** MaxAgeSessionSingleFactor

**影响：** 会话令牌（持久性和非持久性）

**摘要：** 此策略控制用户在上次仅使用一个因素成功完成身份验证后，可以使用会话令牌获取新 ID 令牌和会话令牌的时间长短。 用户完成身份验证并收到新会话令牌后，可在指定的时间段内使用会话令牌流。 （前提是当前会话令牌未吊销，并且尚未过期。）在指定的时间段后，将强制用户重新进行身份验证，以接收新的会话令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个小于“多因素会话令牌最大期限”属性的值。

### <a name="multi-factor-session-token-max-age"></a>多因素会话令牌最大期限
**字符串：** MaxAgeSessionMultiFactor

**影响：** 会话令牌（持久性和非持久性）

**摘要：** 此策略控制用户在上次使用多个因素成功完成身份验证后，可以继续使用会话令牌获取新 ID 令牌和会话令牌的时间长短。 用户完成身份验证并收到新会话令牌后，可在指定的时间段内使用会话令牌流。 （前提是当前会话令牌未吊销，并且尚未过期。）在指定的时间段后，将强制用户重新进行身份验证，以接收新的会话令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个大于“单因素会话令牌最大期限”属性的值。

## <a name="example-token-lifetime-policies"></a>示例令牌生存期策略
如果能够创建和管理应用、服务主体和整个组织的令牌生存期，就可以在 Azure AD 中实现各种新的方案。 本部分逐步讲解一些常见的策略方案，帮助你针对以下属性实施新规则：

* 令牌生存期
* 令牌最大非活动时间
* 令牌最大期限

通过这些示例，可以了解如何执行以下操作：

* 管理组织的默认策略
* 为 Web 登录创建策略
* 为调用 Web API 的本机应用创建策略
* 管理高级策略

### <a name="prerequisites"></a>必备条件
以下示例演示如何创建、更新、链接和删除应用、服务主体和整个组织的策略。 如果是 Azure AD 新手，我们建议在继续学习这些示例之前，先了解[如何获取 Azure AD 租户](quickstart-create-new-tenant.md)。  

若要开始，请执行以下步骤：

1. 首先请下载最新的 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。
2. 运行 `Connect` 命令登录到 Azure AD 管理员帐户。 每次启动新会话都需要运行此命令。

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 若要查看组织中创建的所有策略，请运行以下命令。 执行以下方案中的大多数操作之后，都要运行此命令。 运行此命令还可帮助获取策略的 ** **。

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>示例：管理组织的默认策略
在此示例中，你将创建一个策略，让你的用户无频率地在整个组织中登录。 为此，可以针对应用到整个组织的单因素刷新令牌创建一个令牌生存期策略。 此策略将应用到组织中的每个应用程序，以及尚未设置策略的每个服务主体。

1. 创建令牌生存期策略。

    1. 将单因素刷新令牌设置为“直到吊销”。 在吊销访问权限之前该令牌不会过期。 创建以下策略定义：

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. 若要创建该策略，请运行以下命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要删除任何空白，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. 若要查看新策略并获取其 **ObjectId**，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 更新策略。

    假设在本示例中创建的第一个策略不像服务要求的那样严格。 要将单因素刷新令牌设置为在两天后过期，请运行以下命令：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>示例：为 Web 登录创建策略

本示例创建一个要求用户更频繁地在 Web 应用中进行身份验证的策略。 此策略会针对 Web 应用的服务主体设置访问/ID 令牌的生存期以及多因素会话令牌的最大期限。

1. 创建令牌生存期策略。

    这个用于 Web 登录的策略将访问/ID 令牌生存期和单因素会话令牌最大期限设置为 2 小时。

    1. 若要创建该策略，请运行以下命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略并获取其 **ObjectId**，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到服务主体。 还需要获取服务主体的**ObjectId** 。

    1. 使用[get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet 可查看组织的所有服务主体或单个服务主体。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. 如果有服务主体，请运行以下命令：
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>示例：为调用 Web API 的本机应用创建策略
本示例创建一个不要求用户太频繁进行身份验证的策略。 该策略还可延长用户可保持非活动状态、不必再次身份验证的时间。 该策略将应用到 Web API。 当本机应用以资源形式请求 Web API 时，将应用此策略。

1. 创建令牌生存期策略。

    1. 若要为 Web API 创建一个严格的策略，请运行以下命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到 Web API。 还需要获取应用程序的 **ObjectId**。 使用[get-azureadapplication](/powershell/module/azuread/get-azureadapplication) cmdlet 查找应用的**ObjectId**，或使用[Azure 门户](https://portal.azure.com/)。

    获取应用的**ObjectId**并分配策略：

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>示例：管理高级策略
在此示例中，将创建一些策略来了解优先级系统的工作原理。 你还将了解如何管理应用于多个对象的多个策略。

1. 创建令牌生存期策略。

    1. 要创建一个将单因素刷新令牌生存期设置为 30 天的组织默认策略，请运行以下命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到服务主体。

    现已创建一个要应用到整个组织的策略。 可能想要为特定的服务主体保留这个 30 天策略，但要将组织默认策略更改为上限“直到吊销”。

    1. 若要查看组织的所有服务主体，请使用[get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet。

    1. 如果有服务主体，请运行以下命令：

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. 将 `IsOrganizationDefault` 标志设置为 false：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. 创建新的组织默认策略：

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    现在，已将原始策略链接到服务主体，已将新策略设置为组织默认策略。 请务必记住，应用到服务主体的策略优先级高于组织默认策略。

## <a name="cmdlet-reference"></a>Cmdlet 参考

### <a name="manage-policies"></a>管理策略

可以使用以下 cmdlet 来管理策略。

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

创建新策略。

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Definition</code> |包含所有策略规则的字符串化 JSON 的数组。 | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |策略名称的字符串。 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |如果为 true，则将策略设置为组织的默认策略。 如果为 false，则不执行任何操作。 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |策略的类型。 对于令牌生存期，始终使用“TokenLifetimePolicy”。 | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [可选] |设置策略的备用 ID。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
获取所有 Azure AD 策略或指定的策略。

```powershell
Get-AzureADPolicy
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> [可选] |所需策略的**ObjectId （ID）** 。 |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
获取已链接到策略的所有应用和服务主体。

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |所需策略的**ObjectId （ID）** 。 |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
更新现有策略。

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |所需策略的**ObjectId （ID）** 。 |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |策略名称的字符串。 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [可选] |包含所有策略规则的字符串化 JSON 的数组。 |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [可选] |如果为 true，则将策略设置为组织的默认策略。 如果为 false，则不执行任何操作。 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [可选] |策略的类型。 对于令牌生存期，始终使用“TokenLifetimePolicy”。 |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [可选] |设置策略的备用 ID。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
删除指定的策略。

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |所需策略的**ObjectId （ID）** 。 | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>应用程序策略
可以针对应用程序策略使用以下 cmdlet。</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
将指定的策略链接到应用程序。

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |应用程序的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |策略的 **ObjectId**。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
获取已分配到应用程序的策略。

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |应用程序的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
从应用程序中删除策略。

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |应用程序的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |策略的 **ObjectId**。 | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>服务主体策略
可以针对服务主体策略使用以下 cmdlet。

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
将指定的策略链接到服务主体。

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |应用程序的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |策略的 **ObjectId**。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
获取已链接到指定服务主体的任何策略。

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |应用程序的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
从指定的服务主体中删除策略。

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |应用程序的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |策略的 **ObjectId**。 | `-PolicyId <ObjectId of Policy>` |
