---
title: 使用共享访问签名对 Azure 事件中心访问进行身份验证
description: 本文介绍如何使用共享访问签名对事件中心资源的访问权限进行身份验证。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cb5c53f3f473c10a3c9a12bb1aac20b109c06422
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992531"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>使用共享访问签名 (SAS) 对事件中心资源的访问进行身份验证
使用共享访问签名 (SAS), 可以精细地控制向具有共享访问签名的客户端授予的访问类型。 下面是可以在 SAS 中设置的一些控件: 

- SAS 有效的时间间隔, 包括开始时间和到期时间。
- SAS 授予的权限。 例如, 事件中心命名空间的 SAS 可能授予 "侦听" 权限, 但不授予 "发送" 权限。
- 只有可提供有效凭据的客户端才能将数据发送到事件中心。
- 客户端无法模拟另一个客户端。
- 可以阻止恶意客户端向事件中心发送数据。

本文介绍如何使用 SAS 对事件中心资源的访问权限进行身份验证。 若要了解如何使用 SAS**授权**访问事件中心资源, 请参阅[此文](authorize-access-shared-access-signature.md)。 

> [!NOTE]
> Microsoft 建议在可能的情况下使用 Azure AD 凭据作为最佳安全方案, 而不是使用共享访问签名, 这样会更容易受到威胁。 尽管你可以继续使用共享访问签名 (SAS) 授予对事件中心资源的精细访问权限, 但 Azure AD 提供了类似的功能, 而无需管理 SAS 令牌, 也无需担心吊销已泄露的 SAS。
> 
> 有关 Azure 事件中心 Azure AD 集成的详细信息, 请参阅[使用 Azure AD 授予对事件中心的访问权限](authorize-access-azure-active-directory.md)。 


## <a name="configuring-for-sas-authentication"></a>为 SAS 身份验证配置
可以在事件中心命名空间上配置 EventHubs 共享访问授权规则, 或在事件中心中为启用了 Kafka 的命名空间配置实体 (事件中心实例或 Kafka 主题)。 当前不支持在使用者组上配置共享访问授权规则, 但你可以使用在命名空间或实体上配置的规则来安全访问使用者组。 

下图显示了如何对示例实体应用授权规则。 

![配置授权规则](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

在此示例中, 示例事件中心命名空间 (ExampleNamespace) 具有两个实体: eh1 和 topic1。 授权规则在实体级别同时定义, 也在命名空间级别定义。  

ManageRuleNS、sendRuleNS 和 listenRuleNS 授权规则适用于事件中心实例 eh1 和主题 t1。 ListenRule 和 sendRule 授权规则仅适用于事件中心实例 eh1, Sendrulet 仅 authorization 规则仅适用于主题 topic1。 

使用 sendRuleNS 授权规则时, 客户端应用程序可以同时发送到 eh1 和 topic1。 当使用 Sendrulet 仅授权规则时, 它将仅强制执行 topic1 的粒度访问, 因此, 使用此规则进行访问的客户端应用程序现在无法发送到 eh1, 但只能发送到 topic1。

## <a name="generate-a-shared-access-signature-token"></a>生成共享访问签名令牌 
有权访问授权规则的名称及其某个签名密钥的任何客户端都可以生成 SAS 令牌。 令牌是通过采用以下格式编写一个字符串而生成的：

- `se`-令牌过期即时。 当令牌过期时, 从00:00:00 年 1970 1 月1日1970年1月1日 UTC 开始的整数反映秒
- `skn`–授权规则的名称, 即 SAS 密钥名称。
- `sr`–正在访问的资源的 URI。
- `sig`信号.

签名字符串是通过资源 URI (上一节中所述的作用域) 和令牌到期的字符串表示形式 (用 CRLF 分隔) 来计算的 256 SHA-1 哈希。

哈希计算方式如以下虚拟代码所示，返回 256 位/32 字节哈希值。 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

令牌包含非哈希值，使接收方可以使用相同的参数重新计算哈希，并验证颁发者是否拥有有效的签名密钥。

资源 URI 是向其声明访问权限的服务总线资源的完整 URI。 例如, http://<namespace>或`sb://<namespace>.servicebus.windows.net/<entityPath>;` <entityPath> servicebus.windows.net/`http://contoso.servicebus.windows.net/eventhubs/eh1`。

URI 必须按百分比编码。

用于签名的共享访问授权规则必须在此 URI 指定的实体上，或由其分层父级之一进行配置。 例如，前面的示例中的 `http://contoso.servicebus.windows.net/eventhubs/eh1` 或 `http://contoso.servicebus.windows.net`。

SAS 令牌对于以签名字符串中使用的前缀的<resourceURI>所有资源有效。

> [!NOTE]
> 使用共享访问策略为事件中心生成访问令牌。 有关详细信息, 请参阅[共享访问授权策略](authorize-access-shared-access-signature.md#shared-access-authorization-policies)。

### <a name="generating-a-signaturetoken-from-a-policy"></a>从策略生成签名 (令牌) 
以下部分介绍如何使用共享访问签名策略生成 SAS 令牌。

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>JAVA

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>对带有 SAS 的事件中心发布者进行身份验证 
事件发布者定义事件中心的虚拟终结点。 发布者只能用于将消息发送到事件中心, 而不能接收消息。

通常，事件中心为每个客户端使用一个发布者。 发送到事件中心的任何发布者的所有消息都会在该事件中心内排队。 发布服务器支持精细访问控制。

为每个事件中心客户端分配一个唯一令牌，该令牌将上传到客户端。 生成令牌后, 每个唯一令牌将授予对不同唯一发布者的访问权限。 持有令牌的客户端只能发送到一个发布服务器, 而不能发送到其他发布者。 如果多个客户端共享同一令牌，则其中每个客户端都将共享一个发布者。

所有令牌都为 SAS 密钥分配。 通常，所有令牌使用同一密钥进行签名。 客户端不知道密钥, 这会阻止其他客户端通过制造令牌。 客户端对相同的令牌进行操作, 直到它们过期。

例如, 若要定义限定为仅发送/发布到事件中心的授权规则, 则需要定义发送授权规则。 这可以在命名空间级别上完成, 也可以为特定实体 (事件中心实例或主题) 指定更精细的作用域。 作用域为此类粒度访问的客户端或应用程序称为事件中心发布者。 为此，请执行下列步骤：

1. 在要发布的实体上创建一个 SAS 密钥, 以便为其分配**发送**范围。 有关详细信息, 请参阅[共享访问授权策略](authorize-access-shared-access-signature.md#shared-access-authorization-policies)。
2. 使用在步骤1中生成的密钥为特定发布者生成具有到期时间的 SAS 令牌。
3. 向发布方客户端提供令牌, 该令牌只能发送到令牌授予访问权限的实体。
4. 令牌过期后, 客户端将失去其对实体的 "发送/发布" 访问权限。 


> [!NOTE]
> 尽管不建议这样做, 但可以为设备提供向事件中心授予访问权限的令牌。 持有此令牌的任何设备都可以直接将消息发送到该事件中心。 此外，无法将设备列入阻止列表，使其无法向该事件中心发送消息。
> 
> 当同一个令牌分配到多个设备时, 可以观察到上述行为, 从而使你可以在命名空间级别进行访问。 在这种情况下, 不能隔离和撤销恶意设备/发布服务器。 我们始终建议为具体的粒度范围。

> [!IMPORTANT]
> 创建令牌后，将为每个客户端设置其自身唯一的令牌。
>
> 当客户端向事件中心发送数据时, 它会使用令牌标记其请求。 为了防止攻击者窃听和盗取令牌，客户端与事件中心之间的通信必须通过加密通道进行。
> 
> 如果令牌被攻击者盗取，攻击者可能会模拟令牌已被盗的客户端。 列入方块列表发布服务器后, 将使该客户端无法使用, 直到收到使用其他发布者的新令牌。


## <a name="authenticating-event-hubs-consumers-with-sas"></a>使用 SAS 对事件中心使用者进行身份验证 
若要对从事件中心创建者生成的数据使用的后端应用程序进行身份验证, 事件中心令牌身份验证要求其客户端将 "**管理**" 权限或 "**侦听**" 特权分配给其事件中心命名空间或事件中心实例或主题。 使用使用者组从事件中心使用数据。 虽然 SAS 策略提供粒度范围, 但此作用域只能在实体级别定义, 而不是在使用者级别定义。 这意味着, 在命名空间级别或事件中心实例或主题级别定义的权限将应用于该实体的使用者组。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [使用 SAS 授权](authenticate-shared-access-signature.md)
- [使用角色基本访问控制 (RBAC) 进行授权](authenticate-shared-access-signature.md)
- [了解有关事件中心的详细信息](event-hubs-about.md)

请参阅以下相关文章:

- [使用 Azure Active Directory 对应用程序中的 Azure 事件中心的请求进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 验证托管标识, 以访问事件中心资源](authenticate-managed-identity.md)
- [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)
- [使用共享访问签名授予对事件中心资源的访问权限](authorize-access-shared-access-signature.md)