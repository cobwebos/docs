---
title: 为什么要更新到 Microsoft 标识平台（v2.0） |Microsoft
description: 了解 Microsoft 标识平台（v2.0）终结点与 Azure Active Directory （Azure AD） v2.0 终结点之间的差异，并了解更新到 v2.0 的好处。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.openlocfilehash: a05db11f48811f5ad6163447fae1dbb4ea31842c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712442"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>为什么要更新到 Microsoft 标识平台（v2.0）？

开发新应用程序时，了解 Microsoft 标识平台（v2.0）与 Azure Active Directory （1.0）终结点之间的差异非常重要。 本文介绍了端点与 Microsoft 标识平台的一些现有限制之间的主要区别。

> [!NOTE]
> Microsoft 标识平台终结点并不支持所有 Azure AD 方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读[microsoft 标识平台限制](#limitations)。

## <a name="who-can-sign-in"></a>로그인할 수 있는 사용자

![v1.0 및 v2.0 엔드포인트에 로그인할 수 있는 사용자](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* v1.0 엔드포인트의 경우 회사 및 학교 계정만 애플리케이션(Azure AD)에 로그인할 수 있습니다.
* Microsoft 标识平台终结点允许 Azure AD 和个人 Microsoft 帐户（MSA）（如 hotmail.com、outlook.com 和 msn.com）的工作和学校帐户登录。
* 对于配置为 *[单租户](single-and-multi-tenant-apps.md)* 的应用程序或配置为指向特定于租户的终结点（`https://login.microsoftonline.com/{TenantId_or_Name}`）的*多租户*应用程序，这两个终结点还接受 Azure AD 目录的 *[来宾用户](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* 的登录。

Microsoft 标识平台终结点允许你编写应用，这些应用接受来自 Microsoft 个人帐户和工作和学校帐户的登录。 따라서 완전히 계정에 제약이 없는 앱을 작성할 수 있습니다. 예를 들어 앱이 [Microsoft Graph](https://graph.microsoft.io)를 호출하는 경우 일부 추가 기능 및 데이터를 해당 SharePoint 사이트 또는 디렉터리 데이터와 같은 회사 계정에서 사용할 수 있습니다. 단, [사용자의 이메일 읽기](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages)와 같은 많은 작업에서 동일한 코드가 개인 계정과 회사 및 학교 계정 이메일 모두에 액세스할 수 있습니다.

对于 Microsoft 标识平台终结点，你可以使用 Microsoft 身份验证库（MSAL）来获取对消费者、教育和企业领域的访问权限。 Azure AD v1.0 엔드포인트는 회사 및 학교 계정에서만 로그인을 허용합니다.

## <a name="incremental-and-dynamic-consent"></a>증분 및 동적 동의

Azure AD v1.0 엔드포인트를 사용하는 앱은 필수 OAuth 2.0 권한을 사전에 지정해야 합니다. 예를 들면:

![显示权限注册 UI 的示例](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

애플리케이션 등록에서 직접 설정한 권한은 **정적**입니다. Azure Portal에 정의된 앱의 정적 권한은 코드를 멋지고 간단하게 유지했지만, 개발자에게 몇 가지 가능한 문제를 줄 수 있습니다.

* 사용자가 처음 로그인할 때 앞으로 앱에서 필요로 하게 될 모든 권한을 요청해야 합니다. 이로 인해 권한 목록이 매우 길어서 최종 사용자는 처음 로그인 시 앱의 액세스를 승인하지 않을 수 있습니다.

* 앱이 액세스할 수도 있는 모든 리소스를 미리 알아야 합니다. 리소스의 임의 개수에 액세스할 수 있는 앱을 만들기 어려웠습니다.

使用 Microsoft 标识平台终结点，你可以忽略 Azure 门户中的应用注册信息中定义的静态权限，而改为以增量方式请求权限，这意味着提前要求最小的权限集随着时间的推移越来越多，客户会使用其他应用功能。 이렇게 하려면 애플리케이션 등록 정보에 미리 정의할 필요 없이, 액세스 토큰을 요청할 때 `scope` 매개 변수에 새 범위를 포함시켜서 언제든지 앱에 필요한 범위를 지정할 수 있습니다. 사용자가 요청에 추가된 새 범위에 아직 동의하지 않은 경우에는 새 권한에만 동의하라는 메시지가 표시됩니다. 자세한 내용은 [권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요.

`scope` 매개 변수를 통해 앱이 동적으로 권한을 요청할 수 있도록 허용하면 개발자가 사용자 환경을 완전히 제어할 수 있습니다. 초기 단계에 동의 환경을 배치하고 하나의 초기 권한 부여 요청으로 모든 권한을 요청할 수도 있습니다. 앱이 많은 권한을 요청하는 경우 시간의 경과에 따라 앱의 특정 기능을 사용하도록 시도하는 것처럼 점진적으로 사용자로부터 권한을 수집할 수 있습니다.

조직 대신 관리자가 동의를 수행하려면 앱에 등록된 정적 권한이 필요하므로, 전체 조직을 대신하여 관리자가 동의해야 하는 경우 앱 등록 포털에서 앱에 대한 권한을 설정해야 합니다. 이렇게 하면 조직 관리자가 애플리케이션을 설정하는 데 필요한 주기가 줄어듭니다.

## <a name="scopes-not-resources"></a>리소스가 아닌 범위

v1.0 엔드포인트를 사용하는 앱은 **리소스** 또는 토큰 수신자로 작동할 수 있습니다. 리소스는 많은 **범위** 또는 이해할 수 있는 **oAuth2Permissions**를 정의할 수 있으며, 클라이언트 앱이 범위의 특정 집합에 대한 리소스에서 토큰을 요청하도록 허용합니다. 请考虑 Microsoft Graph API 作为资源的示例：

* 리소스 식별자 또는 `AppID URI`: `https://graph.microsoft.com/`
* 범위 또는 `oAuth2Permissions`: `Directory.Read`, `Directory.Write` 등

这适用于 Microsoft 标识平台终结点。 앱은 여전히 리소스로 작동하고, 범위를 정의하고, URI로 식별될 수 있습니다. 클라이언트 앱은 여전히 해당 범위에 액세스 요청할 수 있습니다. 但是，客户端请求这些权限的方式已更改。

v1.0 엔드포인트의 경우, Azure AD에 대한 OAuth 2.0 인증 요청은 다음과 같습니다.

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

여기서 **resource** 매개 변수는 클라이언트 앱이 권한 부여를 요청하는 리소스를 나타냅니다. Azure AD는 Azure Portal의 정적 구성을 기준으로 앱에 필요한 권한을 계산하고, 이에 따라 토큰을 발급합니다.

对于使用 Microsoft 标识平台终结点的应用程序，相同的 OAuth 2.0 授权请求如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

여기서 **scope** 매개 변수는 앱이 권한 부여를 요청하는 리소스와 권한을 나타냅니다. 원하는 리소스는 여전히 요청에 있으며, scope 매개 변수의 각 값에 포함되어 있습니다. 以这种方式使用 scope 参数可使 Microsoft 标识平台终结点更符合 OAuth 2.0 规范，并更密切地与常见的行业实践进行对齐。 它还使应用程序能够进行[增量许可](#incremental-and-dynamic-consent)-仅当应用程序要求它们（而不是前端）时请求权限。

## <a name="well-known-scopes"></a>잘 알려진 범위

### <a name="offline-access"></a>오프라인 액세스

使用 Microsoft 标识平台终结点的应用可能需要针对应用使用新的已知权限-`offline_access` 范围。 모든 앱이 연장된 기간 동안 사용자를 대신하여 리소스에 액세스해야 할 경우, 사용자가 앱을 자주 쓰지 않는 경우에도 이 권한을 요청해야 합니다. `offline_access` 범위는 사용자에게 동의 대화 상자에 **언제든지 데이터에 액세스**로 표시되며 사용자는 여기에 반드시 동의해야 합니다. 请求 `offline_access` 权限将允许 web 应用从 Microsoft 标识平台终结点接收 OAuth 2.0 refresh_tokens。 새로 고침 토큰은 수명이 길며, 오랜 기간 액세스하기 위해 새로운 OAuth 2.0 액세스 토큰으로 교환할 수 있습니다.

如果你的应用未请求 `offline_access` 范围，则它不会接收刷新令牌。 즉, OAuth 2.0 권한 부여 코드 흐름에서 인증 코드를 사용하면 `/token` 엔드포인트에서 액세스 토큰만 수신하게 됩니다. 이 액세스 토큰은 짧은 기간(일반적으로 1시간) 동안 유효하지만 결국 만료됩니다. 해당 시점에 앱은 사용자를 `/authorize` 엔드포인트로 다시 리디렉션하여 새 인증 코드를 검색해야 합니다. 리디렉션 중에 앱 유형에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수도 있고 그렇지 않을 수도 있습니다.

若要了解有关 OAuth 2.0、`refresh_tokens`和 `access_tokens`的详细信息，请参阅[Microsoft 标识平台协议参考](active-directory-v2-protocols.md)。

### <a name="openid-profile-and-email"></a>OpenID, 프로필 및 메일

从历史上看，使用 Microsoft 标识平台的最基本的 OpenID Connect 登录流在生成的*id_token*中提供了大量有关用户的信息。 id_token의 클레임에는 사용자의 이름, 기본 설정된 사용자 이름, 전자 메일 주소, 개체 ID 등이 있습니다.

이제 `openid` 범위를 통해 앱의 액세스가 허용되는 정보가 제한됩니다. `openid` 범위는 앱이 사용자를 로그인하고 해당 사용자의 앱 특정 식별자를 수신하는 작업만 허용합니다. 앱에 있는 사용자의 개인 데이터를 가져오려면 앱이 사용자로부터 추가 권한을 요청해야 합니다. 두 개의 새 범위(`email` 및 `profile` 범위)를 통해 추가 권한을 요청할 수 있습니다.

* `email` 범위는 사용자에게 주소 지정 가능한 메일 주소가 있다고 가정하여 앱이 id_token의 `email` 클레임을 통해 사용자의 기본 메일 주소에 액세스할 수 있도록 해줍니다.
* `profile` 范围使你的应用程序可以访问 id_token 中有关用户的所有其他基本信息，例如其名称、首选用户名、对象 ID 等等。

이러한 범위를 사용하면 최소한의 공개로 앱을 코딩할 수 있으며, 작업 수행에 필요한 정보만 사용자에게 요청할 수 있습니다. 有关这些范围的详细信息，请参阅[Microsoft 标识平台范围引用](v2-permissions-and-consent.md)。

## <a name="token-claims"></a>토큰 클레임

默认情况下，Microsoft 标识平台终结点在其令牌中颁发一组较小的声明，以使负载保持较小。 如果你的应用程序和服务依赖于不在 Microsoft 标识平台令牌中默认提供的1.0 版令牌中的特定声明，请考虑使用[可选的声明](active-directory-optional-claims.md)功能来包含该声明。

> [!IMPORTANT]
> v1.0 和 v2.0 终结点都可以颁发 v1.0 和 v2.0 令牌！ id_tokens*始终*匹配请求的终结点，并且访问令牌*始终*匹配客户端将使用该令牌调用的 Web API 所需的格式。  因此，如果你的应用程序使用 v2.0 终结点获取令牌来调用 Microsoft Graph，这需要使用 v1.0 格式的访问令牌，则你的应用程序将收到采用1.0 格式的令牌。  

## <a name="limitations"></a>제한 사항

使用 Microsoft 标识平台时，有一些限制需要注意。

构建与 Microsoft 标识平台集成的应用程序时，需要确定 Microsoft 标识平台终结点和身份验证协议是否满足你的需求。 1\.0 版终结点和平台仍完全受支持，并且在某些方面比 Microsoft 标识平台功能更丰富。 但是，Microsoft 标识平台为开发人员[带来了极大的好处](azure-ad-endpoint-comparison.md)。

下面是针对开发人员的简单建议：

* 如果你希望或需要在你的应用程序中支持个人 Microsoft 帐户，或者你正在编写新应用程序，请使用 Microsoft 标识平台。 그러나 그 전에 이 문서에 설명된 제한 사항을 이해해야 합니다.
* 如果要迁移或更新依赖 SAML 的应用程序，则无法使用 Microsoft 标识平台。 请参阅[Azure AD v1.0 指南](v1-overview.md)。

Microsoft 标识平台终结点将发展以消除此处列出的限制，因此你只需使用 Microsoft 标识平台终结点。 同时，请使用本文确定 Microsoft 标识平台终结点是否适合你。 我们将继续更新本文，以反映 Microsoft 标识平台终结点的当前状态。 请返回以根据 Microsoft 标识平台功能重新评估你的要求。

### <a name="restrictions-on-app-registrations"></a>앱 등록에 대한 제한 사항

对于想要与 Microsoft 标识平台终结点集成的每个应用，可以在 Azure 门户的新[**应用注册**体验](https://aka.ms/appregistrations)中创建应用注册。 现有 Microsoft 帐户的应用不与门户兼容，但所有 Azure AD 应用都是，不管它们是在何处注册的。

회사 및 학교 계정과 개인 계정을 지원하는 앱 등록에는 다음과 같은 주의 사항이 있습니다.

* 애플리케이션 ID당 두 개의 앱 비밀만 허용됩니다.
* 테넌트에 등록되지 않은 애플리케이션은 등록한 계정에서만 관리할 수 있습니다. 다른 개발자와 공유할 수 없습니다. 이는 앱 등록 포털에서 개인 Microsoft 계정을 사용하여 등록된 대부분의 앱에 적용됩니다. 如果要与多个开发人员共享应用注册，请使用 Azure 门户的新**应用注册**部分在租户中注册该应用程序。
* 허용된 리디렉션 URL 형식에 대한 몇 가지 제한 사항이 있습니다. 리디렉션 URL에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="restrictions-on-redirect-urls"></a>리디렉션 URI에 대한 제한

为 Microsoft 标识平台注册的应用限制为一组有限的重定向 URL 值。 웹앱 및 서비스에 대한 리디렉션 URL은 스키마 `https`로 시작해야 하고 모든 리디렉션 URL 값은 단일 DNS 도메인을 공유해야 합니다.  등록 시스템은 기존 리디렉션 URL의 전체 DNS 이름을 추가하려는 리디렉션 URL의 DNS 이름과 비교합니다. `http://localhost`도 리디렉션 URL로 지원됩니다.  

다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.  

* 새 리디렉션 URL의 전체 DNS 이름이 기존 리디렉션 URL의 DNS 이름과 일치하지 않는 경우
* 새 리디렉션 URL의 전체 DNS 이름이 기존 리디렉션 URL의 하위 도메인이 아닌 경우

#### <a name="example-1"></a>예 1

앱에 `https://login.contoso.com`의 리디렉션 URL이 있는 경우 다음 예제와 같이 DNS 이름이 정확히 일치하는 리디렉션 URL을 추가할 수 있습니다.

`https://login.contoso.com/new`

또는 다음 예제와 같이 login.contoso.com의 DNS 하위 도메인을 참조할 수 있습니다.

`https://new.login.contoso.com`

#### <a name="example-2"></a>예제 2

`login-east.contoso.com` 및 `login-west.contoso.com`을 리디렉션 URL로 사용하는 앱이 필요하면 리디렉션 URL을 다음 순서로 추가해야 합니다.

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

可以添加后两个，因为它们是第一个重定向 URL contoso.com 的子域。

对于特定的应用程序，只能有20个答复 Url-此限制适用于注册支持的所有应用类型（单页应用程序（SPA）、本机客户端、web 应用和服务）。  

若要了解如何注册应用以与 Microsoft 标识平台一起使用，请参阅[使用全新的应用注册体验注册应用](quickstart-register-app.md)。

### <a name="restrictions-on-libraries-and-sdks"></a>라이브러리 및 SDK에 대한 제한 사항

目前，Microsoft 标识平台终结点的库支持受到限制。 如果要在生产应用程序中使用 Microsoft 标识平台终结点，则可以使用以下选项：

* 如果要构建 web 应用程序，则可以安全地使用正式发布的服务器端中间件来执行登录和令牌验证。 여기에는 ASP.NET용 OWIN Open ID Connect 미들웨어 및 Node.js Passport 플러그 인이 포함됩니다. 有关使用 Microsoft 中间件的代码示例，请参阅[microsoft 标识平台入门](v2-overview.md#getting-started)部分。
* 如果要构建桌面或移动应用程序，可以使用 Microsoft 身份验证库（MSAL）之一。 这些库已公开发布或处于支持生产的预览版中，因此可以在生产应用程序中安全地使用它们。 미리 보기 조건 및 사용 가능한 라이브러리에 대한 자세한 내용은 [인증 라이브러리 참조](reference-v2-libraries.md)에서 확인할 수 있습니다.
* 对于 Microsoft 库未涵盖的平台，可以通过直接在应用程序代码中发送和接收协议消息来与 Microsoft 标识平台终结点集成。 OpenID Connect 和 OAuth 协议[已明确](active-directory-v2-protocols.md)说明，可帮助您执行此类集成。
* 最后，可以使用开源 OpenID Connect 和 OAuth 库与 Microsoft 标识平台终结点集成。 Microsoft 标识平台终结点应与许多开源协议库兼容，且不会发生更改。 이러한 라이브러리의 사용 가능 여부는 언어 및 플랫폼마다 다릅니다. [OpenID Connect](https://openid.net/connect/) 및 [OAuth 2.0](https://oauth.net/2/) 웹 사이트는 주요 구현 목록을 유지 관리합니다. 有关详细信息，请参阅[microsoft 标识平台和身份验证库](reference-v2-libraries.md)，以及已使用 Microsoft 标识平台终结点测试的开源客户端库和示例的列表。
* 作为参考，Microsoft 标识平台公用终结点的 `.well-known` 终结点 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`。 `common`을 테넌트 ID로 바꾸어 테넌트와 관련된 데이터를 가져오세요.  

### <a name="protocol-changes"></a>프로토콜 변경 내용

Microsoft 标识平台终结点不支持 SAML 或 WS 联合身份验证;它仅支持 OpenID Connect 和 OAuth 2.0。  v1.0 엔드포인트에서 OAuth 2.0 프로토콜의 주목할 만한 변경 내용은 다음과 같습니다. 

* `email` 클레임은 선택적 클레임이 구성되어 있는 경우 **또는** 요청에서 범위=메일로 지정된 경우 반환됩니다. 
* 이제 `resource` 매개 변수 대신 `scope` 매개 변수가 지원됩니다.  
* OAuth 2.0 사양을 보다 잘 준수하도록 많은 응답이 수정되었습니다(예: `expires_in`을 string 대신 int로 올바르게 반환).  

若要更好地了解 Microsoft 标识平台终结点支持的协议功能范围，请参阅[OpenID connect 和 OAuth 2.0 协议参考](active-directory-v2-protocols.md)。

#### <a name="saml-restrictions"></a>SAML 제한 사항

如果已在 Windows 应用程序中使用 Active Directory 身份验证库（ADAL），则可能已利用了使用安全断言标记语言（SAML）断言授予的 Windows 集成身份验证。 이 권한 부여를 통해 페더레이션된 Azure AD 테넌트의 사용자는 자격 증명을 입력하지 않고도 해당 온-프레미스 Active Directory 인스턴스로 자동으로 인증할 수 있습니다. Microsoft 标识平台终结点不支持 SAML 断言授予。
