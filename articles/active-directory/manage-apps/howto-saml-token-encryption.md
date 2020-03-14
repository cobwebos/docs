---
title: Azure Active Directory 中的 SAML 令牌加密
description: 了解如何配置 Azure Active Directory SAML 令牌加密。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0082d841faf22745e609d38444f4a97553b3c867
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365860"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>如何：配置 Azure AD SAML 令牌加密

> [!NOTE]
> 令牌加密是一项 Azure Active Directory (Azure AD) 高级功能。 若要详细了解 Azure AD 版本、功能和定价，请参阅 [Azure AD 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

借助 SAML 令牌加密，可以在支持此功能的应用程序中使用加密的 SAML 断言。 为应用程序配置此功能后，Azure AD 将使用从 Azure AD 存储的证书中获取的公钥，来加密它针对该应用程序发出的 SAML 断言。 应用程序必须先使用匹配的私钥来解密该令牌，然后才能将该令牌用作登录用户的身份验证证据。

加密 Azure AD 与应用程序之间的 SAML 断言可以进一步保证令牌内容不被截获，个人或公司数据不会泄密。

即使不使用令牌加密，Azure AD SAML 令牌也永远不会以明文形式在网络中传递。 Azure AD 要求通过加密的 HTTPS/TLS 通道进行令牌请求/响应交换，使 IDP、浏览器与应用程序之间的通信通过加密的链接发生。 请根据自己的场合，在令牌加密所带来的价值与管理更多证书所造成的开销之间进行权衡。   

若要配置令牌加密，需要将包含公钥的 X.509 证书文件上传到代表应用程序的 Azure AD 应用程序对象中。 若要获取 X.509 证书，可以从应用程序本身下载，或者从应用程序供应商处获取（如果应用程序供应商提供加密密钥）；如果应用程序要求你提供私钥，可以使用加密工具、上传到应用程序密钥存储的私钥部分，以及上传到 Azure AD 的匹配公钥证书来创建该私钥。

Azure AD 使用 AES-256 加密 SAML 断言数据。

## <a name="configure-saml-token-encryption"></a>配置 SAML 令牌加密

若要配置 SAML 令牌加密，请执行以下步骤：

1. 获取与应用程序中配置的私钥匹配的公钥证书。

    创建用于加密的非对称密钥对。 或者，如果应用程序提供用于加密的公钥，请遵照应用程序的说明下载 X.509 证书。

    公钥应以 .cer 格式存储在 X.509 证书文件中。

    如果应用程序使用你为实例创建的密钥，请遵照应用程序提供的说明安装私钥，应用程序将使用它来解密从 Azure AD 租户获取的令牌。

1. 将证书添加到 Azure AD 中的应用程序配置。

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>在 Azure 门户中配置令牌加密

可以在 Azure 门户中将公共证书添加到应用程序配置。

1. 转到 [Azure 门户](https://portal.azure.com)。

1. 转到“Azure Active Directory”>“企业应用程序”边栏选项卡，然后选择要为其配置令牌加密的应用程序。

1. 在该应用程序的页面上，选择“令牌加密”。

    ![Azure 门户中的令牌加密选项](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > “令牌加密”选项仅适用于已在 Azure 门户的“企业应用程序”边栏选项卡中设置的、来自应用程序库或非库应用的 SAML 应用程序。 对于其他应用程序，此菜单选项将会禁用。 对于通过 Azure 门户中的“应用注册”体验注册的应用程序，可以通过 Microsoft Graph 或 PowerShell 使用应用程序清单配置 SAML 令牌加密。

1. 在“令牌加密”页上，选择“导入证书”导入包含公共 X.509 证书的 .cer 文件。

    ![导入包含 X.509 证书的 .cer 文件](./media/howto-saml-token-encryption/import-certificate-small.png)

1. 导入证书并配置要在应用程序端使用的私钥后，选择指纹状态旁边的“...”激活加密，然后从下拉菜单选项中选择“激活令牌加密”。

1. 选择“是”确认激活令牌加密证书。

1. 确认针对应用程序发出的 SAML 断言已加密。

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>在 Azure 门户中停用令牌加密

1. 在 Azure 门户中转到“Azure Active Directory”>“企业应用程序”，然后选择已启用 SAML 令牌加密的应用程序。

1. 在应用程序的页面上选择“令牌加密”，找到该证书，然后选择“...”选项显示下拉菜单。

1. 选择“停用令牌加密”。

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>使用图形 API、PowerShell 或应用清单配置 SAML 令牌加密

加密证书存储在 Azure AD 中的应用程序对象内，带有 `encrypt` 用法标记。 可以配置多个加密证书，正在用于加密令牌的证书由 `tokenEncryptionKeyID` 属性标识。

需要使用应用程序的对象 ID 才能通过 Microsoft Graph API 或 PowerShell 配置令牌加密。 可以编程方式找到此值，或者在 Azure 门户中转到应用程序的“属性”页，然后记下“对象 ID”值。

使用 Graph、PowerShell 或应用程序清单配置 keyCredential 时，应生成一个用作 keyId 的 GUID。

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>使用 Microsoft Graph 配置令牌加密

1. 将应用程序的 `keyCredentials` 更新为用于加密的 X.509 证书。 以下示例演示如何执行此操作。

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. 标识正在用于加密令牌的加密证书。 以下示例演示如何执行此操作。

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>使用 PowerShell 配置令牌加密

1. 使用最新 Azure AD PowerShell 模块连接到你的租户。

1. 使用 **[azure 应用程序](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** 命令设置令牌加密设置。

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. 使用以下命令读取令牌加密设置。

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>使用应用程序清单配置令牌加密

1. 在 Azure 门户中，转到“Azure Active Directory”>“应用注册”。

1. 从下拉菜单中选择“所有应用”以显示所有应用，然后选择要配置的企业应用程序。

1. 在该应用程序的页面上，选择“清单”以编辑[应用程序清单](../develop/reference-app-manifest.md)。

1. 设置 `tokenEncryptionKeyId` 属性的值。

    以下示例演示了配置有两个加密证书的应用程序清单，其中第二个证书使用 tokenEnryptionKeyId，已选作活动的证书。

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>后续步骤

* 了解 [Azure AD 如何使用 SAML 协议](../develop/active-directory-saml-protocol-reference.md)
* 了解 [Azure AD 中的 SAML 令牌](../develop/reference-saml-tokens.md)的格式、安全特征和内容