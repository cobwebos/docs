---
title: 配置标识提供者 (MSAL iOS/macOS) |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何在适用于 iOS 和 macOS 的 MSAL 中使用不同的颁发机构，例如 B2C、主权云和来宾用户。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77085213"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>如何：将适用于 iOS 和 macOS 的 MSAL 配置为使用不同的标识提供者

本文介绍如何在适用于 iOS 和 macOS 的 Microsoft 身份验证库 (MSAL) 应用中配置不同的颁发机构，例如 Azure Active Directory (Azure AD)、企业到消费者 (B2C)、主权云和来宾用户。  在整篇文章中，基本上可将颁发机构看作是标识提供者。

## <a name="default-authority-configuration"></a>默认颁发机构配置

在 `MSALPublicClientApplication` 中配置了 `https://login.microsoftonline.com/common` 的默认颁发机构 URL，这适用于大多数 Azure Active Directory (AAD) 方案。 除非要实现国家云等高级方案或要使用 B2C，否则不需要更改它。

> [!NOTE]
> 不支持使用 Active Directory 联合身份验证服务作为标识提供者 (ADFS) 的新式身份验证（有关详细信息，请参阅[面向开发人员的 ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)）。 ADFS 是通过联合支持的。

## <a name="change-the-default-authority"></a>更改默认颁发机构

在某些方案（例如企业到消费者 (B2C)）中，可能需要更改默认颁发机构。

### <a name="b2c"></a>B2C

若要使用 B2C，[Microsoft 身份验证库 (MSAL)](reference-v2-libraries.md) 需要不同的颁发机构配置。 MSAL 将一种颁发机构 URL 格式识别为 B2C 本身。 可识别的 B2C 机构格式为 `https://<host>/tfp/<tenant>/<policy>`，例如 `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`。 不过，也可以通过将颁发机构显式声明为 B2C 颁发机构，来使用任何其他受支持的 B2C 颁发机构 URL。

若要支持 B2C 的任意 URL 格式，可以使用任意 URL 设置 `MSALB2CAuthority`，如下所示：

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

所有不使用默认 B2C 颁发机构格式的 B2C 颁发机构必须声明为已知的颁发机构。

将每个不同的 B2C 颁发机构添加到已知颁发机构列表，即使颁发机构只是在策略方面不同，也应如此。

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

当应用请求新策略时，需要更改颁发机构 URL，因为每个策略的颁发机构 URL 各不相同。 

若要配置 B2C 应用程序，请在创建 `MSALPublicClientApplication` 之前，使用 `MSALPublicClientApplicationConfig` 中 `MSALB2CAuthority` 的实例设置 `@property MSALAuthority *authority`，如下所示：

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>主权云

如果应用在主权云中运行，你可能需要更改 `MSALPublicClientApplication` 中的颁发机构 URL。 以下示例将颁发机构 URL 设置为使用 AAD 德国云：

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

可能需要将不同的范围传递到每个主权云。 要发送的范围取决于所用的资源。 例如，可以全球云中使用 `"https://graph.microsoft.com/user.read"`，在德国云中使用 `"https://graph.microsoft.de/user.read"`。

### <a name="signing-a-user-into-a-specific-tenant"></a>将用户登录到特定的租户

将颁发机构 URL 设置为 `"login.microsoftonline.com/common"` 时，用户将登录到其主租户。 但是，有些应用可能需要将用户登录到不同的租户，而有些应用仅使用单个租户。

若要将用户登录到特定的租户，请使用特定的颁发机构配置 `MSALPublicClientApplication`。 例如：

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

下面演示了如何将用户登录到特定的租户：

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>支持的颁发机构

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority` 类是 MSAL 颁发机构类的基本抽象类。 请勿尝试使用 `alloc` 或 `new` 创建该类的实例。 应直接创建其子类之一（`MSALAADAuthority`、`MSALB2CAuthority`），或者使用工厂方法 `authorityWithURL:error:` 和颁发机构 URL 来创建子类。

使用 `url` 属性获取规范化的颁发机构 URL。 不属于颁发机构的附加参数和路径组成部分或片段不会包含在返回的规范化颁发机构 URL 中。

下面是可以根据所要使用的颁发机构实例化的 `MSALAuthority` 的子类。

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` 表示 AAD 颁发机构。 颁发机构 URL 应采用以下格式（其中 `<port>` 是可选的）：`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` 表示 B2C 颁发机构。 默认情况下，B2C 颁发机构 URL 应采用以下格式（其中 `<port>` 是可选的）：`https://<host>:<port>/tfp/<tenant>/<policy>`。 但是，MSAL 还支持其他任意 B2C 颁发机构格式。

## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
