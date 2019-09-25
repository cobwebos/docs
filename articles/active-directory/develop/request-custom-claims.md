---
title: 如何使用适用于 iOS 和 macOS 的 MSAL 请求自定义声明 |Microsoft 标识平台
description: 了解如何请求自定义声明。
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e09d58742bffd74f07f79b3ec55c1e81533632
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268981"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>如何：使用适用于 iOS 和 macOS 的 MSAL 请求自定义声明

通过 OpenID Connect，可以选择请求从用户信息终结点和/或 ID 令牌返回各个声明。 声明请求表示为一个 JSON 对象，该对象包含请求的声明的列表。 有关更多详细信息，请参阅[OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) 。

适用于 iOS 和 macOS 的 Microsoft 身份验证库（MSAL）允许在交互式和无提示令牌采集方案中请求特定声明。 它通过`claimsRequest`参数执行此操作。

在多个方案中，需要此项。 例如：

- 在应用程序的标准集外请求声明。
- 请求不能使用应用程序范围指定的标准声明的特定组合。 例如，如果访问令牌由于缺少声明而被拒绝，则该应用程序可以使用 MSAL 请求缺少的声明。

> [!NOTE]
> 只要指定了声明请求，MSAL 就会绕过访问令牌缓存。 如果需要其他声明（而`claimsRequest`不是始终在每个 MSAL API 调用中提供相同`claimsRequest`的参数），则必须提供参数。

`claimsRequest`可在和`MSALInteractiveTokenParameters`中`MSALSilentTokenParameters`指定：

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`可从 JSON 声明请求的 NSString 表示形式构造。 

Objective-C：

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

反应

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



还可以通过请求其他特定声明来修改它：

Objective-C：

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

反应

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`然后，应在令牌参数中进行设置，并将其提供给 MSAL 令牌收购 Api 之一：

Objective-C：

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

反应

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
