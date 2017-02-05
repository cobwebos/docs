---
title: "机器学习建议：JavaScript 集成 | Microsoft Docs"
description: "Azure 机器学习建议 - JavaScript 集成 - 文档"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: bbbb5bb6-489d-4a62-a2ae-f36237e9e2e1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 12/08/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: dc07007eec860e0ad7342a4926c6797341719c5b
ms.openlocfilehash: 20791d5729a65497b5f76f929bf331906ac65818


---
# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Azure 机器学习建议 - JavaScript 集成
> [!NOTE]
> 应开始使用建议 API 认知服务，而非此版本。 建议认知服务将替代此服务，并且所有新功能都将在该处开发。 它具有新功能，如支持批处理、更好用的 API 资源管理器、更简洁的 API 图面、更一致的注册/计费体验等。
> 了解有关[迁移到新认知服务](http://aka.ms/recomigrate)的详细信息
> 
> 

本文档介绍如何集成使用 JavaScript 的站点。 借助 JavaScript，可以发送数据采集事件，以及在生成建议模型后使用这些建议。 通过 JS 执行的所有操作还可以从服务器端执行。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="1-general-overview"></a>1.一般概述
将你的站点与 Azure ML 建议集成包含 2 个阶段：

1. 将事件发送到 Azure ML 建议。 这将支持生成建议模型。
2. 使用建议。 生成模型后，即可使用建议。 （本文档未介绍如何生成模型，阅读快速入门指南可获取操作方法的详细信息）。

<ins>第一阶段</ins>

在第一阶段，将一个较小的 JavaScript 库插入你的 html 页面，使该页面可以将在 html 页面中发生的事件发送到 Azure ML 建议服务器（通过数据市场）：

![图 1][1]

<ins>第二阶段</ins>

在第二阶段，当想要在页面上显示这些建议时，请选择以下选项之一：

1.你的服务器（在页面呈现阶段）将调用 Azure ML 建议服务器（通过数据市场），获取建议。 结果包含项目 ID 列表。 你的服务器需要使用元数据项（例如，图片、描述）丰富结果，然后向浏览器发送生成的页面。

![图 2][2]

2.其他选项将使用第一阶段的较小 JavaScript 文件，获取简单的建议项列表。 相较于第一个选项，此处收到的数据更精简。

![图 3][3]

## <a name="2-prerequisites"></a>2.先决条件
1. 使用 API 创建新模型。 有关如何执行此操作，请参阅快速入门指南。
2. 使用 base64 编码你的 &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt;。 （这将用于基本身份验证，支持 JS 代码调用 API）。

## <a name="3-send-data-acquisition-events-using-javascript"></a>3.使用 JavaScript 发送数据采集事件
以下步骤利于发送事件：

1. 将 JQuery 库包含在代码中。 可以从以下 URL 中的 nuget 下载它。
   
     http://www.nuget.org/packages/jQuery/1.8.2
2. 包含以下 URL 中的建议 JavaScript 库：http://aka.ms/RecoJSLib1
3. 使用合适参数初始化 Azure ML 建议库。
   
     <script>
         AzureMLRecommendationsStart("<base64encoding of username:key>",       "<model_id>");   </script>
4. 发送相应事件。 有关所有类型的事件（例如，单击事件），请参阅以下详细信息部分
   
     <script>
         if (typeof AzureMLRecommendationsEvent=="undefined") {         
                     AzureMLRecommendationsEvent = []; } AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });   </script>

### <a name="31----limitations-and-browser-support"></a>3.1.    限制和浏览器支持
这是一个参考实现，按原样提供。 它应支持所有主流浏览器。

### <a name="32----type-of-events"></a>3.2.    事件类型
该库支持 5 种类型的事件：单击、建议单击、添加到购物车、从购物车中删除和购买。 还有用于设置名为登录的用户上下文的其他事件。

#### <a name="321-click-event"></a>3.2.1. 单击事件
只要用户单击某个项，就应该使用此事件。 通常当用户单击某个项时，会打开含有该项详细信息的页面；在此页面中，应该触发此事件。

参数：

* event（字符串，必填） - “click”
* item（字符串，必填） - 项目的唯一标识符
* itemName（字符串，可选） - 项目名称
* itemDescription（字符串，可选） - 项目的描述
* itemCategory（字符串，可选） - 项目类别
  
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

或带可选数据：

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


#### <a name="322-recommendation-click-event"></a>3.2.2. 建议单击事件
只要用户单击从 Azure ML 建议作为建议项收到的项，就应该使用此事件。 通常当用户单击某个项时，会打开含有该项详细信息的页面；在此页面中，应该触发此事件。

参数：

* event（字符串，必填） - “recommendationclick”
* item（字符串，必填） - 项目的唯一标识符
* itemName（字符串，可选） - 项目名称
* itemDescription（字符串，可选） - 项目的描述
* itemCategory（字符串，可选） - 项目类别
* seeds（字符串数组，可选） - 生成的建议查询种子。
* recoList（字符串数组，可选） - 生成的单击项的建议请求结果。
  
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

或带可选数据：

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]                 });
        </script>


#### <a name="323-add-shopping-cart-event"></a>3.2.3. 添加购物车事件
当用户将项添加到购物车时，应该使用此事件。
参数：

* event（字符串，必填） - “addshopcart”
* item（字符串，必填） - 项目的唯一标识符
* itemName（字符串，可选） - 项目名称
* itemDescription（字符串，可选） - 项目的描述
* itemCategory（字符串，可选） - 项目类别
  
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

#### <a name="324-remove-shopping-cart-event"></a>3.2.4. 移除购物车事件
当用户移除购物车中的项时，应该使用此事件。

参数：

* event（字符串，必填） - “removeshopcart”
* item（字符串，必填） - 项目的唯一标识符
* itemName（字符串，可选） - 项目名称
* itemDescription（字符串，可选） - 项目的描述
* itemCategory（字符串，可选） - 项目类别
  
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

#### <a name="325-purchase-event"></a>3.2.5. 购买事件
当用户购买其购物车中物品时，应该使用此事件。

参数：

* event（字符串） - “purchase”
* items (Purchased[]) - 存有每个已购买项的条目的数组。<br><br>
  已购买项的格式：
  * item（字符串） - 项目的唯一标识符。
  * count（整型或字符串） - 已购买项的数目。
  * price（浮点型或字符串） - 可选字段 - 项目的价格。

以下示例显示 3 个购买项（33、34、35），其中两个项的所有字段（item、count、price）已填充，另外一个项（即项 34）未填充价格。

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

#### <a name="326-user-login-event"></a>3.2.6. 用户登录事件
为了识别来自同一个浏览器的事件，Azure ML 建议事件库会创建并使用 cookie。 为了改进模型结果，Azure ML 建议支持设置可覆盖 cookie 使用的唯一用户标识。

在用户登录到你的站点后，应该使用此事件。

参数：

* event（字符串） - “userlogin”
* user（字符串） - 用户的唯一标识。
  
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "userlogin", user: “ABCD10AA” });
        </script>

## <a name="4-consume-recommendations-via-javascript"></a>4.通过 JavaScript 使用建议
使用建议的代码由客户端网页的某些 JavaScript 事件触发。 建议响应包括建议项 ID、建议项名称和建议项评分。 最好是仅针对建议项的列表显示使用此选项 - 更复杂的处理（例如添加项的元数据）应在服务器端集成上执行。

### <a name="41-consume-recommendations"></a>4.1 使用建议
若要使用建议，需要将所需 JavaScript 库包含在你的页面中，并调用 AzureMLRecommendationsStart。 请参阅第 2 节。

若要使用一个或多个项的建议，需要调用名为 AzureMLRecommendationsGetI2IRecommendation 的方法。

参数：

* items（字符串数组） - 要获取其建议的一个或多个项。 如果使用 Fbt 生成，则只可以在此处设置一个项。
* numberOfResults（整型） - 所需的结果数。
* includeMetadata（布尔值，可选） - 如果设置为“true”，表示在结果中必须填充元数据字段。
* Processing 函数 - 处理返回建议的函数。 数据返回为含有以下内容的数组：
  * Item - 项目唯一 ID
  * name - 项目名称（如果目录中存在）
  * rating - 建议评分
  * metadata - 表示项目元数据的字符串

示例：以下代码请求项“64f6eb0d-947a-4c18-a16c-888da9e228ba”的 8 个建议（未指定 includeMetadata - 暗示不需要任何元数据），然后将结果连结到缓冲区。

        <script>
             var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                 var buff = "";
                 for (var ii = 0; ii < reco.length; ii++) {
                       buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                 }
                 alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png



<!--HONumber=Dec16_HO2-->


