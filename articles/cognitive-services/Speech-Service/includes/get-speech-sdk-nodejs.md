---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 3b7f459e0e95e798e5430414d23f34a455719a40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377459"
---
:::row:::
    :::column span="3":::
        JavaScript 语音 SDK 以 npm 包的形式提供（请参阅 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span></a> 及其配套的 GitHub 存储库库 <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"></span></a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 尽管适用于 JavaScript 的语音 SDK 以 npm 包的形式提供，但是 Node.js 和客户端 Web 浏览器都可以使用它 - 请考虑每种环境的各种体系结构含义。 例如， <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank"> (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> 的文档对象模型</a>对服务器端应用程序不可用，因为该<a href="https://nodejs.org/api/fs.html" target="_blank">文件系统 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>不适用于客户端应用程序。

### <a name="nodejs-package-manager-npm"></a>Node.js 包管理器 (NPM)

若要安装 JavaScript 语音 SDK，请运行以下 `npm install` 命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

有关详细信息，请参阅 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js 语音 SDK 快速入门<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
