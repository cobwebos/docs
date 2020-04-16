---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399922"
---
:::row:::
    :::column span="3":::
        JavaScript 语音 SDK 作为 npm 包提供，请参阅<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">微软认知服务-语音-sdk，<span class="docon docon-navigate-external x-hidden-focus"></span></a>它是配套的 GitHub 存储库<a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">认知服务-语音-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 尽管 JavaScript 语音 SDK 作为 npm 包提供，因此客户端 Web 浏览器和 Node.js 都可以使用它 - 考虑每个环境的各种体系结构含义。 例如，<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">文档对象模型<span class="docon docon-navigate-external x-hidden-focus"></span>（DOM）</a>不适用于服务器端应用程序，就像<a href="https://nodejs.org/api/fs.html" target="_blank">文件系统<span class="docon docon-navigate-external x-hidden-focus"></span></a>对客户端应用程序不可用一样。

### <a name="nodejs-package-manager-npm"></a>Node.js 包管理器 （NPM）

要安装 JavaScript 语音 SDK，请`npm install`运行以下命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 脚本标记

或者，您可以直接在 HTML`<script>``<head>`元素中包含一个标记，依赖于<a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr** NPM 辛迪加<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

有关详细信息，请参阅 Web<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">浏览器语音 SDK 快速<span class="docon docon-navigate-external x-hidden-focus"></span>入门</a>。
