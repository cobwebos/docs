---
title: "如何使用 Azure Location Based Services Map Control | Microsoft 文档"
description: "了解如何使用 Azure Location Based Services Map Control 客户端 Javascript 库。"
services: location-based-services
keywords: "未咨询 SEO 专家的情况下，不要添加或编辑关键字。"
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: how-to
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 9ddd11806accddb41c02c0c6681aac07bba25356
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>如何使用 Azure Location Based Services Map Control
可通过 Map Control 客户端 Javascript 库呈现地图，并将 Azure Location Based Services 功能嵌入 Web 或移动应用中。 

## <a name="prerequisites"></a>先决条件
Azure Location Based Services 帐户和订阅密钥。 有关创建帐户和检索订阅密钥的信息，请参阅[如何管理 Azure Location Based Services 帐户和密钥](how-to-manage-account-keys.md)。 

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
    
4. 若要初始化 Map Control，在 html 正文中定义新部分并创建脚本。 使用你自己在 Azure Location Based Services 帐户中的订阅密钥。 

    ```html
    <div id="map">
        <script>
            var subscriptionKey = "<_subscriptionKey_>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        <script>
    <div>
    ```
    
5. 在 Web 浏览器中打开该文件并查看呈现的地图。