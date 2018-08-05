---
title: 认知服务语音 SDK 故障排除
description: 认知服务语音 SDK 故障排除
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284116"
---
# <a name="troubleshooting-speech-services-sdk"></a>语音服务 SDK 故障排除

本文提供的信息可帮助你解决使用语音 SDK 时可能遇到的问题。

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>错误 `WebSocket Upgrade failed with an authentication error (403).`

区域或服务可能有错误的终结点。 请仔细检查 URI 以确保它是正确的。 另请参阅下一节，因为这也可能是订阅密钥或授权令牌有问题。

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>错误 `HTTP 403 Forbidden` 或错误 `HTTP 401 Unauthorized`

此错误通常是由身份验证问题导致的。 没有有效 `Ocp-Apim-Subscription-Key` 或 `Authorization` 标头的连接请求将被拒绝，状态为 401 或 403。

* 如果正在使用订阅密钥进行身份验证，原因可能是：

    - 订阅密钥缺失或无效
    - 已超出订阅的使用配额

* 如果正在使用授权令牌进行身份验证，原因可能是：

    - 授权令牌无效
    - 授权令牌已过期

### <a name="validate-your-subscription-key"></a>验证订阅密钥

可以通过运行以下命令之一来验证以确保拥有有效的订阅密钥。

> [!NOTE]
> 将 `YOUR_SUBSCRIPTION_KEY` 和 `YOUR_REGION` 分别替换为自己的订阅密钥和关联区域。

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>验证授权令牌

如果使用授权令牌进行身份验证，请运行以下命令之一以验证授权令牌是否仍然有效。 令牌的有效期为 10 分钟。

> [!NOTE]
> 将 `YOUR_AUDIO_FILE` 替换为预先录制的音频文件的路径，将 `YOUR_ACCESS_TOKEN` 替换为上一步中返回的授权令牌，将 `YOUR_REGION` 替换为正确的区域。

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>错误 `HTTP 400 Bad Request`

当请求正文包含无效的音频数据时，通常会发生此错误。 仅支持 `WAV` 格式。 还要检查请求的标头，以确保指定适当的 `Content-Type` 和 `Content-Length`。

## <a name="error-http-408-request-timeout"></a>错误 `HTTP 408 Request Timeout`

出现该错误很可能是因为未将音频数据发送到服务。 此错误也可能是由网络问题所导致。

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>响应中的 `RecognitionStatus` 为 `InitialSilenceTimeout`

音频数据通常是导致此问题的原因。 例如：

* 音频开头有一段很长的静音。 该服务将在几秒钟后停止识别并返回 `InitialSilenceTimeout`。
* 音频使用了不受支持的编解码器格式，这导致音频数据被视为静音。

## <a name="next-steps"></a>后续步骤

* [发行说明](releasenotes.md)

