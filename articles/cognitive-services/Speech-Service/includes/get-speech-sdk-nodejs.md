---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: a8c61b526f0f0292909cc1b90391dbae2166ccaa
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186229"
---
:::row:::
    :::column span="3":::
        Speech SDK for JavaScript 作为 npm 包提供，请参阅<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">cognitiveservices account- <span class="docon docon-navigate-external x-hidden-focus"></span> </a> <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a>---------------------------------
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 尽管 Speech SDK for JavaScript 作为 npm 包提供，但 Node.js 和客户端 web 浏览器都可以使用它-请考虑每个环境的各种体系结构含义。 例如， <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank"> (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> 的文档对象模型</a>对服务器端应用程序不可用，因为该<a href="https://nodejs.org/api/fs.html" target="_blank">文件系统 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>不适用于客户端应用程序。

### <a name="nodejs-package-manager-npm"></a>Node.js 包管理器 (NPM)

若要安装 Speech SDK for JavaScript，请运行以下 `npm install` 命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

有关详细信息，请参阅 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js 语音 SDK 快速入门<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
