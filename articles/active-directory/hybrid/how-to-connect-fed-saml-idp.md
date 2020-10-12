---
title: Azure AD Connect：使用 SAML 2.0 标识提供者进行单一登录 - Azure
description: 本文档介绍使用符合 SAML 2.0 的 Idp 进行单一登录。
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd95d01c9b49bb6002c00c805dc82dcf30941e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295030"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>使用 SAML 2.0 标识提供者 (IdP) 进行单一登录

本文档介绍有关将基于符合 SAML 2.0 的 SP-Lite 配置文件的标识提供者作为首选安全令牌服务 (STS)/标识提供者的信息。 如果已经在本地拥有可以使用 SAML 2.0 访问的用户目录和密码存储，此方案将非常有用。 可使用这个现有的用户目录登录到 Microsoft 365 和其他受 Azure AD 保护的资源。 SAML 2.0 SP-Lite 配置文件基于基于广泛使用的安全断言标记语言 (SAML) 联合标识标准，可提供登录和属性交换框架。

>[!NOTE]
>如需经测试可用于 Azure AD 的第三方 Idp 的列表，请参阅 [Azure AD 联合身份验证兼容性列表](how-to-connect-fed-compatibility.md)

Microsoft 支持此登录体验，因为它将 Microsoft 云服务（如 Microsoft 365）与正确配置的 SAML 2.0 基于配置文件的 IdP 集成。 SAML 2.0 标识提供者是第三方产品，因此 Microsoft 不会对与之相关的部署、配置、故障排除最佳实践提供支持。 正确配置后，可以使用 Microsoft Connectivity Analyzer 工具来测试是否已正确配置了与 SAML 2.0 标识提供者的集成，下文将详细介绍。 有关基于 SAML 2.0 SP-Lite 配置文件的标识提供者的详细信息，请咨询其提供组织。

> [!IMPORTANT]
> 在使用 SAML 2.0 标识提供者的这个登录方案中，仅可使用有限的一组客户端，其中包括：
> 
> - 基于 Web 的客户端，例如 Outlook Web Access 和 SharePoint Online
> - 多重格式电子邮件客户端，使用基本身份验证以及受支持的 Exchange 访问方法，例如 IMAP、POP、Active Sync、MAPI 等（需部署增强型客户端协议终结点）。此类客户端包括：
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016、Apple iPhone（各种 iOS 版本）
>     - 各种 Google Android 设备
>     - Windows Phone 7、Windows Phone 7.8 和 Windows Phone 8.0
>     - Windows 8 邮件客户端和 Windows 8.1 邮件客户端
>     - Windows 10 邮件客户端

在使用 SAML 2.0 标识提供者的这个登录方案中，所有其他客户端均不可用。 例如，Lync 2010 桌面客户端不能登录到已将 SAML 2.0 标识提供者配置为进行单一登录的服务。

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD SAML 2.0 协议要求
本文档包含有关你的 SAML 2.0 标识提供者必须实现的协议和消息格式设置的详细要求，以便与 Azure AD 联合登录到一个或多个 Microsoft 云服务 (例如 Microsoft 365) 。 此方案中所使用的 Microsoft 云服务的 SAML 2.0 信赖方 (SP-STS) 为 Azure AD。

建议你确保 SAML 2.0 标识提供者输出消息尽可能与提供的示例跟踪类似。 另外，请尽可能使用来自所提供的 Azure AD 元数据的特定属性值。 对输出消息满意以后，即可使用 Microsoft Connectivity Analyzer 进行测试，如下所述。

可以从此 URL 下载 Azure AD 元数据：[https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml)。
对于使用中国特定的 Microsoft 365 实例的中国客户而言，应使用以下联合终结点：[https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml)。

## <a name="saml-protocol-requirements"></a>SAML 协议要求
本部署详述如何将请求和响应消息对放置在一起，以便正确设置消息格式。

Azure AD 在进行配置后可以用于标识提供者，后者使用 SAML 2.0 SP Lite 配置文件，其中的部分具体要求已在下面列出。 可以使用示例性的 SAML 请求和响应消息进行自动和手动测试，从而实现与 Azure AD 的互操作性。

## <a name="signature-block-requirements"></a>签名块要求
在 SAML 响应消息中，签名节点包含有关消息本身的数字签名的信息。 签名块具有以下要求：

1. 断言节点本身必须签名
2.  必须使用 RSA-sha1 算法作为 DigestMethod。 不接受其他数字签名算法。
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  也可对 XML 文档签名。 
4.  转换算法必须与以下示例中的值匹配：`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  SignatureMethod 算法必须与以下示例匹配：`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>支持的绑定
绑定是所需的传输相关通信参数。 以下要求适用于绑定：

1. HTTPS 是所需的传输。
2.  Azure AD 将需要使用 HTTP POST 在登录期间提交令牌。
3.  Azure AD 将使用 HTTP POST 向标识提供者提交身份验证请求，并使用 REDIRECT 向标识提供者发送“注销”消息。

## <a name="required-attributes"></a>必需属性
下表显示了针对 SAML 2.0 消息中的特定属性的要求。
 
|属性|说明|
| ----- | ----- |
|NameID|此断言语句的值必须与 Azure AD 用户的 ImmutableID 相同。 它最多可以有 64 个字母数字字符。 任何非 html 安全型字符都必须进行编码，例如，“+”字符显示为“.2B”。|
|IDPEmail|用户主体名称 (UPN) 将在 SAML 响应中以 IDPEmail 元素的形式列出，这是用户在 Azure AD/Microsoft 365 中的 UserPrincipalName (UPN)。 UPN 采用电子邮件地址格式。 Windows Microsoft 365 (Azure Active Directory) 中的 UPN 值。|
|颁发者|必须是标识提供者的 URI。 不得重复使用示例消息中的颁发者。 如果在 Azure AD 租户中有多个顶级域，则 Issuer 必须与针对每个域配置的指定 URI 设置匹配。|

>[!IMPORTANT]
>Azure AD 目前支持下述适用于 SAML 2.0 的 NameID 格式 URI：urn:oasis:names:tc:SAML:2.0:nameid-format:persistent。

## <a name="sample-saml-request-and-response-messages"></a>SAML 请求与响应消息示例
显示的请求与响应消息对是针对登录消息交换的。
以下是从 Azure AD 发送到示例 SAML 2.0 标识提供者的示例请求消息。 示例 SAML 2.0 标识提供者为 Active Directory 联合身份验证服务 (AD FS)，已配置为使用 SAML-P 协议。 此外，还针对其他 SAML 2.0 标识提供者完成了互操作性测试。

```xml
    <samlp:AuthnRequest 
        xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
        ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
        IssueInstant="2014-01-30T16:18:35Z" 
        Version="2.0" 
        AssertionConsumerServiceIndex="0" >
            <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
            <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>
```

下面是一个示例响应消息，它从符合 SAML 2.0 的标识提供者示例发送到 Azure AD/Microsoft 365。

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>配置兼容 SAML 2.0 的标识提供者
本部分包含有关如何将 SAML 2.0 标识提供程序配置为与 Azure AD 进行联合身份验证以启用对一个或多个 Microsoft 云服务的单一登录访问的指南 (例如使用 SAML 2.0 协议 Microsoft 365) 。 此方案中所使用的 Microsoft 云服务的 SAML 2.0 信赖方为 Azure AD。

## <a name="add-azure-ad-metadata"></a>添加 Azure AD 元数据
SAML 2.0 标识提供者需遵循有关 Azure AD 信赖方的信息要求。 Azure AD 会在 https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml 上发布元数据。

建议你在配置 SAML 2.0 标识提供者时，始终导入最新的 Azure AD 元数据。

>[!NOTE]
>Azure AD 不会从标识提供者读取元数据。

## <a name="add-azure-ad-as-a-relying-party"></a>将 Azure AD 作为信赖方添加
必须启用 SAML 2.0 标识提供者与 Azure AD 之间的通信。 此配置取决于具体的的标识提供者，请参阅其文档。 通常应将信赖方 ID 设置为与 Azure AD 元数据中的 entityID 相同。

>[!NOTE]
>请验证 SAML 2.0 标识提供者服务器上的时钟是否已与准确的时间源同步。 时钟的时间不准确可能导致联合身份验证登录失败。

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>安装 Windows PowerShell 以使用 SAML 2.0 标识提供者进行登录
配置用于 Azure AD 登录的 SAML 2.0 标识提供者以后，下一步是下载并安装用于 Windows PowerShell 的 Azure Active Directory 模块。 安装以后，即可使用这些 cmdlet 将 Azure AD 域配置为联合域。

用于 Windows PowerShell 的 Azure Active Directory 模块在下载后可用于管理 Azure AD 中的组织数据。 此模块会将一组 cmdlet 安装到 Windows PowerShell；运行这些 cmdlet 以设置对 Azure AD 进行单一登录访问，然后转到订阅的所有云服务。 有关如何下载和安装 cmdlet 的说明，请参阅 [/previous-versions/azure/jj151815 (v = azure) ](/previous-versions/azure/jj151815(v=azure.100))

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>在 SAML 标识提供者与 Azure AD 之间建立信任
在 Azure AD 域上配置联合身份验证之前，必须已配置自定义域。 无法联合 Microsoft 提供的默认域。 Microsoft 的默认域以“onmicrosoft.com”结尾。
若要为单一登录添加或转换域，需要在 Windows PowerShell 命令行接口中运行一系列 cmdlet。

希望使用 SAML 2.0 标识提供者进行联合的每一个 Azure Active Directory 域都必须添加为单一登录域，或者从标准域转换为单一登录域。 添加或转换域可以在 SAML 2.0 标识提供者和 Azure AD 之间建立信任。

以下过程演示如何使用 SAML 2.0 SP-Lite 将现有的标准域转换为联合身份验证域。 

>[!NOTE]
>执行这一步后，域可能会遇到服务中断而影响用户，最长可持续 2 小时。

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>在 Azure AD Directory 中配置进行联合身份验证的域


1. 以租户管理员身份连接到 Azure AD Directory：

    ```powershell
    Connect-MsolService
    ```
    
2. 通过 SAML 2.0 将所需的 Microsoft 365 域配置为使用联合身份验证：

    ```powershell
    $dom = "contoso.com" 
    $BrandName - "Sample SAML 2.0 IDP" 
    $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
    $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
    $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
    $MyURI = "urn:uri:MySamlp2IDP" 
    $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
    $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
    $Protocol = "SAMLP" 
    Set-MsolDomainAuthentication `
        -DomainName $dom `
        -FederationBrandName $BrandName `
        -Authentication Federated `
        -PassiveLogOnUri $LogOnUrl `
        -ActiveLogOnUri $ecpUrl `
        -SigningCertificate $MySigningCert `
        -IssuerUri $MyURI `
        -LogOffUri $LogOffUrl `
        -PreferredAuthenticationProtocol $Protocol
    ``` 

3.  可以从 IDP 元数据文件中获取签名证书的 base64 编码字符串。 已提供此位置的示例，但该示例视实现情况可能稍有不同。

    ```xml
    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <KeyDescriptor use="signing">
          <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
             <X509Data>
                 <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
              </X509Data>
            </KeyInfo>
        </KeyDescriptor>
    </IDPSSODescriptor>
    ``` 

有关 "Set-msoldomainauthentication" 的详细信息，请参阅： [/previous-versions/azure/dn194112 (v =) ](/previous-versions/azure/dn194112(v=azure.100))。

>[!NOTE]
>仅当为标识提供者设置了 ECP 扩展时，才必须使用 `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"`。 除 Outlook Web Application (OWA) 之外的 Exchange Online 客户端依赖于基于 POST 的活动终结点。 如果 SAML 2.0 STS 实现的活动终结点类似于 Shibboleth 对活动终结点的 ECP 实现，则这些富客户端可能会与 Exchange Online 服务交互。

配置联合后，可切换回“非联合”（或“托管”），但是此更改最长需要两个小时才能完成，并且需要为每个用户分配新的随机密码以用于基于云的登录。 在某些情况下，可能需切换回“托管”模式才能在设置中重置错误。 有关域转换的详细信息，请参阅： [/previous-versions/azure/dn194122 (v = azure) ](/previous-versions/azure/dn194122(v=azure.100))。

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>预配 Azure AD/Microsoft 365 的用户主体
向 Microsoft 365 进行用户身份验证之前，必须根据 SAML 2.0 声明中的断言语句为 Azure AD 预配用户主体。 如果 Azure AD 事先无法识别这些用户主体，则它们无法用于联合登录。 可以使用 Azure AD Connect 或 Windows PowerShell 来预配用户主体。

可以使用 Azure AD Connect 从本地 Active Directory 预配 Azure AD 目录中域的主体。 如需更多详细信息，请参阅[将本地目录与 Azure Active Directory 集成](whatis-hybrid-identity.md)。

也可使用 Windows PowerShell 自动将新用户添加到 Azure AD 并同步本地目录中的更改。 若要使用 Windows PowerShell cmdlet，必须下载 [Azure Active Directory 模块](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)。

此过程演示如何向 Azure AD 添加单个用户。


1. 以租户管理员身份连接到 Azure AD Directory：Connect-MsolService。
2. 创建新用户主体：

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

有关 "Set-msoluser" 结帐的详细信息，请 [/previous-versions/azure/dn194096 (v = azure) ](/previous-versions/azure/dn194096(v=azure.100))

>[!NOTE]
>“UserPrinciplName”值必须与将在 SAML 2.0 声明中为“IDPEmail”发送的值匹配，“ImmutableID”值必须与在“NameID”断言语句中发送的值匹配。

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>通过 SAML 2.0 IDP 验证单一登录
在以管理员身份验证和管理单一登录（也称联合身份验证）之前，请查看以下文章中的信息并执行相关步骤，以便通过基于 SAML 2.0 SP-Lite 的标识提供者设置单一登录：


1.  查看 Azure AD SAML 2.0 协议要求
2.  配置 SAML 2.0 标识提供者
3.  安装 Windows PowerShell 以使用 SAML 2.0 标识提供者进行单一登录
4.  在 SAML 2.0 标识提供者与 Azure AD 之间建立信任
5.  通过 Windows PowerShell 或 Azure AD Connect 预配 Azure Active Directory (Microsoft 365) 的已知测试用户主体。
6.  使用 [Azure AD Connect](whatis-hybrid-identity.md) 配置目录同步。

通过基于 SAML 2.0 SP-Lite 的标识提供者设置单一登录以后，应验证其是否正常工作。

>[!NOTE]
>如果转换而不是添加域，则可能需要长达 24 小时来设置单一登录。
在验证单一登录之前，应完成 Active Directory 同步的设置，对目录进行同步，并激活同步的用户。

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>使用工具验证单一登录是否已正确设置
若要验证是否已正确设置单一登录，可以执行以下过程来确认能否使用企业凭据登录到云服务。

Microsoft 提供了一种工具，用于测试基于 SAML 2.0 的标识提供者。 在运行测试工具之前，必须将一个 Azure AD 租户配置为与标识提供者联合。

>[!NOTE]
>Connectivity Analyzer 需要 Internet Explorer 10 或更高版本。



1. 从 [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client) 下载连接分析器。
2.  单击“立即安装”开始下载并安装工具。
3.  选择“我不能通过 Office 365、Azure 或其他使用 Azure Active Directory 的服务设置联合身份验证”。
4.  下载并运行该工具后，即可看到“连接性诊断”窗口。 该工具将逐步引导你测试联合身份验证连接。
5.  连接分析器将打开你的 SAML 2.0 IDP 以供你登录，输入你要测试的用户主体的凭据： ![ 屏幕截图，显示 SAML 2.0 IDP 的登录窗口。](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  在联合测试登录窗口，应为配置为与 SAML 2.0 标识提供者联合的 Azure AD 租户输入帐户名和密码。 该工具会尝试使用这些凭据登录，并会提供在登录尝试期间执行的测试的详细结果作为输出。
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. 此窗口显示失败的测试结果。 单击“查看详细结果”会显示所执行的每次测试的结果的相关信息。 也可将结果保存到磁盘，以便进行共享。
 
>[!NOTE]
>Connectivity Analyzer 也使用基于 WS* 的协议和 ECP/PAOS 协议测试活动联合身份验证。 如果你未使用这些项，则可以忽略以下错误：使用你的标识提供者的活动联合身份验证终结点测试活动登录流。

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>手动验证单一登录是否已正确设置
手动验证提供更多的步骤，可以利用这些步骤来确保 SAML 2.0 标识提供者在多种情形下正常运行。
若要验证单一登录是否已正确设置，请完成以下步骤：


1. 在已加入域的计算机上，使用用于公司凭据的相同登录名登录到云服务。
2.  在密码框内单击。 如果设置了单一登录，则密码框会灰显，同时会显示以下消息：“你现在需在&lt;你的公司&gt;登录。”
3.  单击&lt;你的公司&gt;链接中的登录。 如果能够登录，则已设置好单一登录。

## <a name="next-steps"></a>后续步骤


- [使用 Azure AD Connect 进行 Active Directory 联合身份验证服务的管理和自定义](how-to-connect-fed-management.md)
- [Azure AD 联合身份验证兼容性列表](how-to-connect-fed-compatibility.md)
- [Azure AD Connect 自定义安装](how-to-connect-install-custom.md)