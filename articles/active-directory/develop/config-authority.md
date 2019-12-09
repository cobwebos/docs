---
title: 配置不同的标识提供者（MSAL iOS/macOS） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用不同的权限，例如 B2C、主权云和 guest 用户、MSAL for iOS 和 macOS。
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f2408dc2dd80ab3f52b158a18355087fe941b48
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917975"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>如何：将 iOS 和 macOS 的 MSAL 配置为使用不同的标识提供程序

本文介绍了如何为不同的权限（如 Azure Active Directory （Azure AD）、企业到消费者（B2C）、主权云和来宾用户）配置适用于 iOS 和 macOS （MSAL）的 Microsoft 身份验证库应用。  在本文中，通常可以将颁发机构视为标识提供者。

## <a name="default-authority-configuration"></a>默认授权配置

使用 `https://login.microsoftonline.com/common`的默认授权 URL （适用于大多数 Azure Active Directory （AAD）方案）配置 `MSALPublicClientApplication`。 除非你要实现高级方案（如国家云）或使用 B2C，否则不需要更改它。

> [!NOTE]
> 不支持将 Active Directory 联合身份验证服务用作标识提供程序（ADFS）的新式身份验证（有关详细信息，请参阅[ADFS For 开发人员](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)）。 通过联合支持 ADFS。

## <a name="change-the-default-authority"></a>更改默认权限

在某些情况下，例如企业到消费者（B2C），可能需要更改默认机构。

### <a name="b2c"></a>B2C

若要使用 B2C， [Microsoft 身份验证库（MSAL）](reference-v2-libraries.md)需要不同的机构配置。 MSAL 将一个颁发机构 URL 格式视为 B2C。 可识别的 B2C 机构格式为 `https://<host>/tfp/<tenant>/<policy>`，例如 `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`。 不过，你也可以通过将权威声明为 B2C 权威机构来使用任何其他受支持的 B2C 授权 Url。

若要支持 B2C 的任意 URL 格式，可以使用任意 URL 来设置 `MSALB2CAuthority`，如下所示：

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

所有未使用默认 B2C 颁发机构格式的 B2C 权威都必须声明为已知的颁发机构。

将每个不同的 B2C 机构添加到 "已知颁发机构" 列表，即使策略中的 "颁发机构" 不同。

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

当应用请求新策略时，需要更改颁发机构 URL，因为每个策略的证书颁发机构 URL 有所不同。 

若要配置 B2C 应用程序，请 `MSALPublicClientApplicationConfig` 在创建 `MSALPublicClientApplication`之前，将 `@property MSALAuthority *authority` 设置为 `MSALB2CAuthority` 的实例，如下所示：

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

如果你的应用在主权云中运行，你可能需要在 `MSALPublicClientApplication`中更改授权 URL。 下面的示例将颁发机构 URL 设置为适用于德语 AAD 云：

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

可能需要将不同的作用域传递到每个主权云。 要发送的作用域取决于所使用的资源。 例如，你可能会在全球云中使用 `"https://graph.microsoft.com/user.read"`，并在德语云中 `"https://graph.microsoft.de/user.read"`。

### <a name="signing-a-user-into-a-specific-tenant"></a>将用户登录到特定租户

当证书颁发机构 URL 设置为 `"login.microsoftonline.com/common"`时，用户将登录到其主租户。 但是，某些应用可能需要将用户登录到不同的租户，而某些应用则仅适用于单个租户。

若要将用户登录到特定租户，请使用特定权限配置 `MSALPublicClientApplication`。 例如：

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

下面演示了如何将用户登录到特定租户：

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

`MSALAuthority` 类是 MSAL 机构类的基本抽象类。 请勿尝试使用 `alloc` 或 `new`创建它的实例。 相反，可以直接创建其中一个子类（`MSALAADAuthority`、`MSALB2CAuthority`），也可以使用工厂方法 `authorityWithURL:error:` 来使用颁发机构 URL 创建子类。

使用 `url` 属性可获取规范化的授权 URL。 不属于权威机构的额外参数和路径组件或片段不会位于返回的规范化颁发机构 URL 中。

下面是可以实例化的 `MSALAuthority` 的子类，具体取决于要使用的证书颁发机构。

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` 表示 AAD 颁发机构。 颁发机构 url 应采用以下格式，其中 `<port>` 是可选的： `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` 表示 B2C 证书颁发机构。 默认情况下，B2C 机构 url 应采用以下格式，其中 `<port>` 可选： `https://<host>:<port>/tfp/<tenant>/<policy>`。 但是，MSAL 还支持其他任意 B2C 机构格式。

## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
