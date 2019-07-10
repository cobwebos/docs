---
title: 教程：启动沉浸式阅读器 (Node.js)
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将从头开始构建 Web 应用程序并添加沉浸式阅读器功能。
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ac90496c950d8a563bf8794b4c1bb105b6c12232
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444062"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>教程：启动沉浸式阅读器 (Node.js)

在[概述](./overview.md)中，你了解了沉浸式阅读器是什么以及它如何实施经过验证的技术以提高语言学习者、新兴读者和有学习差异的学生的阅读理解能力。 本教程介绍如何创建启动沉浸式阅读器的 Node.js Web 应用程序。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Express 创建 Node.js Web 应用程序
> * 获取访问令牌
> * 启动沉浸式阅读器以显示示例内容
> * 指定内容的语言
> * 指定沉浸式阅读器界面的语言
> * 启动沉浸式阅读器以显示数学内容

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 沉浸式阅读器的订阅密钥。 按照[这些说明](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)获取一个订阅密钥。
* [Node.js](https://nodejs.org/) 和 [Yarn](https://yarnpkg.com)
* IDE 如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>使用 Express 创建 Node.js Web 应用程序

使用 `express-generator` 工具创建 Node.js Web 应用程序。

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

安装 YARN 依赖项，并添加 `request` 和 `dotenv` 依赖项，以便在后续教程中使用。

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>获取访问令牌

接下来，编写后端 API 来使用订阅密钥检索访问令牌。 需要订阅密钥和终结点才能完成下一步。 可以在 Azure 门户中的沉浸式阅读器资源的“秘钥”页中找到订阅密钥。 可以在“概述”页面中找到终结点。

拥有订阅密钥和终结点后，创建一个名为 .env 的新文件，并将下面的代码粘贴到其中，将 `{YOUR_SUBSCRIPTION_KEY}` 和 `{YOUR_ENDPOINT}` 分别替换为订购密钥和终结点  。

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

请确保不要将此文件提交到源代码管理中，因为它包含不应公开的秘密。

接下来，打开 app.js 并将以下内容添加到文件顶部  。 这会将订阅密钥和终结点作为环境变量加载到 Node 中。

```javascript
require('dotenv').config();
```

打开 routes\index.js 文件，并在文件顶部进行以下导入  ：

```javascript
var request = require('request');
```

接下来，在该行的正下方添加以下代码。 此代码创建一个 API 终结点，它使用订阅密钥获取访问令牌，然后返回该令牌。

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

应该以某种形式的身份验证（例如 [OAuth](https://oauth.net/2/)）来保护此 API 终结点；这项工作超出了本教程的范围。

## <a name="launch-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

1. 打开 views\layout.pug，并在 `head` 标记之下、`body` 标记之上添加以下代码  。 这些 `script` 标记将加载[沉浸式阅读器 SDK ](https://github.com/Microsoft/immersive-reader-sdk)和 jQuery。

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. 打开 views\index.pug，并将其内容替换为以下代码  。 此代码使用一些示例内容填充页面，并添加启动沉浸式阅读器的按钮。

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. 我们的网络应用现已准备就绪。 运行以下命令以启动该应用：

    ```bash
    npm start
    ```

4. 打开浏览器并导航到 http://localhost:3000  。 你应该可以在页面上看到以上内容。 单击沉浸式阅读器按钮，启动沉浸式阅读器以显示你的内容  。

## <a name="specify-the-language-of-your-content"></a>指定内容的语言

沉浸式阅读器支持许多不同的语言。 可以按照以下步骤指定内容的语言。

1. 打开 views\index.pug 并在上一步中添加的 `p(id=content)` 标记下方添加以下代码  。 此代码将一些西班牙语内容添加到你的页面。

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. 在 JavaScript 代码中，在对 `ImmersiveReader.launchAsync` 的调用上方添加以下内容。 此代码将西班牙语内容传递到沉浸式阅读器。

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. 再次导航到 http://localhost:3000  。 应可在页面上看到西班牙语文本，当点击“沉浸式阅读器”时，该文本也会显示在沉浸式阅读器中  。

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>指定沉浸式阅读器界面的语言

默认情况下，沉浸式阅读器界面的语言与浏览器的语言设置相匹配。 还可以使用以下代码指定沉浸式阅读器界面的语言。

1. 在 views\index.pug 中，将对 `ImmersiveReader.launchAsync(token, content)` 的调用替换为下面的代码  。

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. 导航到 _http://localhost:3000_ 。 启动沉浸式阅读器时，界面将显示法语。

## <a name="launch-the-immersive-reader-with-math-content"></a>启动沉浸式阅读器以显示数学内容

可以使用 [MathML ](https://developer.mozilla.org/en-US/docs/Web/MathML)在沉浸式阅读器中包含数学内容。

1. 修改 views\index.pug，在对 `ImmersiveReader.launchAsync` 的调用上方包含以下代码  ：

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. 导航到 _http://localhost:3000_ 。 启动沉浸式阅读器并滚动到底部时，将看到数学公式。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/Microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)
* 在[ GitHub ](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp)上查看代码示例