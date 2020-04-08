---
title: 使用 Azure 地图服务模块 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Azure 地图服务模块使用 Microsoft Azure 地图 REST 服务。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 6e9d1f35d021c3381f9c2887dfb1c150bb720871
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804650"
---
# <a name="use-the-azure-maps-services-module"></a>使用 Azure 地图服务模块

Azure 地图 Web SDK 提供*服务模块*。 此模块是一个帮助库，通过使用 JavaScript 或 TypeScript，可以轻松地在 Web 或 Node.js 应用程序中使用 Azure 映射 REST 服务。

## <a name="use-the-services-module-in-a-webpage"></a>在网页中使用服务模块

1. 创建新的 HTML 文件。
1. 加载 Azure 映射服务模块。 您可以通过两种方式之一加载它：
    - 使用 Azure 地图服务模块的全局托管 Azure 内容传递网络版本。 添加对文件元素的`<head>`脚本引用：

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - 或者，使用[Azure 地图-rest](https://www.npmjs.com/package/azure-maps-rest) npm 包在本地加载 Azure 地图 Web SDK 源代码的服务模块，然后将其与应用托管。 此程序包还包括了 TypeScript 定义。 使用此命令：
    
        > npm install azure-maps-rest****
    
        然后，添加对文件元素的`<head>`脚本引用：

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. 创建身份验证管道。 必须先创建管道，然后才能初始化服务 URL 客户端终结点。 使用自己的 Azure 地图帐户密钥或 Azure 活动目录 （Azure AD） 凭据对 Azure 地图搜索服务客户端进行身份验证。 在此示例中，将创建搜索服务 URL 客户端。 

    如果使用订阅密钥进行身份验证：

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    如果使用 Azure AD 进行身份验证：

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    有关详细信息，请参阅使用[Azure 映射进行身份验证](azure-maps-authentication.md)。

1. 以下代码使用新创建的 Azure 地图搜索服务 URL 客户端对地址进行地理编码："1 微软方式，雷德蒙德，华盛顿州"。 代码使用 函数`searchAddress`并将结果显示为页面正文中的表。

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    下面是完整的正在运行的代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用服务模块" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （）<a href='https://codepen.io/azuremaps/pen/zbXGMR/'>使用服务模块</a>的笔。
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure 政府云支持

Azure 地图 Web SDK 支持 Azure 政府云。 用于访问 Azure 地图 Web SDK 的所有 JavaScript 和 CSS URL 保持不变，但需要执行以下任务才能连接到 Azure 地图平台的 Azure 政府云版本。

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

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [地图URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [搜索URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [路由URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [订阅密钥证书](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [令牌凭据](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

有关使用服务模块的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [在地图上显示搜索结果](./map-search-location.md)

> [!div class="nextstepaction"]
> [从坐标获取信息](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [显示从 A 到 B 的路线](./map-route.md)
