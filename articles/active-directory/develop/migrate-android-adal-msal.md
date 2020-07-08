---
title: 适用于 Android 的 ADAL 到 MSAL 迁移指南 | Azure
description: 了解如何将 Azure Active Directory 身份验证库 (ADAL) Android 应用迁移到 Microsoft 身份验证库 (MSAL)。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77084060"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>适用于 Android 的 ADAL 到 MSAL 迁移指南

本文重点介绍需要做出哪些更改，才能迁移使用 Azure Active Directory 身份验证库 (ADAL) 的应用，使之使用 Microsoft 身份验证库 (MSAL)。

## <a name="difference-highlights"></a>差异重点说明

ADAL 适用于 Azure Active Directory v1.0 终结点。 Microsoft 身份验证库 (MSAL) 适用于 Microsoft 标识平台（前称为 Azure Active Directory v2.0 终结点）。 Microsoft 标识平台与 Azure Active Directory v1.0 的不同之处在于：

支持：
  - 组织标识 (Azure Active Directory)
  - 非组织标识，如 Outlook.com、Xbox Live 等
  - （仅限 B2C）与 Google、Facebook、Twitter 和 Amazon 联合登录

- 与以下协议的标准兼容：
  - OAuth v2.0
  - OpenID Connect (OIDC)

MSAL 公共 API 引入了重要的更改，其中包括：

- 用于访问令牌的新模型：
  - ADAL 允许通过 `AuthenticationContext`（表示服务器）访问令牌。 MSAL 允许通过 `PublicClientApplication`（表示客户端）访问令牌。 客户端开发人员无需为每个要交互的颁发机构创建新的 `PublicClientApplication` 实例。 只需一个 `PublicClientApplication` 配置。
  - 除使用资源标识符以外，还支持使用范围请求访问令牌。
  - 对增量许可的支持。 当用户在应用中访问越来越多的功能时，开发人员可以请求范围，包括应用注册期间未包含的范围。
  - 不再在运行时验证颁发机构， 而是由开发人员在开发过程中声明“已知颁发机构”列表。
- 令牌 API 更改：
  - 在 ADAL 中，`AcquireToken()` 首先发出静默请求。 如果该请求失败，则发出交互式请求。 此行为导致某些开发人员仅依赖于 `AcquireToken`，这又导致系统有时候以意外方式提示用户提供凭据。 MSAL 要求开发人员留意用户收到 UI 提示的时间。
    - `AcquireTokenSilent` 始终生成成功或失败的静默请求。
    - `AcquireToken` 始终生成一个通过 UI 提示用户的请求。
- MSAL 支持来自默认浏览器或嵌入式 Web 视图的登录：
  - 默认情况下，使用设备上的默认浏览器。 这样，MSAL 就可以使用已为一个或多个登录帐户提供的身份验证状态 (Cookie)。 如果未提供身份验证状态，则在授权期间通过 MSAL 进行的身份验证将导致创建身份验证状态 (Cookie)，以便为同一浏览器中使用的其他 Web 应用程序提供便利。
- 新的异常模型：
  - 异常更明确地定义了发生的错误类型，以及开发人员需要采取哪些措施来解决该错误。
- MSAL 支持 `AcquireToken` 和 `AcquireTokenSilent` 调用的参数对象。
- MSAL 支持以下项的声明性配置：
  - 客户端 ID、重定向 URI。
  - 嵌入式浏览器与默认浏览器
  - 颁发机构
  - 读取和连接超时等 HTTP 设置

## <a name="your-app-registration-and-migration-to-msal"></a>应用注册以及到 MSAL 的迁移

不需更改现有应用注册即可使用 MSAL。 若要利用增量/渐进式许可，可能需要查看注册，以识别要增量请求的特定范围。 下面是有关范围和增量许可的详细信息。

在门户中的应用注册中，你将看到 " **API 权限**" 选项卡。这将提供应用当前配置为请求访问权限的 Api 和权限（作用域）的列表。 其中还显示与每个 API 权限关联的范围名称列表。

### <a name="user-consent"></a>用户同意

首次使用 ADAL 和 AAD v1 终结点时，就会授予用户对其拥有的资源的许可。 通过 MSAL 和 Microsoft 标识平台时，可以增量请求许可。 对于被用户视为高特权的权限，或者对为何需要某个权限提供明确的解释时，增量许可非常有用。 在 ADAL 中，这些权限可能导致用户放弃应用登录。

> [!TIP]
> 如果需要向用户提供额外的上下文来解释为何应用需要某个权限，我们建议使用增量许可。

### <a name="admin-consent"></a>管理员同意

组织管理员可以代表其组织中的所有成员来许可应用程序所需的权限。 某些组织仅允许管理员向应用程序提供许可。 管理员许可要求在应用注册中包含应用程序使用的所有 API 权限和范围。

> [!TIP]
> 即使可以使用 MSAL 来为应用注册中未包含的活动请求范围，我们也建议更新应用注册，以包含用户可以授权访问的所有资源和范围。

## <a name="migrating-from-resource-ids-to-scopes"></a>从资源 ID 迁移到范围

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>首次使用时进行身份验证并请求所有权限的授权

如果目前使用的是 ADAL 且不需要使用增量许可，则开始使用 MSAL 的最简单方法是使用新的 `AcquireTokenParameter` 对象并设置资源 ID 值来发出 `acquireToken` 请求。

> [!CAUTION]
> 不能同时设置作用域和资源 id。尝试同时设置这两个将导致 `IllegalArgumentException` 。

 这与使用 v1 时的行为相同。 在应用注册中请求的所有权限是用户首次交互期间从用户请求的。

### <a name="authenticate-and-request-permissions-only-as-needed"></a>仅在有需要时才进行身份验证并请求权限

若要利用增量许可，请创建应用要在应用注册中使用的权限（范围）列表，并根据以下条件将其组织成两份列表：

- 用户在登录期间首次与应用交互时你要请求的范围。
- 与应用的某项重要功能关联的权限（你还需要向用户解释为何需要这些权限）。

组织范围后，请根据要请求其令牌的资源 (API) 组织每个列表。 以及同时希望用户授权的任何其他范围。

用于向 MSAL 发出请求的参数对象支持：

- `Scope`：你要为其请求授权的作用域的列表，并收到访问令牌。
- `ExtraScopesToConsent`：你希望在请求其他资源的访问令牌时请求授权的作用域的其他列表。 使用此范围列表可以最大程度地减少请求用户授权的次数。 这意味着可以减少用户授权或许可提示的次数。

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>从 AuthenticationContext 迁移到 PublicClientApplications

### <a name="constructing-publicclientapplication"></a>构造 PublicClientApplication

使用 MSAL 时，将实例化 `PublicClientApplication`。 此对象为应用标识建模，并用于向一个或多个颁发机构发出请求。 将使用此对象来配置客户端标识、重定向 URI、默认颁发机构、是使用设备浏览器还是嵌入式 Web 视图、日志级别，等等。

可以使用 JSON 以声明方式配置此对象，然后将其作为文件提供，或作为资源存储在 APK 中。

尽管此对象不是单一实例，但在内部，它使用共享的 `Executors` 来处理交互式请求和静默请求。

### <a name="business-to-business"></a>企业到企业

在 ADAL 中，从中请求访问令牌的每个组织都需要 `AuthenticationContext` 的单独实例。 在 MSAL 中，不再存在此项要求。 可以在静默请求或交互式请求中指定要从中请求令牌的授权机构。

### <a name="migrate-from-authority-validation-to-known-authorities"></a>从颁发机构验证迁移到已知颁发机构

MSAL 不提供用于启用或禁用颁发机构验证的标志。 颁发机构验证是 ADAL 以及早期版本的 MSAL 中的一项功能，用于防止代码从可能的恶意颁发机构请求令牌。 MSAL 现在会检索 Microsoft 已知的颁发机构列表，并将该列表与在配置中指定的颁发机构合并到一起。

> [!TIP]
> 对于 Azure 企业到消费者 (B2C) 用户而言，这意味着不再需要禁用颁发机构验证， 而可以将每个受支持的 Azure AD B2C 策略作为颁发机构包含在 MSAL 配置中。

如果尝试使用 Microsoft 未知的颁发机构，并且未在配置中包含该颁发机构，将会收到 `UnknownAuthorityException`。

### <a name="logging"></a>Logging
现在可在配置中以声明方式配置日志记录，如下所示：

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>从 UserInfo 迁移到帐户

在 ADAL 中，`AuthenticationResult` 提供一个 `UserInfo` 对象用于检索有关经过身份验证的帐户的信息。 术语“用户”（表示某个人或软件代理）的运用方式使得难以传达某些应用支持具有多个帐户的单个用户（无论是人还是软件代理）这一信息。

以某个银行帐户为例。 你可能在多个金融机构建立了多个帐户。 建立每个帐户时，机构将你为（用户）颁发凭据（例如 ATM 卡和 PIN）用于访问余额、帐单付款等。 这些凭据只能在颁发它们的金融机构使用。

与金融机构的帐户一样，Microsoft 标识平台中的帐户也是使用凭据访问的。 这些凭据是在 Microsoft 注册的、由 Microsoft 颁发， 或者由 Microsoft 代表某家组织颁发。

相比之下，Microsoft 标识平台与金融机构的不同之处在于，Microsoft 标识平台提供一个框架，可让用户使用一个帐户及其关联的凭据来访问属于多个个人和组织的资源。 这类似于用户能够使用某家银行在另一所金融机构颁发的银行卡。 这种运作方式之所以可行，是因为相关的所有组织都使用 Microsoft 标识平台，允许在多个组织中使用一个帐户。 以下是一个示例：

Sam 在 Contoso.com 任职，同时管理属于 Fabrikam.com 的 Azure 虚拟机。 要使 Sam 能够管理 Fabrikam 的虚拟机，他需要获取访问这些虚拟机的授权。 要向 Sam 授予此访问权限，可将其帐户添加到 Fabrikam.com，并向其帐户授予一个可以管理虚拟机的角色。 也可以使用 Azure 门户进行这种授权。

将 Sam 的 Contoso.com 帐户添加为 Fabrikam.com 的成员会导致在 Fabrikam.com 的 Azure Active Directory 中为 Sam 创建新的记录。 Sam 在 Azure Active Directory 中的记录称为用户对象。 在这种情况下，该用户对象会指向回到 Sam 在 Contoso.com 中的用户对象。 Sam 的 Fabrikam 用户对象是 Sam 的本地表示形式，用于在 Fabrikam.com 的上下文中存储与 Sam 关联的帐户的相关信息。 在 Contoso.com 中，Sam 的职称为“高级 DevOps 顾问”。 在 Fabrikam 中，Sam 的职称为“虚拟机签约管理员”。 在 Contoso.com 中，Sam 既不负责，也未获授权管理虚拟机。 在 Fabrikam.com 中，这是他的唯一职责。 但是，Sam 仍只有一组需要跟踪的凭据，即 Contoso.com 颁发的凭据。

成功发出 `acquireToken` 调用后，将会看到对 `IAccount` 对象的引用，该对象可在后续的 `acquireTokenSilent` 请求中使用。

### <a name="imultitenantaccount"></a>IMultiTenantAccount

如果某个应用可以从代表某帐户的每个租户中访问有关该帐户的声明，则可以将 `IAccount` 对象强制转换为 `IMultiTenantAccount`。 此接口提供按租户 ID 键控的 `ITenantProfiles` 映射，使你能够在相对于当前帐户的、从中请求了令牌的每个租户中访问属于该帐户的声明。

位于 `IAccount` 和 `IMultiTenantAccount` 根位置的声明始终包含主租户中的声明。 如果你尚未对主租户中的令牌发出请求，则此集合是空的。

## <a name="other-changes"></a>其他更改

### <a name="use-the-new-authenticationcallback"></a>使用新的 AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>迁移到新的异常

在 ADAL 中，有一个异常类型 `AuthenticationException` 包含用于检索 `ADALError` 枚举值的方法。
MSAL 中提供异常层次结构，每个异常具有自身的一组关联的特定错误代码。

MSAL 异常列表

|例外  | 描述  |
|---------|---------|
| `MsalException`     | MSAL 引发的默认选择异常。  |
| `MsalClientException`     | 当错误在客户端上发生时引发。 |
| `MsalArgumentException`     | 当一个或多个输入参数无效时引发。 |
| `MsalClientException`     | 当错误在客户端上发生时引发。 |
| `MsalServiceException`     | 当错误在服务器端上发生时引发。 |
| `MsalUserCancelException`     | 当用户取消了身份验证流时引发。  |
| `MsalUiRequiredException`     | 当令牌无法以静默方式刷新时引发。  |
| `MsalDeclinedScopeException`     | 当服务器拒绝了一个或多个请求的范围时引发。  |
| `MsalIntuneAppProtectionPolicyRequiredException` | 当资源启用了 MAMCA 保护策略时引发。 |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError 到 MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>ADAL 日志记录到 MSAL 日志记录

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
