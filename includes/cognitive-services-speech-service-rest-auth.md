---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668650"
---
## <a name="authentication"></a>Authentication

每个请求都需要一个授权标头。 下表列出了每个服务支持的标头：

| 支持的授权标头 | 语音转文本 | 文本转语音 |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | 是 | 否 |
| 授权：持有者 | 是 | 是 |

使用 `Ocp-Apim-Subscription-Key` 标头时，只需提供订阅密钥。 例如：

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

使用 `Authorization: Bearer` 标头时，需要向 `issueToken` 终结点发出请求。 在此请求中，交换有效期为 10 分钟的访问令牌的订阅密钥。 在接下来的几个部分中，你将了解如何获取令牌并使用令牌。

### <a name="how-to-get-an-access-token"></a>如何获取访问令牌

若要获取访问令牌，需使用 `issueToken` 和订阅密钥向 `Ocp-Apim-Subscription-Key` 终结点发出请求。

`issueToken` 终结点具有以下格式：

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

将 `<REGION_IDENTIFIER>` 替换为与此表中的订阅区域匹配的标识符：

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

使用这些示例创建访问令牌请求。

#### <a name="http-sample"></a>HTTP 示例

此示例是获取令牌的简单 HTTP 请求。 请将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 如果订阅不在美国西部区域，请将 `Host` 标头替换为所在区域的主机名。

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

响应正文包含 JSON Web 令牌 (JWT) 格式的访问令牌。

#### <a name="powershell-sample"></a>PowerShell 示例

此示例是获取访问令牌的简单 PowerShell 脚本。 请将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 请务必使用与订阅匹配的正确区域终结点。 此示例目前设置为“美国西部”。

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL 示例

cURL 是 Linux（及面向 Linux 的 Windows 子系统）中提供的一种命令行工具。 此 cURL 命令演示如何获取访问令牌。 请将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 请务必使用与订阅匹配的正确区域终结点。 此示例目前设置为“美国西部”。

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# 示例

此 C# 类演示如何获取访问令牌。 实例化该类时，请传递语音服务订阅密钥。 如果订阅不在美国西部区域，请更改 `FetchTokenUri` 的值，以便与订阅的区域相匹配。

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python 示例

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>如何使用访问令牌

应将访问令牌作为 `Authorization: Bearer <TOKEN>` 标头发送到服务。 每个访问令牌的有效期为 10 分钟。 随时可以获取新令牌，但是，为了最大限度地减少流量和延迟，我们建议使用同一令牌 9 分钟。

下面是向文本转语音 REST API 发出的示例 HTTP 请求：

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
