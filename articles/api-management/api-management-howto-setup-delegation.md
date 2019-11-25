---
title: 如何委派用户注册和产品订阅
description: 了解如何在 Azure API 管理中将用户注册和产品订阅委派给第三方。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454352"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派用户注册和产品订阅

Delegation allows you to use your existing website for handling developer sign in/sign up and subscription to products, as opposed to using the built-in functionality in the developer portal. It enables your website to own the user data and perform the validation of these steps in a custom way.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>委派开发人员登录和注册

To delegate developer, sign in and sign up to your existing website, you'll need to create a special delegation endpoint on your site. It needs to act as the entry-point for any such request initiated from the API Management developer portal.

最终工作流将如下所示：

1. Developer clicks on the sign in or sign up link at the API Management developer portal
2. 浏览器重定向到委派终结点
3. Delegation endpoint in return redirects to or presents UI asking user to sign in or sign up
4. 成功后，用户会重定向回一开始使用的 API 管理开发人员门户页

一开始需先将 API 管理设置为通过委派终结点路由请求。 In the Azure portal, search for **Security** in your API Management resource and then click the **Delegation** item. Click the checkbox to enable 'Delegate sign in & sign up'.

![“委派”页][api-management-delegation-signin-up]

* 确定特殊委派终结点的 URL，将其输入“委派终结点 URL”字段中。 
* 在”委派身份验证密钥”字段中输入一个密钥，该密钥用于计算提供给用户进行验证的签名，确保请求确实来自 Azure API 管理。 可以单击“生成”按钮让 API 管理随机生成一个密钥。

现在需创建“委派终结点”。 该终结点需执行多项操作：

1. 接收以下形式的请求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Query parameters for the sign in / sign up case:
   
   * **operation**：确定委派请求的类型，在此示例中只能为 **SignIn**
   * **returnUrl**: the URL of the page where the user clicked on a sign in or sign up link
   * **salt**：用于计算安全哈希的特殊 salt 字符串
   * **sig**：计算的安全哈希，用于与用户自行计算的哈希进行比较
2. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）
   
   * 根据 **returnUrl** 和 **salt** 查询参数计算字符串的 HMAC-SHA512 哈希（[示例代码在下面提供]）：
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则转到下一步，否则拒绝该请求。
3. Verify that you are receiving a request for sign in/sign up: the **operation** query parameter will be set to "**SignIn**".
4. Present the user with UI to sign in or sign up
5. 如果用户要注册，则需在 API 管理中为其创建相应的帐户。 请使用 API 管理 REST API [创建用户]。 When doing so, ensure that you set the user ID to the same value as in your user store or to an ID that you can keep track of.
6. 成功对用户进行身份验证以后，请执行以下操作：
   
   * 通过 API 管理 REST API [请求单一登录 (SSO) 令牌]
   * 将 returnUrl 查询参数追加到从上述 API 调用接收的 SSO URL：
     
     > 例如， https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * 将用户重定向到上述生成的 URL

除了 **SignIn** 操作，还可以执行帐户管理，只需按上述步骤使用以下某个操作即可：

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

若要进行帐户管理操作，必须传递以下查询参数。

* **operation**：确定委派请求的类型（ChangePassword、ChangeProfile 或 CloseAccount）
* **userId**: the user ID of the account to manage
* **salt**：用于计算安全哈希的特殊 salt 字符串
* **sig**：计算的安全哈希，用于与用户自行计算的哈希进行比较

## <a name="delegate-product-subscription"> </a>委派产品订阅
Delegating product subscription works similarly to delegating user sign in/-up. 最终工作流将如下所示：

1. Developer selects a product in the API Management developer portal and clicks on the Subscribe button.
2. Browser is redirected to the delegation endpoint.
3. Delegation endpoint performs required product subscription steps. It's up to you to design the steps. They may include redirecting to another page to request billing information, asking additional questions, or simply storing the information and not requiring any user action.

若要启用此功能，请在“委派”页上单击“委派产品订阅”。

Next, ensure the delegation endpoint does the following actions:

1. 接收以下形式的请求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    产品订阅示例的查询参数：
   
   * **operation**：确定委派请求的类型。 对于产品订阅请求，有效选项包括：
     * “Subscribe”：请求为用户订阅具有提供的 ID 的给定产品（见下）
     * “Unsubscribe”：请求为用户取消某个产品的订阅
     * “Renew”：请求续订某个订阅（例如即将到期的订阅）
   * **productId**：用户请求订阅的产品的 ID
   * **subscriptionId**: on *Unsubscribe* and *Renew* - the ID of the product subscription
   * **userId**: the ID of the user the request is made for
   * **salt**：用于计算安全哈希的特殊 salt 字符串
   * **sig**：计算的安全哈希，用于与用户自行计算的哈希进行比较

2. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）
   
   * Compute an HMAC-SHA512 of a string based on the **productId**, **userId**, and **salt** query parameters:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则转到下一步，否则拒绝该请求。
3. Process product subscription based on the type of operation requested in **operation** - for example, billing, further questions, etc.
4. On successfully subscribing the user to the product on your side, subscribe the user to the API Management product by [calling the REST API for subscriptions].

## <a name="delegate-example-code"> </a> 示例代码

These code samples show how to:

* Take the *delegation validation key*, which is set in the Delegation screen of the publisher portal
* Create an HMAC, which is then used to validate the signature, proving the validity of the passed returnUrl.

同样的代码也适用于 productId 和 userId，只需进行轻微修改。

**用于生成 returnUrl 哈希的 C# 代码**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**用于生成 returnUrl 哈希的 NodeJS 代码**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> You need to [republish the developer portal](api-management-howto-developer-portal-customize.md#publish) for the delegation changes to take effect.

## <a name="next-steps"></a>后续步骤
有关委派的详细信息，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[请求单一登录 (SSO) 令牌]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[创建用户]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[calling the REST API for subscriptions]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[示例代码在下面提供]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
