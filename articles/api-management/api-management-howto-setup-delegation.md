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
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: 796bea3c64ef7fc03367707461d13e0ea2514b8b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051744"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派用户注册和产品订阅

委派允许您使用现有网站处理开发人员登录 / 注册和订阅到产品，而不是开发人员门户中使用的内置功能。 这样就可以让网站拥有用户数据，并通过自定义方式对这些步骤进行验证。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>委派开发人员登录和注册

若要委派开发人员登录和注册到你现有的网站，您将需要在站点上创建一个特殊委派终结点。 需要对其充当从 API 管理开发人员门户中发起的任何此类请求的入口点。

最终工作流将如下所示：

1. 开发人员单击登录或注册链接在 API 管理开发人员门户
2. 浏览器重定向到委派终结点
3. 委派终结点反过来会重定向到或呈现 UI，要求用户登录或注册
4. 成功后，用户会重定向回一开始使用的 API 管理开发人员门户页

一开始需先将 API 管理设置为通过委派终结点来路由请求。 在 API 管理发布者门户中单击“安全”，并单击“委派”选项卡。单击复选框以启用委派登录和注册。

![“委派”页][api-management-delegation-signin-up]

* 确定特殊委派终结点的 URL，将其输入“委派终结点 URL”字段中。 
* 在”委派身份验证密钥”字段中输入一个密钥，该密钥用于计算提供给用户进行验证的签名，确保请求确实来自 Azure API 管理。 可以单击“生成”按钮让 API 管理随机生成一个密钥。

现在需创建“委派终结点”。 该终结点需执行多项操作：

1. 接收以下形式的请求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn & returnUrl = {源页的 URL} & = {字符串} & sig = {字符串}*
   > 
   > 
   
    查询参数的登录/注册示例：
   
   * **operation**：确定委派请求的类型，在此示例中只能为 **SignIn**
   * **returnUrl**： 用户单击了登录或注册链接的页面的 URL
   * **salt**：用于计算安全哈希的特殊 salt 字符串
   * **sig**：计算的安全哈希，用于与用户自行计算的哈希进行比较
2. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）
   
   * 根据 **returnUrl** 和 **salt** 查询参数计算字符串的 HMAC-SHA512 哈希（[示例代码在下面提供]）：
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则转到下一步，否则拒绝该请求。
3. 验证你收到的请求登录 / 注册：**操作**查询参数将设置为"**SignIn**"。
4. 为用户提供登录或注册 UI
5. 如果用户要注册，则需在 API 管理中为其创建相应的帐户。 请使用 API 管理 REST API [创建用户]。 这样做时，请确保将用户 ID 设置为相同的值相同用户存储区或您可以跟踪的 ID。
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
* **userId**： 帐户的用户 ID，以管理
* **salt**：用于计算安全哈希的特殊 salt 字符串
* **sig**：计算的安全哈希，用于与用户自行计算的哈希进行比较

## <a name="delegate-product-subscription"> </a>委派产品订阅
委派产品订阅的工作原理类似委派用户登录/注册。 最终工作流将如下所示：

1. 开发人员 API 管理开发人员门户中选择一个产品，并单击订阅按钮。
2. 浏览器重定向到委派终结点。
3. 委派终结点执行所需的产品订阅步骤。 由你来设计步骤。 它们可能包括重定向到另一个页请求计费信息、 询问其他问题，或只需存储信息而不需任何用户执行任何操作。

若要启用此功能，请在“委派”页上单击“委派产品订阅”。

接下来，请确保委派终结点以下操作：

1. 接收以下形式的请求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation= {operation} & productId = {要订阅的产品} & userId = {发出请求的用户} & = {字符串} & sig = {字符串}*
   >
   
    产品订阅示例的查询参数：
   
   * **operation**：确定委派请求的类型。 对于产品订阅请求，有效选项包括：
     * “Subscribe”：请求为用户订阅具有提供的 ID 的给定产品（见下）
     * “Unsubscribe”：请求为用户取消某个产品的订阅
     * “Renew”：请求续订某个订阅（例如即将到期的订阅）
   * **productId**：用户请求订阅的产品的 ID
   * **subscriptionId**： 上*Unsubscribe*并*续订*-产品订阅的 ID
   * **userId**： 发出请求的用户的 ID
   * **salt**：用于计算安全哈希的特殊 salt 字符串
   * **sig**：计算的安全哈希，用于与用户自行计算的哈希进行比较

2. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）
   
   * 计算基于字符串的 HMAC-SHA512 **productId**， **userId**，并**salt**查询参数：
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则转到下一步，否则拒绝该请求。
3. 处理产品订阅请求中的操作的类型**操作**-例如，计费信息、 提问更多问题，等等。
4. 在这一端成功为用户订阅产品以后，即可[调用产品订阅 REST API] 为用户订阅 API 管理产品。

## <a name="delegate-example-code"> </a> 示例代码

这些代码示例显示如何为：

* 采取*委派验证密钥*，发布者门户的委派屏幕中将其设置
* 创建 HMAC，后者则用于验证签名，证明传递的 returnUrl 的有效性。

同样的代码也适用于 productId 和 userId，只需进行轻微修改。

**C#用于生成 returnUrl 哈希的代码**

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

## <a name="next-steps"></a>后续步骤
有关委派的详细信息，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[请求单一登录 (SSO) 令牌]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[创建用户]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[调用产品订阅 REST API]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[下文提供了示例代码]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
