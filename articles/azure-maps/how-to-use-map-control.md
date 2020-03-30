---
title: 开始使用 Web 地图控件 |微软 Azure 地图
description: 了解如何使用 Microsoft Azure 地图映射控制客户端 JavaScript 库，将地图和嵌入 Azure 地图功能呈现到 Web 或移动应用程序中。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335246"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure 地图映射控件

地图控件客户端 JavaScript 库允许您在 Web 或移动应用程序中呈现地图和嵌入 Azure 地图功能。

## <a name="create-a-new-map-in-a-web-page"></a>在网页中创建新地图

您可以使用地图控件客户端 JavaScript 库将地图嵌入网页中。

1. 创建新的 HTML 文件。

2. 载入 Azure Maps Web SDK。 您可以选择两个选项之一;您可以选择两个选项之一。

    * 通过在 HTML 文件`<head>`元素中添加对 JavaScript 和样式表的引用，使用全局托管的 Azure 地图 Web SDK 版本：

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * 使用[Azure 映射控件](https://www.npmjs.com/package/azure-maps-control)NPM 包在本地加载 Azure 映射 Web SDK 源代码，并将其与应用一起托管。 此程序包还包括了 TypeScript 定义。

        > **npm install azure-maps-control**

       然后，在该文件的 `<head>` 元素中添加对 Azure Maps 样式表和脚本源的引用：

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > 可以通过添加以下代码将类型脚本定义导入到应用程序中：
    >
    > ```Javascript
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
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. 要初始化地图控件，请在 html 正文中定义一个新的脚本标记。 在创建`Map`类`id`的实例时`<div>`，将`HTMLElement`映射或 （`document.getElementById('myMap')`例如 ）， 作为第一个参数传递。 通过[身份验证选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)使用你自己的 Azure Maps 帐户密钥或 Azure Active Directory (AAD) 凭据对地图进行身份验证。 

   如果需要创建帐户或查找密钥，请按照["创建帐户"中的](quick-demo-map-app.md#create-an-account-with-azure-maps)说明[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 

   **language** 选项指定用于地图标签和控件的语言。 有关受支持语言的详细信息，请参阅[支持的语言](supported-languages.md)。如果使用订阅密钥进行身份验证，请使用以下操作：

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

   如果使用 Azure 活动目录 （AAD） 进行身份验证，请使用以下操作：

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
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   此处列出了如何将 Azure 活动目录 （AAD） 与 Azure 映射集成[的示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)列表。 
    
   有关详细信息，请参阅 Azure[映射](azure-maps-authentication.md)文档和 Azure 映射[Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)。

6. （可选）在页面的头部添加以下元标记元素，你会发现这比较有用：

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. 将其全部放在一起的 HTML 文件应类似于以下代码：

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

8. 在 Web 浏览器中打开该文件并查看呈现的地图。 它应类似于下图：

   ![显示渲染结果的地图图像](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>本地化地图

Azure 地图提供了两种不同的方法来设置渲染地图的语言和区域视图。 第一个选项是将此信息添加到全局`atlas`命名空间，这将导致应用中的所有地图控制实例默认为这些设置。 下面将语言设置为法语（"fr-FR"），区域视图为"自动"：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

第二个选项是在加载地图时将此信息传递到地图选项中，如下所示：

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

> [!Note]
> 使用 Web SDK，可以在同一页上加载具有不同语言和区域设置的多个映射实例。 此外，这些设置可以在地图加载后使用`setStyle`地图的功能进行更新。 

下面是 Azure 映射的示例，其语言设置为"fr-FR"，区域视图设置为"自动"。

![以法语显示标签的地图图像](./media/how-to-use-map-control/websdk-localization.png)

[此文档](supported-languages.md)提供了支持的语言和区域视图的完整列表。

## <a name="azure-government-cloud-support"></a>Azure 政府云支持

Azure 地图 Web SDK 支持 Azure 政府云。 用于访问 Azure 地图 Web SDK 的所有 JavaScript 和 CSS URL 保持不变。 连接到 Azure 地图平台的 Azure 政府云版本需要执行以下任务。

使用交互式地图控件时，在创建`Map`类的实例之前添加以下代码行。 

```javascript
atlas.setDomain('atlas.azure.us');
```

在验证地图和服务时，请确保使用 Azure 政府云平台中的 Azure 映射身份验证详细信息。

使用服务模块时，需要在创建 API URL 终结点的实例时设置服务的域。 例如，以下代码创建类的`SearchURL`实例，并将域指向 Azure 政府云。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

如果直接访问 Azure 映射 REST 服务，则将`atlas.azure.us`URL 域更改为 。 例如，如果使用搜索 API 服务，则将 URL 域`https://atlas.microsoft.com/search/`从`https://atlas.azure.us/search/`更改为 。

## <a name="next-steps"></a>后续步骤

了解如何创建地图并与之进行交互：

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

了解如何设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](choose-map-style.md)

要向地图添加更多数据，请进行：

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)

有关如何将 Azure 活动目录 （AAD） 与 Azure 映射集成的示例列表，请参阅：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
