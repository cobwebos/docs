---
title: 如何使用 Azure Maps Map Control | Microsoft Docs
description: 了解如何使用 Azure Maps Map Control 客户端 Javascript 库。
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: bbd06aad9052d2a775c35dd08f80462f8ea505a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-the-azure-maps-map-control"></a>如何使用 Azure Maps Map Control
可通过 Map Control 客户端 Javascript 库呈现地图，并将 Azure Maps 功能嵌入到 Web 或移动应用中。 

## <a name="create-a-new-map-in-a-web-page"></a>在网页中创建新地图

通过使用 Map Control 客户端 Javascript 库，可以在网页中嵌入地图。

1. 创建一个新文件并将其命名为 MapSearch.html。

2. 向该文件的 `<head>` 元素添加 Azure Maps 样式表和脚本源引用：

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. 若要在浏览器中呈现新地图，请在 `<style>` 元素中添加 #map 引用。

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. 若要初始化 Map Control，在 html 正文中定义新部分并创建脚本。 在脚本中使用你自己的 Azure Maps 帐户密钥。 如果需要创建帐户或查找密钥，请参阅[如何管理 Azure Maps 帐户和密钥](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. 在 Web 浏览器中打开该文件并查看呈现的地图。

## <a name="next-steps"></a>后续步骤

本文已展示了如何使用 Azure Maps 密钥创建基本地图。 有关可向地图添加的更多代码示例，请参阅以下文章： 

* [创建地图](map-create.md)
* [添加图钉](map-add-pin.md)