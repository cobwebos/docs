---
title: 故障排除 | Microsoft 文档
description: 如何解决使用 Microsoft 语音服务时出现的问题。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 04f3da19939d523d201d357b2b0293db1508431d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365662"
---
# <a name="troubleshooting"></a>故障排除

## <a name="error-http-403-forbidden"></a>错误 `HTTP 403 Forbidden`

使用语音识别 API 时，返回 `HTTP 403 Forbidden` 错误。

### <a name="cause"></a>原因

此错误通常是由身份验证问题导致的。 无有效 `Ocp-Apim-Subscription-Key` 或 `Authorization` 标头的连接请求将被服务拒绝并返回 `HTTP 403 Forbidden` 响应。

如果使用订阅密钥进行身份验证，原因可能是

- 订阅密钥缺失或无效
- 超出了订阅密钥的使用配额
- 调用 REST API 时，请求标头中未设置 `Ocp-Apim-Subscription-Key` 字段

如果使用授权令牌进行身份验证，以下原因可能会导致错误。

- 使用 REST 时，请求中缺少 `Authorization` 标头
- 授权标头中指定的授权令牌无效
- 授权令牌已过期。 访问令牌的到期时间为 10 分钟

有关身份验证的详细信息，请参阅[身份验证](How-to/how-to-authentication.md)页。

### <a name="troubleshooting-steps"></a>疑难解答步骤

#### <a name="verify-that-your-subscription-key-is-valid"></a>验证订阅密钥是否有效

可运行以下命令进行验证。 请注意将 YOUR_SUBSCRIPTION_KEY 替换为自己的订阅密钥。 如果订阅密钥有效，将在响应中收到 JSON Web 令牌 (JWT) 形式的授权令牌。 否则，响应会出错。

> [!NOTE]
> 将 `YOUR_SUBSCRIPTION_KEY` 替换为自己的订阅密钥。

# <a name="powershelltabpowershell"></a>[Powershell](#tab/Powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

该示例使用具有 bash 的 Linux 上的 curl。 如果未在平台上提供，则可能需要安装 curl。 该示例也适用于 Windows 上的 Cygwin、Git Bash、zsh 和其他 shell。

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

请确保在应用程序或 REST 请求中使用的订阅密钥与上面使用的订阅密钥相同。

#### <a name="verify-the-authorization-token"></a>验证授权令牌

仅当使用授权令牌进行身份验证时，才需要执行此步骤。 运行以下命令，验证授权令牌是否仍然有效。 该命令向服务发出 POST 请求，并期望来自该服务的响应消息。 如果仍收到 HTTP `403 Forbidden` 错误，请仔细检查访问令牌是否设置正确且未过期。

> [!IMPORTANT]
> 令牌的到期时间为 10 分钟。
> [!NOTE]
> 将 `YOUR_AUDIO_FILE` 替换为预先录制的音频文件的路径，并将 `YOUR_ACCESS_TOKEN` 替换为上一步中返回的授权令牌。

# <a name="powershelltabpowershell"></a>[Powershell](#tab/Powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>错误 `HTTP 400 Bad Request`

这通常是因为请求正文包含无效的音频数据。 目前，我们仅支持 WAV 文件。

## <a name="error-http-408-request-timeout"></a>错误 `HTTP 408 Request Timeout`

此错误的原因很可能是没有发送到服务的音频数据，服务在超时后返回此错误。 对于 REST API，音频数据应放在请求正文中。

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>响应中的 `RecognitionStatus` 为 `InitialSilenceTimeout`

音频数据通常是导致此问题的原因。 例如，

- 音频在开始时有较长的静音时间。 该服务将在几秒钟后停止识别并返回 `InitialSilenceTimeout`。
- 音频使用了不受支持的编解码器格式，这导致音频数据被视为静音。
