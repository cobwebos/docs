---
title: 错误和异常 (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL Android 应用程序中的错误和异常、条件访问与声明质询。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: c0b08a6c1a784216abe2bd562109dbb1586252c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119804"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>处理适用于 Android 的 MSAL 中的异常和错误

Microsoft 身份验证库 (MSAL) 中的异常旨在帮助应用开发人员对其应用程序进行故障排除。 异常消息未经本地化。

处理异常和错误时，可以使用异常类型本身和错误代码来区分不同的异常。  有关错误代码的列表，请参阅[身份验证和授权错误代码](reference-aadsts-error-codes.md)。

在登录体验期间，可能会遇到有关许可、条件访问（MFA、设备管理、基于位置的限制）、令牌颁发和兑换以及用户属性的错误。


|错误类 | 原因/错误字符串| 处理方式 |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`：用于兑换访问令牌的刷新令牌无效、已过期或已撤销。 此异常可能是由于密码更改导致的。 </li><li>`NO_TOKENS_FOUND`：访问令牌不存在，并且找不到用于兑换访问令牌的刷新令牌。</li> <li>必需的升级<ul><li>MFA</li><li>缺少声明</li></ul></li><li>被条件访问阻止（例如，需要安装[身份验证代理](./brokered-auth.md)）</li><li>`NO_ACCOUNT_FOUND`：缓存中没有可用于无提示身份验证的帐户。</li></ul> |调用 `acquireToken()` 以提示用户输入用户名和密码，可能还需要同意并执行多重身份验证。|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`：用户或服务器尚未接受所有作用域。 如果请求的作用域不受支持、无法识别或不支持某个特定帐户，服务器可能会拒绝该作用域。 </li></ul>| 开发人员应该决定是继续使用授予的作用域进行身份验证还是结束身份验证过程。 选项仅为已授予的范围重新提交获取令牌请求，并通过传递 `silentParametersForGrantedScopes` 和调用 `acquireTokenSilent` 提供有关已授予了哪些权限的提示。 |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`：请求中缺少必需的参数、包含无效的参数、多次包含某个参数，或格式不正确。 </li><li>`SERVICE_NOT_AVAILABLE`：表示由于服务中断而导致的 500/503/506 错误代码。 </li><li>`UNAUTHORIZED_REQUEST`：客户端无权请求授权代码。</li><li>`ACCESS_DENIED`：资源所有者或授权服务器拒绝了请求。</li><li>`INVALID_INSTANCE`：`AuthorityMetadata` 验证失败</li><li>`UNKNOWN_ERROR`：对服务器的请求失败，但没有从服务返回错误和 `error_description`。</li><ul>| 此异常类表示与服务通信时的错误，可以来自授权终结点或令牌终结点。 MSAL 从服务器响应中读取错误和 error_description。 通常，通过在代码中或在应用注册门户中修复应用配置来解决这些错误。 服务中断很少会触发此警告，只有等待服务恢复才能缓解此警告。  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`：找到多个缓存项，sdk 无法从缓存中识别正确的访问或刷新令牌。 此异常通常表示 sdk 中存在用于存储令牌的错误，或者在无提示请求中未提供颁发机构，并且找到多个匹配的令牌。 </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`：设备上没有可用的活动网络。 </li><li>`JSON_PARSE_FAILURE`：sdk 未能分析 JSON 格式。</li><li>`IO_ERROR`：发生 `IOException`，可能是设备或网络错误。 </li><li>`MALFORMED_URL`：Url 格式不正确。 可能是在构造身份验证请求、颁发机构或重定向 URI 时导致的。 </li><li>`UNSUPPORTED_ENCODING`：设备不支持编码。 </li><li>`NO_SUCH_ALGORITHM`：不支持用于生成 [PKCE](https://tools.ietf.org/html/rfc7636) 质询的算法。 </li><li>`INVALID_JWT`：`JWT` 服务器返回的值无效、为空或格式错误。 </li><li>`STATE_MISMATCH`：授权响应的状态与授权请求中的状态不匹配。 对于授权请求，sdk 将验证重定向返回的状态和请求中发送的状态。 </li><li>`UNSUPPORTED_URL`：Url 不受支持，无法执行 ADFS 证书颁发机构验证。 </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`：该颁发机构不支持颁发机构验证。 sdk 支持 B2C 权限，但不支持 B2C 颁发机构验证。 只支持已知主机。 </li><li>`CHROME_NOT_INSTALLED`：设备上未安装 Chrome。 sdk 使用 chrome 自定义选项卡执行授权请求（如果可用），并回退到 chrome 浏览器。 </li><li>`USER_MISMATCH`：获取令牌请求中提供的用户与从服务器返回的用户不匹配。</li></ul>|此异常类表示库本地的常规错误。 可以通过更正请求来处理这些异常。|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`：用户启动了交互流，并在收到返回的令牌之前取消了请求。 </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`：必须为 `acquireTokenSilent` 指定颁发机构。</li></ul>|开发人员可以通过以下方式缓解这些错误：更正参数并确保完成了交互式验证、完成回调、设置作用域，并提供了具有有效 ID 的帐户。|


## <a name="catching-errors"></a>捕获错误

下面的代码片段显示了在无提示 `acquireToken` 调用的情况下捕获错误的示例。

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>后续步骤 

详细了解[日志记录错误](./msal-logging.md?tabs=android)