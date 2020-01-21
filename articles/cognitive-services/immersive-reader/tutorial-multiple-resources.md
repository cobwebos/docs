---
title: 教程：集成多个沉浸式阅读器资源
titleSuffix: Azure Cognitive Services
description: 在本教程中，我们将创建一个 Node.js 应用程序，该应用程序使用多个沉浸式阅读器资源启动沉浸式阅读器。
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046473"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>教程：集成多个沉浸式阅读器资源

在[概述](./overview.md)中，你了解了沉浸式阅读器是什么以及它如何实施经过验证的技术以提高语言学习者、新兴读者和有学习差异的学生的阅读理解能力。 在 [Node.js 快速入门](./quickstart-nodejs.md)中，你学习了如何将沉浸式阅读器与单个资源配合使用。 本教程介绍如何将多个沉浸式阅读器资源集成到同一个应用程序中。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在现有资源组下创建多个沉浸式阅读器资源
> * 使用多个资源启动沉浸式阅读器

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

* 按照[快速入门](./quickstart-nodejs.md)操作，创建一个使用 NodeJS 来启动沉浸式阅读器的 Web 应用。 在该快速入门中，我们配置单个沉浸式阅读器资源。 在本教程中，我们将在其基础上进行构建。

## <a name="create-the-immersive-reader-resources"></a>创建沉浸式阅读器资源

按照[这些说明](./how-to-create-immersive-reader.md)操作，创建每个沉浸式阅读器资源。 **Create-ImmersiveReaderResource** 脚本将 `ResourceName`、`ResourceSubdomain` 和 `ResourceLocation` 作为参数。 对于创建的每个资源，它们应该独一无二。 其余参数应与设置第一个沉浸式阅读器资源时使用的参数相同。 这样一来，每个资源都可以关联到同一个 Azure 资源组和 Azure AD 应用程序。

下面的示例演示如何创建两个资源，一个在 WestUS 中，另一个在 EastUS 中。 记下 `ResourceName`、`ResourceSubdomain` 和 `ResourceLocation` 的唯一值。

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>向环境配置添加资源

在快速入门中，你创建了一个包含 `TenantId`、`ClientId`、`ClientSecret` 和 `Subdomain` 参数的环境配置文件。 由于所有资源都使用相同的 Azure AD 应用程序，因此我们可以对 `TenantId`、`ClientId` 和 `ClientSecret` 使用相同的值。 需要做的唯一更改是列出每个资源的每个子域。

新的 __.env__ 文件现在应如下所示：

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

请确保不要将此文件提交到源代码管理中，因为它包含不应公开的机密。

接下来，我们将修改创建的 _routes\index.js_ 文件，使之支持多个资源。 将其内容替换为以下代码。

与以前一样，此代码创建一个 API 终结点，该终结点使用服务主体密码获取 Azure AD 身份验证令牌。 这一次，它使用户能够指定资源位置，并将其作为查询参数传入。 然后它返回一个包含令牌和相应子域的对象。

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
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
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**getimmersivereaderlaunchparams** API 终结点应该以某种形式的身份验证（例如，[OAuth](https://oauth.net/2/)）进行保护，以防止未经授权的用户获取令牌以用于你的沉浸式阅读器服务和计费；该内容超出了本教程的范围。

## <a name="launch-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

1. 打开 views\index.pug，并将其内容替换为以下代码  。 此代码使用一些示例内容填充页面，并添加两个启动沉浸式阅读器的按钮。 一个用于启动 EastUS 资源的沉浸式阅读器，另一个用于 WestUS 资源。

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }

        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    ```

3. 我们的 Web 应用现已准备就绪。 运行以下命令以启动该应用：

    ```bash
    npm start
    ```

4. 打开浏览器并导航到 http://localhost:3000[](http://localhost:3000)。 你应该可以在页面上看到以上内容。 单击“EastUS 沉浸式阅读器”  按钮，或单击“WestUS 沉浸式阅读器”  按钮，以使用相应的资源启动沉浸式阅读器。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)
* 在[ GitHub ](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)上查看代码示例