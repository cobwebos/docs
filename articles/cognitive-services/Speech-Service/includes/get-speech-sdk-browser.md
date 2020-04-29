---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399922"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK 可用作 npm 包，请参阅<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">cognitiveservices account- <span class="docon docon-navigate-external x-hidden-focus"></span> </a> <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>------------------
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 尽管 JavaScript Speech SDK 可用作 npm 包，但客户端 web 浏览器和 node.js 都可以使用它-请考虑每个环境的各种体系结构含义。 例如，<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">文档对象模型（DOM） <span class="docon docon-navigate-external x-hidden-focus"></span> </a>对服务器端应用程序不可用，因为<a href="https://nodejs.org/api/fs.html" target="_blank">文件系统<span class="docon docon-navigate-external x-hidden-focus"></span> </a>不适用于客户端应用程序。

### <a name="nodejs-package-manager-npm"></a>Node.js 包管理器（NPM）

若要安装 JavaScript Speech SDK，请运行以下`npm install`命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 脚本标记

或者，你可以直接在 HTMLs `<script>` `<head>`元素中包含一个标记，该标记依赖于<a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM <span class="docon docon-navigate-external x-hidden-focus"></span>联合</a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

有关详细信息，请参阅<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Web 浏览器语音 SDK <span class="docon docon-navigate-external x-hidden-focus"></span>快速入门</a>。
