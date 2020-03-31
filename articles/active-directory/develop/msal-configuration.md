---
title: Android MSAL 配置文件 | Azure
titleSuffix: Microsoft identity platform
description: Android Microsoft 身份验证库 (MSAL) 配置文件的概述。该配置文件代表应用程序在 Azure Active Directory 中的配置。
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
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050374"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Android Microsoft 身份验证库配置文件

Android Microsoft 身份验证库 (MSAL) 随附了一个[默认的配置 JSON 文件](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)，对此文件进行自定义可以定义公共客户端应用在默认颁发机构、要使用的颁发机构等方面的行为。

本文将帮助你了解该配置文件中的各项设置，以及如何指定要在基于 MSAL 的应用中使用的配置文件。

## <a name="configuration-settings"></a>配置设置

### <a name="general-settings"></a>常规设置

| properties | 数据类型 | 必选 | 说明 |
|-----------|------------|-------------|-------|
| `client_id` | String | 是 | [应用程序注册页](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中的应用客户端 ID |
| `redirect_uri`   | String | 是 | [应用程序注册页](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中的应用重定向 URI |
| `authorities` | List\<Authority> | 否 | 应用所需的颁发机构列表 |
| `authorization_user_agent` | AuthorizationAgent（枚举） | 否 | 可能的值：`DEFAULT`、`BROWSER`、`WEBVIEW` |
| `http` | HttpConfiguration | 否 | 配置`HttpUrlConnection``connect_timeout`和`read_timeout` |
| `logging` | LoggingConfiguration | 否 | 指定日志记录的详细级别。 可选配置包括：采用布尔值的 `pii_enabled`，以及采用 `ERROR`、`WARNING`、`INFO` 或 `VERBOSE` 的 `log_level`。 |

### <a name="client_id"></a>client_id

注册应用程序时创建的客户端 ID 或应用 ID。

### <a name="redirect_uri"></a>redirect_uri

注册应用程序时注册的重定向 URI。 如果重定向 URI 属于某个中介应用，请参阅[公共客户端应用的重定向 URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps)，确保对中介应用使用正确的重定向 URI 格式。

### <a name="authorities"></a>authorities

你已知且信任的颁发机构列表。 除了此处所列的颁发机构以外，MSAL 还会查询 Microsoft，以获取 Microsoft 已知的云和颁发机构列表。 在此颁发机构列表中，指定颁发机构的类型，并根据应用的注册指定与应用受众相符的任何其他可选参数（例如 `"audience"`）。 下面是颁发机构列表的示例：

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

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>将 AAD 颁发机构和受众映射到 Microsoft 标识平台终结点

| 类型 | 读者 | 租户 ID | Authority_Url | 生成的终结点 | 说明 |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADand个人微软帐户 | | | `https://login.microsoftonline.com/common` | `common`是帐户所在的租户别名。 例如特定的 Azure 活动目录租户或 Microsoft 帐户系统。 |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | 只有 contoso.com 中的帐户才能获取令牌。 任何已验证的域或租户 GUID 都可用作租户 ID。 |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | 在此终结点上只能使用 Azure Active Directory 帐户。 Microsoft 帐户可以是组织的成员。 若要使用 Microsoft 帐户获取组织中资源的令牌，请指定要从中获取令牌的组织租户。 |
| AAD | 个人微软帐户 | | | `https://login.microsoftonline.com/consumers` | 只有 Microsoft 帐户才能使用此终结点。 |
| B2C | | | 参阅“生成的终结点” | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | 只有contoso.onmicrosoft.com租户中的帐户才能获取令牌。 在此示例中，B2C 策略是颁发机构 URL 路径的一部分。 |

> [!NOTE]
> 在 MSAL 中无法启用和禁用授权机构验证。
> 颁发机构是开发人员已知的、通过配置指定的颁发机构，或者是 Microsoft 已知的、通过元数据指定的颁发机构。
> 如果 MSAL 收到了向未知颁发机构获取令牌的请求，将导致 `UnknownAuthority` 类型的 `MsalClientException`。

#### <a name="authority-properties"></a>颁发机构属性

| properties | 数据类型  | 必选 | 说明 |
|-----------|-------------|-----------|--------|
| `type` | String | 是 | 镜像应用面向的受众或帐户类型。 可能的值：`AAD`、`B2C` |
| `audience` | Object | 否 | 仅当 type=`AAD` 时才适用。 指定应用面向的标识。 使用应用注册中的值 |
| `authority_url` | String | 是 | 仅当 type =`B2C` 时才是必需的。 指定应用应该使用的颁发机构 URL 或策略  |
| `default` | boolean | 是 | 指定了一个或多个颁发机构时，需要指定单个 `"default":true`。 |

#### <a name="audience-properties"></a>受众属性

| properties | 数据类型  | 必选 | 说明 |
|-----------|-------------|------------|-------|
| `type` | String | 是 | 指定应用要面向的受众。 可能的值： `AzureADandPersonalMicrosoftAccount` `PersonalMicrosoftAccount`、 `AzureADMultipleOrgs`、 、`AzureADMyOrg` |
| `tenant_id` | String | 是 | 仅当指定 `"type":"AzureADMyOrg"` 时才是必需的。 如果指定其他 `type` 值，则是可选的。 这可以是类似于 `contoso.com` 的租户域，或类似于 `72f988bf-86f1-41af-91ab-2d7cd011db46` 的租户 ID |

### <a name="authorization_user_agent"></a>authorization_user_agent

指示在登录到某个帐户或授权访问资源时，是要使用嵌入式 Web 视图，还是使用设备上的默认浏览器。

可能的值：
- `DEFAULT`：首选系统浏览器。 如果设备上未提供浏览器，则使用嵌入式 Web 视图。
- `WEBVIEW`：使用嵌入式 Web 视图。
- `BROWSER`：使用设备上的默认浏览器。

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

对于支持多个国家云的客户端，请指定 `true`。 然后，在授权和令牌兑换期间，Microsoft 标识平台会自动重定向到正确的国家云。 可以通过检查与 `AuthenticationResult` 关联的颁发机构来确定登录帐户的国家云。 请注意，`AuthenticationResult` 不提供要请求其令牌的资源的国家云特定的终结点地址。

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

一个布尔值，指示是否正在使用 Microsoft 标识中介兼容的中介内重定向 URI。 如果不想要在应用中使用中介，请设置为 `false`。

如果您使用的 AAD 颁发机构将访问对象设置为`"MicrosoftPersonalAccount"`，则不会使用代理。

### <a name="http"></a>http

配置全局 HTTP 超时设置，例如：

| properties | 数据类型 | 必选 | 说明 |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | 否 | 时间（毫秒） |
| `read_timeout` | int | 否 | 时间（毫秒） |

### <a name="logging"></a>logging

以下全局设置用于日志记录：

| properties | 数据类型  | 必选 | 说明 |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | 否 | 是否发出个人数据 |
| `log_level`   | boolean | 否 | 要输出的日志消息 |
| `logcat_enabled` | boolean | 否 | 除了输出到日志记录界面以外，是否还要输出到 logcat |

### <a name="account_mode"></a>account_mode

指定在应用中一次可以使用多少个帐户。 可能的值包括：

- `MULTIPLE`（默认值）
- `SINGLE`

使用与此设置不匹配的帐户模式构造 `PublicClientApplication` 会导致异常。

有关单个帐户与多个帐户之间的差异的详细信息，请参阅[单帐户和多帐户应用](single-multi-account.md)。

### <a name="browser_safelist"></a>browser_safelist

与 MSAL 兼容的浏览器的允许列表。 这些浏览器可根据自定义意图正确处理重定向。 可在此列表中添加内容。 默认值在默认配置中提供，如下所示。
``
## <a name="the-default-msal-configuration-file"></a>默认的 MSAL 配置文件

下面显示了 MSAL 随附的默认 MSAL 配置。 可以在 [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) 上查看最新版本。

此配置由提供的值补充。 提供的值将替代默认值。

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

以下示例演示了一个基本配置，该配置指定了客户端 ID、重定向 URI、是否注册了中介重定向，以及颁发机构列表。

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

1. 创建配置文件。 建议在 `res/raw/auth_config.json` 中创建自定义配置文件。 但可以将此文件放在所需的任意位置。
2. 构造 `PublicClientApplication` 时，请告诉 MSAL 要在何处查找配置。 例如：

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
