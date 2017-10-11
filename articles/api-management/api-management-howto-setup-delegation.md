---
title: "如何委托用户注册和产品订阅"
description: "了解如何委托用户注册和产品订阅给第三方在 Azure API 管理。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2637ab6405f2d4ea1da84981295a144874dfa4f6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委托用户注册和产品订阅
委派，可为处理开发人员登录-在/注册和订阅到产品而不是在开发人员门户中使用的内置功能使用你现有的网站。 这使你的网站以拥有用户数据和自定义方式执行这些步骤的验证。

## <a name="delegate-signin-up"></a>委派开发人员在登录和注册
若要委托开发人员签入和注册到你将需要在你充当用于从 API 管理开发人员门户启动的任何此类请求的入口点的站点上创建的特殊委派终结点的现有网站。

最后一个工作流将如下所示：

1. 开发人员单击 API 管理开发人员门户上的登录或注册链接
2. 浏览器重定向到委派终结点
3. 委派终结点中返回将重定向到或提供用户界面要求用户在登录或注册
4. 成功后，用户重定向回它们的启动从 API 管理开发人员门户页

若要开始，让我们第一个设置 API 管理路由通过请求你委派的终结点。 在 API 管理发布者门户中，单击**安全**，然后单击**委派**选项卡。 单击复选框以启用委托登录和注册。

![委派页][api-management-delegation-signin-up]

* 确定特殊委派终结点的 URL 将成为并其输入**委派终结点 URL**字段。 
* 在**委派身份验证密钥**字段中，输入将用于计算向你进行验证以确保该请求确实来自 Azure API 管理提供的签名的密钥。 你可以单击**生成**按钮后，随机生成密钥，为你的 API 管理。

现在你需要创建**委派终结点**。 它具有执行多个操作：

1. 接收到的请求采用以下形式：
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {源页面的 URL} 和 salt = {字符串} & sig = {字符串}*
   > 
   > 
   
    登录 / 注册的用例的查询参数：
   
   * **操作**： 标识哪种类型的委托请求是-它仅可以**SignIn**在这种情况下
   * **returnUrl**: 用户登录或注册链接单击的位置页的 URL
   * **salt**： 用于计算安全哈希算法的特殊 salt 字符串
   * **sig**： 要用来为你自己的比较计算的安全哈希值计算的哈希
2. 验证，该请求来自 Azure API 管理 （可选的但强烈建议执行的安全）
   
   * 计算基于字符串的 HMAC SHA512 哈希**returnUrl**和**salt**查询参数 ([提供下面的示例代码]):
     
     > HMAC (**salt** + \n + **returnUrl**)
     > 
     > 
   * 比较上述计算哈希值的值与**sig**查询参数。 如果两个哈希匹配，将移到下一步，否则拒绝该请求。
3. 验证你是否接收了登录/登录向上的请求：**操作**查询参数将设置为"**SignIn**"。
4. 向用户提供用户界面，以在登录或注册
5. 注册用户是否必须在 API 管理中为它们创建相应的帐户。 [创建一个用户]使用 API 管理 REST API。 在这样做，则确保将用户 ID 设置为相同处于用户存储区或您可以跟踪的 ID。
6. 当用户成功进行身份验证：
   
   * [请求单一登录 (SSO) 令牌]通过 API 管理 REST API
   * 将 returnUrl 查询参数追加到你已从上面的 API 调用接收 SSO URL:
     
     > 例如 https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * 将用户重定向到上述生成 URL

除了**SignIn**操作，你还可以执行帐户管理通过按照上述步骤并使用以下操作之一。

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

你必须通过帐户管理操作数的以下查询参数。

* **操作**： 标识哪种类型的委托请求是 （ChangePassword、 ChangeProfile，还是 CloseAccount）
* **userId**： 要管理的帐户的用户 id
* **salt**： 用于计算安全哈希算法的特殊 salt 字符串
* **sig**： 要用来为你自己的比较计算的安全哈希值计算的哈希

## <a name="delegate-product-subscription"></a>委派产品订阅
委派产品订阅工作方式类似于委派用户在登录/型。 最后一个工作流将如下所示：

1. 开发人员 API 管理开发人员门户中选择一个产品，并单击订阅按钮
2. 浏览器重定向到委派终结点
3. 委派终结点执行所需的产品订阅步骤-这是由你决定，可能需要将重定向到另一个页面以请求计费信息，询问其他问题，或只需存储信息并不需要用户执行任何操作

若要启用功能，在**委派**页上，单击**委托产品订阅**。

然后，确保委派终结点执行下列操作：

1. 接收到的请求采用以下形式：
   
   > *http://www.yourwebsite.com/apimdelegation?operation= {操作} （& a) productId = {产品璹綷} （& a) userId = {发出请求的用户} 和 salt = {字符串} & sig = {字符串}*
   > 
   > 
   
    产品订阅用例的查询参数：
   
   * **操作**： 标识是哪种类型的委托请求。 对于产品订阅请求的有效选项为：
     * "订阅": 订阅与特定产品与用户的请求提供 ID （见下文）
     * "取消订阅": 若要取消订阅产品中的用户的请求
     * "续订": 若要续订的订阅 （例如，可能即将到期） 请求
   * **productId**： 用户请求订阅的产品 ID
   * **userId**： 为其发出的请求的用户 ID
   * **salt**： 用于计算安全哈希算法的特殊 salt 字符串
   * **sig**： 要用来为你自己的比较计算的安全哈希值计算的哈希
2. 验证，该请求来自 Azure API 管理 （可选的但强烈建议执行的安全）
   
   * 计算基于字符串 hmac-sha512 **productId**， **userId**和**salt**查询参数：
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 比较上述计算哈希值的值与**sig**查询参数。 如果两个哈希匹配，将移到下一步，否则拒绝该请求。
3. 执行基于操作中请求的任何的类型产品订阅处理**操作**-例如计费，进一步的问题，等等。
4. 在成功订阅你一侧产品用户，订阅到通过 API 管理产品用户[调用 REST API 产品订阅]。

## <a name="delegate-example-code"></a>示例代码
这些代码示例展示了如何采用*委派验证密钥*，这将设置发布者门户的委派屏幕中为创建 HMAC 然后用于验证签名，以证明传递 returnUrl 的有效性。 相同的代码适用于 productId 和 userId 与细微的修改。

**C# 代码，以生成哈希的 returnUrl**

```c#
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

**NodeJS 代码，以生成哈希的 returnUrl**

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
有关委派的详细信息，请参阅下面的视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[请求单一登录 (SSO) 令牌]: http://go.microsoft.com/fwlink/?LinkId=507409
[创建用户]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[调用 REST API 产品订阅]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[提供下面的示例代码]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
