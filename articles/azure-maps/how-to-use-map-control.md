---
title: 如何使用 Azure Maps Map Control | Microsoft Docs
description: 了解如何使用 Azure Maps Map Control 客户端 Javascript 库。
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166011"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure Maps Map Control

可通过 Map Control 客户端 Javascript 库呈现地图，并将 Azure Maps 功能嵌入到 Web 或移动应用中。

## <a name="create-a-new-map-in-a-web-page"></a>在网页中创建新地图

通过使用 Map Control 客户端 Javascript 库，可以在网页中嵌入地图。

1. 创建一个新文件并将其命名为 MapSearch.html。

2. 向该文件的 `<head>` 元素添加 Azure Maps 样式表和脚本源引用：

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. 要在浏览器中呈现新地图，请在 `<style>` 元素中添加 #map 引用：

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. 要初始化地图控件，请在 html 正文中定义新部分并创建脚本。 在脚本中使用你自己的 Azure Maps 帐户密钥。 如果需要创建帐户或查找密钥，请参阅[如何管理 Azure Maps 帐户和密钥](how-to-manage-account-keys.md)。 setLanguage 方法指定用于地图标签和控件的语言。 有关受支持语言的详细信息，请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)。

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. 在 Web 浏览器中打开该文件并查看呈现的地图。

## <a name="next-steps"></a>后续步骤

了解如何使用完整示例创建地图：

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

了解如何设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](choose-map-style.md)
