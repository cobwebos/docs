---
title: 使用媒体服务 .NET SDK 配置内容密钥授权策略 | Microsoft Docs
description: 了解如何使用媒体服务 .NET SDK 配置内容密钥的授权策略。
services: media-services
documentationcenter: ''
author: mingfeiy
manager: cfowler
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 531b90b905df8549846c6027fe547521d16cf082
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868494"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>动态加密：配置内容密钥授权策略
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>概述
 可以使用 Azure 媒体服务传送受高级加密标准 (AES)（使用 128 位加密密钥）或受 [PlayReady 数字版权管理 (DRM)](https://www.microsoft.com/playready/overview/) 保护的 MPEG-DASH 流、平滑流式处理流和 HTTP 实时流式处理 (HLS) 流。 使用媒体服务，还可以传送通过 Widevine DRM 加密的 DASH 流。 PlayReady 和 Widevine 都是按通用加密 (ISO/IEC 23001-7 CENC) 规范加密的。

媒体服务还提供了一个密钥/许可证传送服务，客户端可从中获取 AES 密钥或 PlayReady/Widevine 许可证，用于播放加密的内容。

如果希望媒体服务对某个资产进行加密，则需要将加密密钥（CommonEncryption 或 EnvelopeEncryption）与该资产相关联。 有关详细信息，请参阅[使用 .NET 创建内容密钥](media-services-dotnet-create-contentkey.md)。 还需要配置密钥的授权策略（如本文中所述）。

当播放器请求流时，媒体服务将使用指定的密钥通过 AES 或 DRM 加密来动态加密内容。 为解密流，播放器从密钥传送服务请求密钥。 为了确定用户是否被授权获取密钥，服务将评估你为密钥指定的授权策略。

媒体服务支持通过多种方式对发出密钥请求的用户进行身份验证。 内容密钥授权策略可以有一个或多个授权限制。 选项为“开放”或“令牌限制”。 令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用简单 Web 令牌 ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 格式和 JSON Web 令牌 ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) 格式的令牌。

媒体服务不提供 STS。 可以创建自定义 STS 或使用 Azure 访问控制服务来颁发令牌。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及在令牌限制配置中指定的颁发声明进行签名（如本文所述）。 如果令牌有效，并且令牌中的声明与为内容密钥配置的声明相匹配，则媒体服务密钥传送服务会将加密密钥返回到客户端。

有关详细信息，请参阅以下文章：

- [JWT 令牌身份验证](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [将基于 Azure 媒体服务 OWIN MVC 的应用与 Azure Active Directory 相集成，并基于 JWT 声明限制内容密钥传送](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>需要注意的一些事项
* 创建媒体服务帐户时，会将一个处于“已停止”状态的默认流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，流式处理终结点必须处于“正在运行”状态。 
* 资产必须包含一组自适应比特率 MP4 或自适应比特率平滑流文件。 有关详细信息，请参阅[对资产进行编码](media-services-encode-asset.md)。
* 使用 AssetCreationOptions.StorageEncrypted 选项上传资产并对其进行编码。
* 如果打算创建需要相同策略配置的多个内容密钥，建议创建单个授权策略，并将其重复用于多个内容密钥。
* 密钥传送服务将 ContentKeyAuthorizationPolicy 及其相关对象（策略选项和限制）缓存 15 分钟。 可以创建 ContentKeyAuthorizationPolicy 并指定使用令牌限制，对其进行测试，然后更新策略以开放限制。 在策略切换到策略的开放版本之前，此过程需要花费大约 15 分钟。
* 如果添加或更新资产的传送策略，则必须删除现有的定位符并创建新的定位符。
* 目前，无法对渐进式下载进行加密。
* 媒体服务流式处理终结点将预检响应中 CORS 的“Access-Control-Allow-Origin”标头的值设置为通配符“\*”。 此值适用于大多数播放器，其中包括 Azure Media Player、Roku、JWPlayer 等。 但是，这不适用于一些使用 dashjs 的播放器，因为将凭据模式设置为“包含”之后，dashjs 中的 XMLHttpRequest 不允许将通配符“\*”作为“Access-Control-Allow-Origin”的值。 作为 dashjs 中这一限制的解决办法，如果将客户端承载在单个域中，则媒体服务可以指定预检响应标头中的域。 若需帮助，请通过 Azure 门户开具支持票证。

## <a name="aes-128-dynamic-encryption"></a>AES-128 动态加密
### <a name="open-restriction"></a>开放限制
开放限制意味着系统会将密钥传送到发出密钥请求的任何用户。 此限制可能适用于测试用途。

以下示例创建开放授权策略，并将其添加到内容密钥：
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>令牌限制
本部分介绍如何创建内容密钥授权策略，以及如何将其与内容密钥相关联。 授权策略描述了必须达到什么授权要求才能确定用户是否有权接收密钥。 例如，“验证密钥”列表是否包含为令牌签名时使用的密钥？

若要配置令牌限制选项，需要使用 XML 来描述令牌的授权要求。 令牌限制配置 XML 必须符合以下 XML 架构：
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含为令牌签名时使用的密钥。 颁发者是颁发令牌的 STS。 受众（有时称为范围）描述该令牌的意图，或者令牌授权访问的资源。 媒体服务密钥交付服务会验证令牌中的这些值是否与模板中的值匹配。

使用用于 .NET 的媒体服务 SDK 时，可以使用 TokenRestrictionTemplate 类来生成限制令牌。
以下示例创建包含令牌限制的授权策略。 在此示例中，客户端必须出示令牌，其中包含：签名密钥 (VerificationKey)、令牌颁发者和必需的声明。
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>测试令牌
若要获取用于密钥授权策略的基于令牌限制的测试令牌，请执行以下操作：
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>PlayReady 动态加密
可以使用媒体服务配置相应的权限和限制，这样当用户尝试播放受保护的内容时，PlayReady DRM 运行时就会强制实施这些权限和限制。 

使用 PlayReady 保护内容时，需要在授权策略中指定的项目之一是用于定义 [PlayReady 许可证模板](media-services-playready-license-template-overview.md)的 XML 字符串。 在用于 .NET 的媒体服务 SDK 中，PlayReadyLicenseResponseTemplate 和 PlayReadyLicenseTemplate 类有助于定义 PlayReady 许可证模板。

若要了解如何使用 PlayReady 和 Widevine 来加密内容，请参阅[使用 PlayReady 和/或 Widevine 动态通用加密](media-services-protect-with-playready-widevine.md)。

### <a name="open-restriction"></a>开放限制
开放限制意味着系统会将密钥传送到发出密钥请求的任何用户。 此限制可能适用于测试用途。

以下示例创建开放授权策略，并将其添加到内容密钥：

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>令牌限制
若要配置令牌限制选项，需要使用 XML 来描述令牌的授权要求。 令牌限制配置 XML 必须遵循“[令牌限制架构](#token-restriction-schema)”部分所示的 XML 架构。

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

若要获取基于令牌限制（用于密钥授权策略）的测试令牌，请参阅“[测试令牌](#test-token)”部分。 

## <a id="types"></a>定义 ContentKeyAuthorizationPolicy 时使用的类型
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>后续步骤
现在已配置内容密钥的授权策略，可以查看[配置资产传送策略](media-services-dotnet-configure-asset-delivery-policy.md)了。

