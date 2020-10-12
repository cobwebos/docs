---
title: Microsoft Azure 地图 web 地图控件入门
description: 了解如何使用 Azure Maps 中的 Map Control 客户端 JavaScript 库将映射添加到 web 和移动应用程序。 请参阅如何本地化映射。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 19db4fb0d75369a0f272eef1180e86f47d45d284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335324"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure Maps 地图控件

Map Control 客户端 JavaScript 库允许你将地图和嵌入 Azure Maps 功能呈现到你的 web 或移动应用程序中。

## <a name="prerequisites"></a>必备条件

若要在网页中使用 Map Control，必须具备以下先决条件之一：

* [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account) 并 [获取主要订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也称为主密钥或订阅密钥。

* 获取 Azure Active Directory (具有 [身份验证选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)的 AAD) 凭据。

## <a name="create-a-new-map-in-a-web-page"></a>在网页中创建新地图

您可以通过使用 Map Control 的客户端 JavaScript 库，将地图嵌入到网页中。

1. 创建新的 HTML 文件。

2. 载入 Azure Maps Web SDK。 可选择以下两个选项之一：

    * 通过在 HTML 文件的元素中添加对 JavaScript 和样式表的引用，使用 Azure Maps Web SDK 的全球托管 CDN 版本 `<head>` ：

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * 使用 [Azure Maps control](https://www.npmjs.com/package/azure-maps-control) NPM 包在本地加载 AZURE MAPS Web SDK 源代码，并将其与应用程序一起托管。 此程序包还包括了 TypeScript 定义。

      > **npm install azure-maps-control**

    然后，将对 Azure Maps 样式表的引用添加到该 `<head>` 文件的元素中：

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > 可以通过添加以下代码将 Typescript 定义导入到应用程序中：
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. 现在，我们将初始化地图控件。 若要对控件进行身份验证，你需要拥有 Azure Maps 订阅密钥，或使用具有 [身份验证选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)的 AZURE ACTIVE DIRECTORY (AAD) 凭据。

    如果使用订阅密钥进行身份验证，请复制以下脚本元素，并将其粘贴到 `<head>` 元素内和第一个 `<script>` 元素之下。 `<Your Azure Maps Key>`将替换为 Azure Maps 的主订阅密钥。

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            }
        });
    </script>
    ```

    如果使用 Azure Active Directory (AAD) 进行身份验证，请复制以下脚本元素，并将其粘贴到 `<head>` 元素内和第一个 `<script>` 元素之下。

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            }
        });
    </script>
   ```

    有关 Azure Maps 的身份验证的详细信息，请参阅 [Azure Maps 的身份验证](azure-maps-authentication.md) 文档。 此外，还提供了一个示例列表，其中显示了如何将 Azure Active Directory (AAD) 与 Azure Maps[集成。](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

    >[!TIP]
    >在此示例中，我们传入了 `id` 映射的 `<div>` 。 执行此操作的另一种方法是通过将 `HTMLElement` `document.getElementById('myMap')` 作为第一个参数传递来传入对象。

6. 或者，您可能会发现将以下 `meta` 元素添加到页面的元素中会很有帮助 `head` ：

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. 将其放在一起，HTML 文件应类似于以下标记：

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


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. 在 Web 浏览器中打开该文件并查看呈现的地图。 它应该如下图所示：

   ![显示呈现结果的地图图像](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>本地化地图

Azure Maps 提供了两种不同的方法来设置呈现地图的语言和区域视图。 第一种方法是将此信息添加到全局 `atlas` 命名空间，这会导致应用中的所有地图控件实例默认设置为这些设置。 下面将语言设置为法语 ( "fr" ) ，区域视图设置为 "自动"：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

第二种方法是在加载映射时将此信息传递到映射选项，如下所示：

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> 可以在具有不同语言和区域设置的同一页上加载多个映射实例。 此外，在使用映射的函数加载映射后，可以更新这些设置 `setStyle` 。

下面是将语言设置为 "fr" 并将区域视图设置为 "自动" 的 Azure Maps 的示例。

![以法语显示标签的地图图像](./media/how-to-use-map-control/websdk-localization.png)

[此文档](supported-languages.md)提供了支持的语言和区域视图的完整列表。

## <a name="azure-government-cloud-support"></a>Azure 政府版云支持

Azure Maps Web SDK 支持 Azure 政府云。 用于访问 Azure Maps Web SDK 的所有 JavaScript 和 CSS Url 保持不变。 需要完成以下任务才能连接到 Azure Maps 平台的 Azure 政府云版本。

使用交互式地图控件时，请在创建类的实例之前添加以下代码行 `Map` 。

```javascript
atlas.setDomain('atlas.azure.us');
```

验证地图和服务时，请确保使用 Azure 政府版云平台中 Azure Maps 的身份验证详细信息。

使用 "服务" 模块时，需要在创建 API URL 端点的实例时设置服务的域。 例如，下面的代码创建类的一个实例 `SearchURL` ，并将该域指向 Azure 政府云。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

如果直接访问 Azure Maps REST 服务，请将 URL 域更改为 `atlas.azure.us` 。 例如，如果使用搜索 API 服务，请将 URL 域从更改 `https://atlas.microsoft.com/search/` 为 `https://atlas.azure.us/search/` 。

## <a name="javascript-frameworks"></a>JavaScript 框架

如果使用 JavaScript 框架进行开发，则下述某一开源项目可能很有用：

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - 围绕 Azure Maps 的 Angular 10 包装器。
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure Maps Blazor 组件。
- [Azure Maps React 组件](https://github.com/WiredSolutions/react-azure-maps) - Azure Maps 控件的 React 包装器。
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Vue 应用程序的 Azure Maps 组件。

## <a name="next-steps"></a>后续步骤

了解如何创建地图并与之进行交互：

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

了解如何设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](choose-map-style.md)

将更多数据添加到地图：

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)

有关显示如何将 Azure Active Directory (AAD) 与 Azure Maps 集成的示例列表，请参阅：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
