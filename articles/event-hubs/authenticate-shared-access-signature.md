---
title: 使用共享访问签名对 Azure 事件中心访问进行身份验证
description: 本文介绍如何使用共享访问签名对事件中心资源访问进行身份验证。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: d17026dba26b3c1cb846d60967180c29563c425d
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74545593"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>使用共享访问签名 (SAS) 对事件中心资源访问进行身份验证
使用共享访问签名 (SAS) 可以精细控制向具有共享访问签名的客户端授予的访问权限类型。 下面是可以在 SAS 中设置的一些控制措施： 

- SAS 的有效时间间隔，包括开始时间和过期时间。
- SAS 授予的权限。 例如，事件中心命名空间的 SAS 可以授予侦听权限，但不能授予发送权限。
- 只有可提供有效凭据的客户端才能将数据发送到事件中心。
- 一个客户端无法模拟另一个客户端。
- 可以阻止恶意客户端向事件中心发送数据。

本文介绍如何使用 SAS 对事件中心资源访问进行身份验证。 若要了解如何使用 SAS **授权**对事件中心资源的访问，请参阅[此文](authorize-access-shared-access-signature.md)。 

> [!NOTE]
> 作为安全最佳做法，Microsoft 建议尽可能地使用 Azure AD 凭据，而不要使用更容易泄密的共享访问签名。 尽管你可以继续使用共享访问签名 (SAS) 授予对事件中心资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄密的 SAS。
> 
> 有关 Azure 事件中心内 Azure AD 集成的详细信息，请参阅[使用 Azure AD 授权访问事件中心](authorize-access-azure-active-directory.md)。 


## <a name="configuring-for-sas-authentication"></a>配置 SAS 身份验证
可以在事件中心命名空间上配置 EventHubs 共享访问授权规则，或在事件中心中为启用了 Kafka 的命名空间配置实体（事件中心实例或 Kafka 主题）。 目前不支持在使用者组中配置共享访问授权规则，但你可以使用命名空间或实体中配置的规则来保护对使用者组的访问。 

下图显示了如何对示例实体应用授权规则。 

![配置授权规则](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

在此示例中，示例事件中心命名空间 (ExampleNamespace) 有两个实体：eh1 和 topic1。 授权规则是同时在实体级别和命名空间级别定义的。  

manageRuleNS、sendRuleNS 和 listenRuleNS 授权规则将同时应用到事件中心实例 eh1 和主题 t1。 listenRule-eh 和 sendRule-eh 授权规则仅应用到事件中心实例 eh1，sendRuleT 授权规则仅应用到主题 topic1。 

使用 sendRuleNS 授权规则时，客户端应用程序可以同时向 eh1 和 topic1 发送消息。 使用 sendRuleT 授权规则时，会强制要求仅对 topic1 进行粒度访问，因此，使用此规则进行访问的客户端应用程序现在无法向 eh1 发送消息，而只能向 topic1 发送消息。

## <a name="generate-a-shared-access-signature-token"></a>生成共享访问签名令牌 
有权访问授权规则的名称及其某个签名密钥的任何客户端都可以生成 SAS 令牌。 令牌是通过采用以下格式编写一个字符串而生成的：

- `se` – 令牌即时过期时间。 一个整数，反映令牌过期距 1970 年 1 月 1 日 00:00:00 UTC 纪元时间（UNIX 纪元）的秒数。
- `skn` – 授权规则的名称，即 SAS 密钥名称。
- `sr` – 正在访问的资源的 URI。
- `sig` – 签名。

signature-string 是基于资源 URI 计算的 SHA-256 哈希（上一部分中所述的范围），以及令牌即时过期时间的字符串表示形式，以 CRLF 分隔。

哈希计算方式如以下虚拟代码所示，返回 256 位/32 字节哈希值。 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

令牌包含非哈希值，使接收方可以使用相同的参数重新计算哈希，并验证颁发者是否拥有有效的签名密钥。

资源 URI 是向其声明访问权限的服务总线资源的完整 URI。 例如， http://<namespace>. servicebus.windows.net/<entityPath> 或 `sb://<namespace>.servicebus.windows.net/<entityPath>;`，`http://contoso.servicebus.windows.net/eventhubs/eh1`。

URI 必须采用百分比编码格式。

用于签名的共享访问授权规则必须在此 URI 指定的实体上，或由其分层父级之一进行配置。 例如，前面的示例中的 `http://contoso.servicebus.windows.net/eventhubs/eh1` 或 `http://contoso.servicebus.windows.net`。

SAS 令牌对于以 signature-string 中使用的 <resourceURI> 为前缀的所有资源有效。

> [!NOTE]
> 使用共享访问策略为事件中心生成访问令牌。 有关详细信息，请参阅[共享访问授权策略](authorize-access-shared-access-signature.md#shared-access-authorization-policies)。

### <a name="generating-a-signaturetoken-from-a-policy"></a>从策略生成签名（令牌） 
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

## <a name="authenticating-event-hubs-publishers-with-sas"></a>使用 SAS 对事件中心发布者进行身份验证 
事件发布者定义事件中心的虚拟终结点。 发布者只可用于将消息发送到事件中心，而不可用于接收消息。

通常，事件中心为每个客户端使用一个发布者。 发送到事件中心的任何发布者的所有消息都会在该事件中心内排队。 发布者可以实现精细访问控制。

为每个事件中心客户端分配一个唯一令牌，该令牌将上传到客户端。 生成令牌后，每个唯一令牌授予对不同唯一发布者的访问权限。 持有令牌的客户端只能向一个发布者发送消息，但不能向其他发布者发送消息。 如果多个客户端共享同一令牌，则其中每个客户端共享发布者。

为所有令牌分配 SAS 密钥。 通常，所有令牌使用同一密钥进行签名。 客户端不知道密钥，这会阻止客户端进行制造令牌。 客户端以相同的令牌运行，直到令牌过期。

例如，若要定义范围限定为向事件中心发送/发布消息的授权规则，需要定义发送授权规则。 可以在命名空间级别执行此操作，或者为特定的实体（事件中心实例或主题）分配更精细的范围。 具有此类粒度访问范围的客户端或应用程序称为“事件中心发布者”。 为此，请执行下列步骤：

1. 在要发布的实体中创建一个 SAS 密钥，以在其上分配**发送**范围。 有关详细信息，请参阅[共享访问授权策略](authorize-access-shared-access-signature.md#shared-access-authorization-policies)。
2. 使用在步骤 1 中生成的密钥为特定的发布者生成具有过期时间的 SAS 令牌。

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. 向发布方客户端提供令牌，该令牌只能发送到实体，后者可授予对的访问权限。

    令牌过期后，客户端将失去向该实体发送/发布消息的访问权限。 


> [!NOTE]
> 尽管不建议这样做，但可以为设备提供向事件中心或命名空间授予访问权限的令牌。 持有此令牌的任何设备都可以直接将消息发送到该事件中心。 此外，无法将设备列入阻止列表，使其无法向该事件中心发送消息。
> 
> 我们始终建议指定具体的精细范围。

> [!IMPORTANT]
> 创建令牌后，将为每个客户端设置其自身唯一的令牌。
>
> 当客户端向事件中心发送数据时，客户端会使用令牌标记其请求。 为了防止攻击者窃听和盗取令牌，客户端与事件中心之间的通信必须通过加密通道进行。
> 
> 如果令牌被攻击者盗取，攻击者可能会模拟令牌已被盗的客户端。 将发布者列入方块列表会导致客户端不可用，直至该客户端收到使用其他发布者的新令牌。


## <a name="authenticating-event-hubs-consumers-with-sas"></a>使用 SAS 对事件中心使用者进行身份验证 
若要对使用事件中心生成者生成的数据的后端应用程序进行身份验证，事件中心令牌身份验证要求其客户端对其事件中心命名空间或者事件中心实例或主题拥有**管理**权限或**侦听**权限。 通过使用者组使用事件中心内的数据。 尽管 SAS 策略可以提供精细范围，但此范围仅仅是在实体级别定义的，而不是在使用者级别定义的。 这意味着，在命名空间级别或者事件中心实例或主题级别定义的特权将应用到该实体的使用者组。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [使用 SAS 授权](authenticate-shared-access-signature.md)
- [使用基于角色的访问控制 (RBAC) 授权](authenticate-shared-access-signature.md)
- [详细了解事件中心](event-hubs-about.md)

请参阅以下相关文章：

- [使用 Azure Active Directory 对应用程序的 Azure 事件中心请求进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 对托管标识的事件中心资源访问进行身份验证](authenticate-managed-identity.md)
- [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)
- [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)