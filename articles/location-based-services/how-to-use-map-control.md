---
title: 如何使用 Azure Location Based Services Map Control | Microsoft 文档
description: 了解如何使用 Azure Location Based Services Map Control 客户端 Javascript 库。
services: location-based-services
author: kgremban
ms.author: kgremban
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: ee767c9461f79437ab49d2a919bb82e7de8feba7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>如何使用 Azure Location Based Services Map Control
可通过 Map Control 客户端 Javascript 库呈现地图，并将 Azure Location Based Services 功能嵌入 Web 或移动应用中。 

## <a name="prerequisites"></a>先决条件
Azure Location Based Services 帐户和密钥。 有关创建帐户和检索密钥的信息，请参阅[如何管理 Azure Location Based Services 帐户和密钥](how-to-manage-account-keys.md)。 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>在网页上使用 Map Control API 创建新地图
通过使用 Map Control 客户端 Javascript 库，可以在网页中嵌入地图。

1. 创建一个新文件并将其命名为 MapSearch.html。

2. 向该文件 `<head>` 元素添加 Azure Location Based Services 样式表和脚本源引用：

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
    
4. 若要初始化 Map Control，在 html 正文中定义新部分并创建脚本。 在脚本中使用自己的 Azure Location Based Services 帐户密钥。 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. 在 Web 浏览器中打开该文件并查看呈现的地图。