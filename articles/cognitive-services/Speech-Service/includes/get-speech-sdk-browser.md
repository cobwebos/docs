---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 2a4f0cfbdc6a88c2f32b60e8ac3178ef5bd4f8dd
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035519"
---
:::row:::
    :::column span="3":::
        Speech SDK for JavaScript 作为 npm 包提供，请参阅<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">cognitiveservices account- <span class="docon docon-navigate-external x-hidden-focus"></span> </a> <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a>-----------------
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 尽管 Speech SDK for JavaScript 作为 npm 包提供，因此客户端 web 浏览器和 Node.js 都可以使用它-请考虑每个环境的各种体系结构含义。 例如，<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">文档对象模型（DOM） <span class="docon docon-navigate-external x-hidden-focus"></span> </a>对服务器端应用程序不可用，因为<a href="https://nodejs.org/api/fs.html" target="_blank">文件系统 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>不适用于客户端应用程序。

### <a name="nodejs-package-manager-npm"></a>Node.js 包管理器 (NPM)

若要安装 Speech SDK for JavaScript，请运行以下 `npm install` 命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 脚本标记

或者，可以直接在 HTML `<head>` 元素中包含一个 `<script>` 标记，该标记依赖于 <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">JSDelivr  NPM syndicate <span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

有关详细信息，请参阅 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Web 浏览器语音 SDK 快速入门<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
