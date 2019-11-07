---
title: 适用于 Android 的 ADAL 到 MSAL 迁移指南 |Microsoft
description: 了解如何将 Azure Active Directory 身份验证库（ADAL） Android 应用迁移到 Microsoft 身份验证库（MSAL）。
services: active-directory
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: twhitney
ms.reviewer: shoatman
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: be8129de8b1c12965810bd5d9b5dfd1093e18d1c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667890"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>适用于 Android 的 ADAL 到 MSAL 迁移指南

本文重点介绍将使用 Azure Active Directory 身份验证库（ADAL）的应用程序迁移到使用 Microsoft 身份验证库（MSAL）所需的更改。

## <a name="difference-highlights"></a>差异突出显示

ADAL 适用于 Azure Active Directory 1.0 版终结点。 Microsoft 身份验证库（MSAL）适用于 Microsoft 标识平台（以前称为 Azure Active Directory v2.0 终结点）。 Microsoft 标识平台与 Azure Active Directory v1.0 的不同之处在于：

支持：
  - 组织标识（Azure Active Directory）
  - 非组织标识，如 Outlook.com、Xbox Live 等
  - （仅限 B2C）与 Google、Facebook、Twitter 和 Amazon 联合登录

- 与兼容的标准：
  - OAuth v2。0
  - OpenID Connect （OIDC）

MSAL 公共 API 引入了重要更改，包括：

- 用于访问令牌的新模型：
  - ADAL 通过表示服务器的 `AuthenticationContext`提供对令牌的访问。 MSAL 通过代表客户端的 `PublicClientApplication`提供对令牌的访问。 客户端开发人员无需为每个要与之交互的证书颁发机构创建新的 `PublicClientApplication` 实例。 只需要一个 `PublicClientApplication` 配置。
  - 除资源标识符外，还支持使用范围请求访问令牌。
  - 支持增量许可。 开发人员可以请求范围，因为用户在应用中访问更多的功能，包括在应用注册过程中未包括的功能。
  - 在运行时，将不再验证权限。 开发人员会在开发过程中声明 "已知的颁发机构" 列表。
- 令牌 API 更改：
  - 在 ADAL 中，`AcquireToken()` 首先发出无提示请求。 如果失败，它将发出交互式请求。 此行为导致某些开发人员仅依赖于 `AcquireToken`，从而导致用户在一段时间内意外提示输入凭据。 MSAL 要求开发人员在用户收到 UI 提示符时有意了解相关信息。
    - `AcquireTokenSilent` 始终导致无提示请求成功或失败。
    - `AcquireToken` 总是会导致通过 UI 提示用户的请求。
- MSAL 支持从默认浏览器或嵌入式 web 视图登录：
  - 默认情况下，将使用设备上的默认浏览器。 这允许 MSAL 使用已为一个或多个已登录帐户提供的身份验证状态（cookie）。 如果未提供身份验证状态，则在通过 MSAL 授权期间进行身份验证将导致为在同一浏览器中使用的其他 web 应用程序带来的好处而创建身份验证状态（cookie）。
- 新的异常模型：
  - 异常更清晰地定义了所发生错误的类型以及开发人员需要执行哪些操作来解决问题。
- MSAL 支持 `AcquireToken` 和 `AcquireTokenSilent` 调用的参数对象。
- MSAL 支持的声明性配置：
  - 客户端 ID，重定向 URI。
  - 嵌入的 vs 默认浏览器
  - 授权
  - HTTP 设置，如读取和连接超时

## <a name="your-app-registration-and-migration-to-msal"></a>应用注册和迁移到 MSAL

无需更改现有的应用注册即可使用 MSAL。 如果你想要利用增量/渐进式许可，你可能需要查看注册，以确定要增量请求的特定范围。 下面是有关范围和增量许可的详细信息。

在门户中的应用注册中，你将看到 " **API 权限**" 选项卡。这将提供应用当前配置为请求访问权限的 Api 和权限（作用域）的列表。 它还显示与每个 API 权限相关联的作用域名称列表。

### <a name="user-consent"></a>用户同意

使用 ADAL 和 AAD v1 终结点时，将在首次使用时授予用户对其拥有的资源的许可。 通过 MSAL 和 Microsoft 标识平台，可以以增量方式请求同意。 对于用户可以考虑高权限的权限，或在未提供权限的情况清晰说明的情况下，可以使用 "增量许可"。 在 ADAL 中，这些权限可能导致用户放弃登录到你的应用程序。

> [!TIP]
> 如果需要为用户提供附加的上下文，则建议在需要为用户提供更多权限的情况下使用增量许可。

### <a name="admin-consent"></a>管理员同意

组织管理员可以同意你的应用程序代表其组织的所有成员所需的权限。 某些组织仅允许管理员同意应用程序。 管理员同意要求你在应用注册中包含应用程序使用的所有 API 权限和作用域。

> [!TIP]
> 即使你可以使用 MSAL 请求范围来实现应用注册中未包含的内容，我们也建议你更新应用注册，使其包含用户可向其授予权限的所有资源和作用域。

## <a name="migrating-from-resource-ids-to-scopes"></a>从资源 Id 迁移到作用域

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>验证并请求首次使用时的所有权限的授权

如果你当前使用的是 ADAL 并且不需要使用增量许可，则开始使用 MSAL 的最简单方法是使用新的 `AcquireTokenParameter` 对象和设置资源 ID 值进行 `acquireToken` 请求。

> [!CAUTION]
> 不能同时设置作用域和资源 id。尝试同时设置这两个将导致 `IllegalArgumentException`。

 这将导致你使用相同的 v1 行为。 在应用注册过程中请求的所有权限都是在其第一次交互期间从用户请求的。

### <a name="authenticate-and-request-permissions-only-as-needed"></a>仅在需要时进行身份验证和请求权限

若要利用增量许可，请从应用注册中获取应用使用的权限（作用域）列表，并根据以下内容将它们组织成两个列表：

- 用户首次与应用进行交互时要请求的作用域。
- 与你的应用程序的一项重要功能关联的权限，你还需要向用户说明此功能。

组织作用域后，按要为其请求令牌的资源（API）来组织每个列表。 以及希望用户同时授权的任何其他作用域。

用于向 MSAL 发出请求的参数对象支持：

- `Scope`：要为其请求授权的作用域列表，并收到访问令牌。
- `ExtraScopesToConsent`：在请求另一资源的访问令牌时，你想要为其请求授权的作用域的其他列表。 通过此作用域列表，你可以最大程度地减少请求用户授权所需的次数。 这意味着更少的用户授权或许可提示。

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>从 AuthenticationContext 迁移到 PublicClientApplications

### <a name="constructing-publicclientapplication"></a>构造 PublicClientApplication

当你使用 MSAL 时，将实例化 `PublicClientApplication`。 此对象对应用标识建模，并用于向一个或多个权限发出请求。 使用此对象，你将配置客户端标识、重定向 URI、默认授权、是否使用设备浏览器与嵌入的 web 视图、日志级别等。

您可以使用 JSON 以声明方式配置此对象，您可以将其作为文件或存储提供给 APK 中的资源。

尽管此对象不是单一实例，但在内部，它使用共享 `Executors` 处理交互式和无提示请求。

### <a name="business-to-business"></a>企业到企业

在 ADAL 中，你请求的访问令牌的每个组织都需要 `AuthenticationContext`的单独实例。 在 MSAL 中，这不再是必需的。 你可以指定要在无提示或交互式请求中请求令牌的授权机构。

### <a name="migrate-from-authority-validation-to-known-authorities"></a>从颁发机构验证迁移到已知颁发机构

MSAL 没有用于启用或禁用证书颁发机构验证的标志。 颁发机构验证是 ADAL 中的一项功能，也是 MSAL 的早期版本中的一项功能，它可防止您的代码向可能的恶意机构请求标记。 MSAL 现在将检索 Microsoft 已知的颁发机构列表，并将该列表与你在配置中指定的颁发机构合并在一起。

> [!TIP]
> 如果你是 Azure 企业对消费者（B2C）用户，这意味着你不再需要禁用授权机构验证。 相反，请将每个受支持的 Azure AD B2C 策略作为 MSAL 配置中的颁发机构。

如果你尝试使用 Microsoft 无法识别的颁发机构，并且你的配置中未包含该机构，你将获得 `UnknownAuthorityException`。

### <a name="logging"></a>日志记录
你现在可以在配置中以声明方式配置日志记录，如下所示：

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>从用户信息迁移到帐户

在 ADAL 中，`AuthenticationResult` 提供了一个 `UserInfo` 对象，该对象用于检索有关经过身份验证的帐户的信息。 应用 "用户" 这一术语，它是一种使用人机或软件代理的方式，这使得难以传达某些应用是否支持具有多个帐户的单个用户（无论是人或软件代理）。

以银行帐户为例。 您可能在多个金融机构有多个帐户。 当你打开某个帐户时，你（用户）将为每个帐户颁发凭据（例如 ATM 卡 & PIN），这些凭据用于访问余额、帐单付款等。 这些凭据只能在颁发它们的金融机构使用。

如金融机构的帐户一样，使用凭据访问 Microsoft 标识平台中的帐户。 这些凭据是使用注册的，或由 Microsoft 颁发的。 或由 Microsoft 代表一家组织。

Microsoft 标识平台与金融机构的不同之处在于，在此类比中，Microsoft 标识平台提供了一个框架，使用户能够使用一个帐户及其关联的凭据来访问属于多个人和组织。 这就像能够使用一个银行颁发的卡，还可以使用另一个金融机构。 这是因为所涉及的所有组织都在使用 Microsoft 标识平台，这允许在多个组织中使用一个帐户。 下面是一个示例：

Sam 适用于 Contoso.com，但管理属于 Fabrikam.com 的 Azure 虚拟机。 要使 Sam 管理 Fabrikam 的虚拟机，他需要有权访问它们。 可以通过以下方式授予此访问权限：向 Fabrikam.com 添加 Sam 帐户，并向其帐户授予允许他处理虚拟机的角色。 这将通过 Azure 门户完成。

将 Sam 的 Contoso.com 帐户添加为 Fabrikam.com 的成员会导致在 Fabrikam Azure Active Directory 中为 Sam 创建新记录。 中 Sam 记录 Azure Active Directory 称为 "用户对象"。 在这种情况下，该用户对象将在 Contoso.com 中返回 Sam 的 user 对象。 Sam 的 Fabrikam 用户对象是 Sam 的本地表示形式，将用于在 Fabrikam.com 的上下文中存储与 Sam 关联的帐户的相关信息。 在 Contoso.com 中，Sam 的标题是高级 DevOps 顾问。 在 Fabrikam 中，Sam 的标题为 "承包商-虚拟机"。 在 Contoso.com 中，Sam 不负责管理虚拟机并对其进行授权。 在 Fabrikam.com 中，这是他唯一的工作职能。 但 Sam 仍只包含一组凭据来跟踪，这些凭据是 Contoso.com 颁发的凭据。

成功进行 `acquireToken` 调用后，你将看到对 `IAccount` 对象的引用，该对象可在以后 `acquireTokenSilent` 请求中使用。

### <a name="imultitenantaccount"></a>IMultiTenantAccount

如果你的应用程序从表示该帐户的每个租户访问有关该帐户的声明，则可以将 `IAccount` 对象强制转换为 `IMultiTenantAccount`。 此接口提供按租户 ID 键控的 `ITenantProfiles`的映射，使你能够从相对于当前帐户的请求令牌的每个租户中的帐户访问声明。

位于 `IAccount` 的根目录的声明和 `IMultiTenantAccount` 始终包含来自 home 租户的声明。 如果你尚未在 home 租户内请求令牌，此集合将为空。

## <a name="other-changes"></a>其他更改

### <a name="use-the-new-authenticationcallback"></a>使用新的 Authenticationcallback 传递给

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

在 ADAL 中，有一种类型的异常，`AuthenticationException`，其中包括用于检索 `ADALError` 枚举值的方法。
在 MSAL 中，有一个异常层次结构，并且每个都有其自己的一组关联的特定错误代码。

MSAL 异常列表

|异常  | 说明  |
|---------|---------|
| `MsalException`     | MSAL 引发的默认选中异常。  |
| `MsalClientException`     | 如果错误是客户端，则引发。 |
| `MsalArgumentException`     | 如果一个或多个输入参数无效，则引发该异常。 |
| `MsalClientException`     | 如果错误是客户端，则引发。 |
| `MsalServiceException`     | 如果错误是服务器端，则引发。 |
| `MsalUserCancelException`     | 如果用户取消了身份验证流，则引发此异常。  |
| `MsalUiRequiredException`     | 如果无法以静默方式刷新标记，则引发此异常。  |
| `MsalDeclinedScopeException`     | 如果服务器拒绝一个或多个请求的作用域，则引发此异常。  |
| `MsalIntuneAppProtectionPolicyRequiredException` | 如果资源启用了 MAMCA 保护策略，则引发此异常。 |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError to MsalException ErrorCode

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
