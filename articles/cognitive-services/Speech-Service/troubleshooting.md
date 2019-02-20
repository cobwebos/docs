---
title: 排查语音 SDK 问题 - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文提供的信息可帮助你解决在使用语音服务 SDK 时可能遇到的问题。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: dbcdfd117a39939491914ebddb717f404e07f09c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859311"
---
# <a name="troubleshoot-the-speech-service-sdk"></a>排查语音服务 SDK 问题

本文提供的信息可帮助你解决在使用语音服务 SDK 时可能遇到的问题。

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>错误：WebSocket 升级失败，出现身份验证错误 (403)

你的区域或服务可能有错误的终结点。 请检查 URI 以确保它正确无误。

此外，你的订阅密钥或授权令牌可能有问题。 有关详细信息，请参阅后续部分。

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>错误：HTTP 403 禁止访问或 HTTP 401 未授权

此错误通常是由身份验证问题导致的。 没有有效 `Ocp-Apim-Subscription-Key` 或 `Authorization` 标头的连接请求将被拒绝，状态为 403 或 401。

* 如果在使用订阅密钥进行身份验证，则可能看到该错误的原因是：

    - 订阅密钥缺失或无效
    - 已超出订阅的使用配额

* 如果在使用授权令牌进行身份验证，则可能看到该错误的原因是：

    - 授权令牌无效
    - 授权令牌已过期

### <a name="validate-your-subscription-key"></a>验证订阅密钥

可以通过运行以下命令之一来验证你拥有有效的订阅密钥。

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

如果输入了有效的订阅密钥，则该命令将返回授权令牌，否则将返回错误。

### <a name="validate-an-authorization-token"></a>验证授权令牌

如果使用授权令牌进行身份验证，请运行以下命令之一以验证授权令牌是否仍然有效。 令牌的有效期为 10 分钟。

> [!NOTE]
> 将 `YOUR_AUDIO_FILE` 替换为预先录制的音频文件的路径。 将 `YOUR_ACCESS_TOKEN` 替换为上一步中返回的授权令牌。 将 `YOUR_REGION` 替换为正确的区域。

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

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

如果输入了有效的授权令牌，则该命令将返回音频文件的听录，否则将返回错误。

---

## <a name="error-http-400-bad-request"></a>错误：HTTP 400 错误请求

当请求正文包含无效的音频数据时，通常会发生此错误。 仅支持 WAV 格式。 此外，请检查请求的标头，以确保为 `Content-Type` 和 `Content-Length` 指定适当的值。

## <a name="error-http-408-request-timeout"></a>错误：HTTP 408 请求超时

出现该错误很可能是因为未将音频数据发送到服务。 此错误也可能是由网络问题所致。

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>响应中的“RecognitionStatus”是“InitialSilenceTimeout”

此问题通常是由音频数据引起的。 出现此错误的可能原因有：

* 音频开头有一段很长的静音。 在这种情况下，该服务将在几秒钟后停止识别并返回 `InitialSilenceTimeout`。

* 音频使用了不受支持的编解码器格式，这导致音频数据被视为静音。

## <a name="next-steps"></a>后续步骤

* [查看发行说明](releasenotes.md)
