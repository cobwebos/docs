---
title: Android MSAL 配置文件 |Microsoft
titleSuffix: Microsoft identity platform
description: Android Microsoft 身份验证库（MSAL）配置文件概述，它表示 Azure Active Directory 中的应用程序配置。
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2d366a48adf536276697959be3418f36e10d8ae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424391"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Android Microsoft 身份验证库配置文件

Android Microsoft 身份验证库（MSAL）附带了一个[默认的配置 JSON 文件](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)，您可以自定义该文件以定义公共客户端应用程序的行为，例如默认证书颁发机构、您将使用的授权机构等。

本文将帮助你了解配置文件中的各种设置，以及如何在基于 MSAL 的应用中指定要使用的配置文件。

## <a name="configuration-settings"></a>配置设置

### <a name="general-settings"></a>常规设置

| 属性 | 数据类型 | 需要 | 说明 |
|-----------|------------|-------------|-------|
| `client_id` | String | 是 | [应用程序注册页](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中的应用的客户端 ID |
| `redirect_uri`   | String | 是 | [应用程序注册页](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)的重定向 URI |
| `authorities` | 列出\<颁发机构 > | 否 | 应用所需的权限列表 |
| `authorization_user_agent` | AuthorizationAgent （enum） | 否 | 可能的值： `DEFAULT`、`BROWSER``WEBVIEW` |
| `http` | HttpConfiguration | 否 | 配置 `HttpUrlConnection` `connect_timeout` 和 `read_timeout` |
| `logging` | LoggingConfiguration | 否 | 指定日志记录的详细级别。 可选配置包括： `pii_enabled`，它采用布尔值，`log_level`，它采用 `ERROR`、`WARNING`、`INFO`或 `VERBOSE`。 |

### <a name="client_id"></a>client_id

注册应用程序时创建的客户端 ID 或应用 ID。

### <a name="redirect_uri"></a>redirect_uri

注册应用程序时注册的重定向 URI。 如果重定向 URI 为 broker 应用，请参阅[公共客户端应用的重定向 uri](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) ，以确保对代理应用使用正确的重定向 uri 格式。

### <a name="authorities"></a>授权

你已知并信任的颁发机构的列表。 除了本文中列出的权威机构外，MSAL 还会查询 Microsoft 以获取 Microsoft 已知的云和颁发机构的列表。 在此颁发机构列表中，指定机构的类型和任何其他可选参数（如 `"audience"`），该参数应与基于应用注册的应用的受众相符。 下面是一个颁发机构示例列表：

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>将 AAD 颁发机构 & 受众映射到 Microsoft 标识平台终结点

| 类型 | 受众 | 租户 ID | Authority_Url | 生成的终结点 | 说明 |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | https://login.microsoftonline.com/common | `common` 是帐户所在位置的租户别名。 如特定的 Azure Active Directory 租户或 Microsoft 帐户系统。 |
| AAD | AzureADMyOrg | contoso.com | | https://login.microsoftonline.com/contoso.com | 只有 contoso.com 中的帐户才能获取令牌。 任何经过验证的域或租户 GUID 都可以用作租户 ID。 |
| AAD | AzureADMultipleOrgs | | | https://login.microsoftonline.com/organizations | 只有 Azure Active Directory 帐户才能与此终结点一起使用。 Microsoft 帐户可以是组织的成员。 若要为组织中的资源使用 Microsoft 帐户获取令牌，请指定要从中进行令牌的组织租户。 |
| AAD | PersonalMicrosoftAccount | | | https://login.microsoftonline.com/consumers | 只有 Microsoft 帐户才能使用此终结点。 |
| B2C | | | 查看生成的终结点 | https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/ | 只有 contoso.onmicrosoft.com 租户中的帐户才能获取令牌。 在此示例中，B2C 策略是授权 URL 路径的一部分。 |

> [!NOTE]
> 无法在 MSAL 中启用和禁用授权机构验证。
> 颁发机构是通过配置指定的开发人员或通过元数据从 Microsoft 知道的。
> 如果 MSAL 接收到未知颁发机构的令牌请求，则 `UnknownAuthority` 结果的 `MsalClientException` 类型。

#### <a name="authority-properties"></a>颁发机构属性

| 属性 | 数据类型  | 需要 | 说明 |
|-----------|-------------|-----------|--------|
| `type` | String | 是 | 镜像应用程序的目标受众或帐户类型。 可能的值： `AAD`、`B2C` |
| `audience` | 对象 | 否 | 仅当 type =`AAD`时才适用。 指定应用的目标标识。 使用应用注册中的值 |
| `authority_url` | String | 是 | 仅当 type =`B2C`时才是必需的。 指定应用应使用的授权 URL 或策略  |
| `default` | boolean | 是 | 指定一个或多个颁发机构时，需要单个 `"default":true`。 |

#### <a name="audience-properties"></a>访问群体属性

| 属性 | 数据类型  | 需要 | 说明 |
|-----------|-------------|------------|-------|
| `type` | String | 是 | 指定应用要以其为目标的受众。 可能的值： `AzureADandPersonalMicrosoftAccount`、`PersonalMicrosoftAccount`、`AzureADMultipleOrgs``AzureADMyOrg` |
| `tenant_id` | String | 是 | 仅在 `"type":"AzureADMyOrg"`时是必需的。 对于其他 `type` 值是可选的。 这可以是租户域（如 `contoso.com`）或租户 ID （如 `72f988bf-86f1-41af-91ab-2d7cd011db46`） |

### <a name="authorization_user_agent"></a>authorization_user_agent

指示在登录帐户或授权访问资源时，是使用嵌入的 web 视图还是设备上的默认浏览器。

可能的值：
- `DEFAULT`：更倾向于系统浏览器。 如果浏览器在设备上不可用，则使用嵌入的 web 视图。
- `WEBVIEW`：使用嵌入的 web 视图。
- `BROWSER`：在设备上使用默认浏览器。

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

对于支持多个国家云的客户端，指定 `true`。 然后，Microsoft 标识平台会在授权和令牌兑换期间自动重定向到正确的国内云。 可以通过检查与 `AuthenticationResult`关联的授权机构来确定已登录帐户的国家/地区。 请注意，`AuthenticationResult` 不提供要为其请求令牌的资源的国家云特定的终结点地址。

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

一个布尔值，指示是否正在使用与 Microsoft 身份 broker 兼容的 broker 重定向 URI。 如果你不想在应用中使用代理，则设置为 `false`。

如果使用的是 "访问群体" 设置为 "`"MicrosoftPersonalAccount"`" 的 AAD 颁发机构，则不会使用 broker。

### <a name="http"></a>http

配置 HTTP 超时的全局设置，例如：

| 属性 | 数据类型 | 需要 | 说明 |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | 否 | 时间（毫秒） |
| `read_timeout` | int | 否 | 时间（毫秒） |

### <a name="logging"></a>logging

以下全局设置用于日志记录：

| 属性 | 数据类型  | 需要 | 说明 |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | 否 | 是否发出个人数据 |
| `log_level`   | boolean | 否 | 要输出的日志消息 |
| `logcat_enabled` | boolean | 否 | 除日志记录接口外是否输出到日志 cat |

### <a name="account_mode"></a>account_mode

指定在应用中一次可以使用的帐户数。 可能的值包括：

- `MULTIPLE`（默认）
- `SINGLE`

使用与此设置不匹配的帐户模式构造 `PublicClientApplication` 将导致异常。

有关单个和多个帐户之间的差异的详细信息，请参阅[单个和多个帐户应用](single-multi-account.md)。

### <a name="browser_safelist"></a>browser_safelist

与 MSAL 兼容的浏览器的允许列表。 这些浏览器会正确地处理重定向到自定义意向。 您可以向此列表中添加。 默认值为默认值，如下所示。
``
## <a name="the-default-msal-configuration-file"></a>默认 MSAL 配置文件

MSAL 附带的默认 MSAL 配置如下所示。 你可以在[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)上看到最新版本。

此配置由您提供的值来补充。 您提供的值将覆盖默认值。

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>基本配置示例

下面的示例演示了一个基本配置，该配置指定了客户端 ID、重定向 URI、是否注册了 broker 重定向和一个授权列表。

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>如何使用配置文件

1. 创建配置文件。 建议在 `res/raw/auth_config.json`中创建自定义配置文件。 不过，您可以将它放在您需要的任何位置。
2. 当你构造 `PublicClientApplication`时，请告诉 MSAL 在何处查找你的配置。 例如：

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
