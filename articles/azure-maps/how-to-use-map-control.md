---
title: 开始使用 Azure Maps 中的 web 地图控件 |Microsoft Docs
description: 了解如何使用 Azure Maps Map Control 客户端 Javascript 库。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: aa923fa7e2d5e673e6a2db2b349e54d433d1817b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957272"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure Maps Map Control

可通过 Map Control 客户端 Javascript 库呈现地图，并将 Azure Maps 功能嵌入到 Web 或移动应用中。

## <a name="create-a-new-map-in-a-web-page"></a>在网页中创建新地图

通过使用 Map Control 客户端 Javascript 库，可以在网页中嵌入地图。

1. 创建新的 HTML 文件。

2. 载入 Azure Maps Web SDK。 可以使用以下两个选项之一执行此操作：

    a. 通过在该文件的 `<head>` 元素中添加样式表和脚本引用的 URL 终结点，使用 Azure Maps Web SDK 的全局承载的 CDN 版本：

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. 另外，还可以使用 [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) NPM 程序包将 Azure Maps Web SDK 源代码加载到本地并将其与你的应用承载在一起。 此程序包还包括了 TypeScript 定义。

    > npm install azure-maps-control

    然后，在该文件的 `<head>` 元素中添加对 Azure Maps 样式表和脚本源的引用：

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. 若要以填满整个页面正文的方式呈现地图，请向 `<head>` 元素中添加以下 `<style>` 元素。

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. 在页面的正文中，添加一个 `<div>` 元素并将其 `id` 指定为 **myMap**。

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. 要初始化地图控件，请在 html 正文中定义新部分并创建脚本。 传入`id`的映射`<div>`或`HTMLElement`(例如， `document.getElementById('myMap')`) 作为第一个参数创建的实例时`Map`类。 通过[身份验证选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)使用你自己的 Azure Maps 帐户密钥或 Azure Active Directory (AAD) 凭据对地图进行身份验证。 如果需要创建帐户或查找密钥，请参阅[如何管理 Azure Maps 帐户和密钥](how-to-manage-account-keys.md)。 **language** 选项指定用于地图标签和控件的语言。 有关受支持语言的详细信息，请参阅[支持的语言](supported-languages.md)。 如果使用订阅密钥进行身份验证：

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    如果使用 Azure Active Directory (AAD) 进行身份验证：

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    有关详细信息，请参阅[身份验证与 Azure Maps](azure-maps-authentication.md)的更多详细信息。

6. （可选）在页面的头部添加以下元标记元素，你会发现这比较有用：

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. 将它放在一起 HTML 文件应类似于以下代码：

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. 在 Web 浏览器中打开该文件并查看呈现的地图。 它应如以下代码所示：

    <iframe height="700" style="width: 100%;" scrolling="no" title="如何使用地图控件" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">请参阅笔<a href='https://codepen.io/azuremaps/pen/yZpEYL/'>如何使用 map control</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
    </iframe>

## <a name="next-steps"></a>后续步骤

了解如何创建地图并与之进行交互：

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

了解如何设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](choose-map-style.md)
