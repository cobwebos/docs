---
title: 开始使用 JavaScript 中的 Microsoft 语音识别 API | Microsoft Docs
description: 使用认知服务中的 Microsoft 语音识别 API 开发将语音音频持续转换为文本的应用程序。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 12/21/2017
ms.author: zhouwang
ms.openlocfilehash: 04332c453d22122e65a758a65b09e17300e07f02
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040532"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>开始使用 JavaScript 中的语音识别 API

通过使用语音识别 API，可以开发将语音音频转换为文本的应用程序。 JavaScript 客户端库使用[语音服务 WebSocket 协议](../API-Reference-REST/websocketprotocol.md)，通过它可以同时探讨和接收转录文本。 本文有助于开始使用 JavaScript 中的语音识别 API。

## <a name="prerequisites"></a>先决条件

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>订阅语音识别 API，并获得免费试用的订阅密钥

语音 API 是认知服务的一部分。 可以从[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)页面获取免费试用的订阅密钥。 选择语音 API 后，选择“获取 API 密钥”以获取密钥。 它将返回主密钥和辅助密钥。 两个密钥都绑定到相同的配额，因此你可以使用任一密钥。

> [!IMPORTANT]
> 获取订阅密钥。 必须拥有[订阅密钥](https://azure.microsoft.com/try/cognitive-services/)才能使用语音客户端库。

## <a name="get-started"></a>入门

在本节中，我们将逐步介绍加载示例 HTML 页的必要步骤。 示例位于 [github 存储库](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)。 可以从存储库直接打开示例，或从存储库的本地副本打开示例。 

> [!NOTE]
> 某些浏览器阻止麦克风访问不安全的源。 因此，建议以 https 承载“示例”/“应用”，使其在所有支持的浏览器上正常工作。 

### <a name="open-the-sample-directly"></a>直接打开示例

获取订阅密钥，如上所述。 然后打开[示例链接](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html)。 该操作将在默认浏览器中加载页面（使用 [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com) 呈现）。

### <a name="open-the-sample-from-a-local-copy"></a>从本地副本打开示例

若要在本地试用示例，克隆此存储库：

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

编译 TypeScript 源并将其捆绑到单个 JavaScript 文件（需要在计算机上安装 [npm](https://www.npmjs.com/)）。 更改为克隆存储库的根目录，并运行命令：

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

在喜爱的浏览器中打开 `samples\browser\Sample.html`。

## <a name="next-steps"></a>后续步骤

关于如何将 SDK 包含在自己的网页中，其详细信息在[此处](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)提供。

## <a name="remarks"></a>备注

- 语音识别 API 支持三种[识别模式](../concepts.md#recognition-modes)。 可以通过更新 Sample.html 文件中提供的 Setup() 函数切换模式。 该示例默认将模式设置为 `Interactive`。 若要更改模式，请将参数 `SR.RecognitionMode.Interactive` 更新为另一种模式。 例如，将参数更改为 `SR.RecognitionMode.Conversation`。
- 有关受支持语言的完整列表，请参阅[支持的语言](../API-Reference-REST/supportedlanguages.md)。

## <a name="related-topics"></a>相关主题

- [JavaScript 语音识别 API 示例存储库](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [REST API 入门](GetStartedREST.md)
