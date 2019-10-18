---
title: 教程：使用 Node.js 启动沉浸式阅读器
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将从头开始构建 Web 应用程序并添加沉浸式阅读器功能。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 2a07e392170fb9e6993f4c560a4896a468d90820
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338504"
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

* 为 Azure Active Directory (Azure AD) 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./azure-active-directory-authentication.md)进行设置。 在配置环境属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
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

## <a name="acquire-an-azure-ad-authentication-token"></a>获取 Azure AD 身份验证令牌

接下来，编写后端 API 以检索 Azure AD 身份验证令牌。

此部分需要上述 Azure AD 身份验证配置先决条件步骤中的一些值。 请回头参考保存的该会话的文本文件。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

获得这些值后，创建一个名为“.env”  的新文件，并将以下代码粘贴到其中，提供上面的自定义属性值。 请勿包含引号或“{”和“}”字符。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

请确保不要将此文件提交到源代码管理中，因为它包含不应公开的机密。

接下来，打开 app.js 并将以下内容添加到文件顶部  。 这会将 .env 文件中定义的属性作为环境变量加载到 Node 中。

```javascript
require('dotenv').config();
```

打开 _routes\index.js_ 文件并将其内容替换为以下代码。

此代码创建一个 API 终结点，该终结点使用服务主体密码获取 Azure AD 身份验证令牌。 它还检索子域。 然后它返回一个包含令牌和子域的对象。

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }
        
                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});
 
/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

**getimmersivereaderlaunchparams** API 终结点应该以某种形式的身份验证（例如，[OAuth](https://oauth.net/2/)）进行保护，以防止未经授权的用户获取令牌以用于你的沉浸式阅读器服务和计费；该内容超出了本教程的范围。

## <a name="launch-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

1. 打开 views\layout.pug，并在 `head` 标记之下、`body` 标记之上添加以下代码  。 这些 `script` 标记将加载[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和 jQuery。

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
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
        
            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }
        
            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };
            
                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;
            
                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. 我们的 Web 应用现已准备就绪。 运行以下命令以启动该应用：

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

1. 在 views\index.pug 中，将对 `ImmersiveReader.launchAsync(token, subdomain, content)` 的调用替换为下面的代码  。

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
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

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)
* 在[ GitHub ](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)上查看代码示例
