---
title: 使用 Azure Maps 搜索服务搜索位置
description: 了解 Azure Maps 搜索服务。 请参阅如何将这组 Api 用于地理编码、反向地理编码、模糊搜索和反向交叉街道搜索。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/21/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2a322de383194f131395629d33456d7561397eb9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310979"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>使用 Azure Maps 搜索服务搜索位置

[Azure Maps 搜索服务](https://docs.microsoft.com/rest/api/maps/search)是一组 RESTful api，旨在帮助开发人员按姓名、类别和其他地理信息搜索地址、位置和业务列表。 除了支持传统的地理编码，服务还可以根据纬度和经度来反转地理编码地址和交叉街道。 搜索返回的纬度和经度值可用作其他 Azure Maps 服务（如 [路由](https://docs.microsoft.com/rest/api/maps/route) 和 [天气](https://docs.microsoft.com/rest/api/maps/weather) 服务）中的参数。

本文将指导如何进行以下操作：

* 使用 [搜索地址 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)为地址 (地理编码 address location) 请求纬度和经度坐标。
* 使用 [模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) (POI) 搜索地址或兴趣点。
* 进行 [反向地址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) ，将坐标位置转换为街道地址。
* 使用 [搜索地址反向交叉街道 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)将坐标位置转换为可识别人工的交叉街道。  大多数情况下，这是跟踪从设备或资产接收 GPS 源的应用程序所必需的，并且希望知道坐标所在的位置。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>请求地址的纬度和经度 (地理编码) 

在此示例中，我们将使用 Azure Maps [获取搜索地址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 将地址转换为纬度和经度坐标。 此过程也称为 *地理编码*。 除了返回坐标外，响应还会返回详细的地址属性，例如街道、邮政编码、市政府和国家/地区信息。

>[!TIP]
>如果你有一组要地理编码的地址，则可以使用 [Post Search 地址批处理 API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) 通过单个 API 调用发送一批查询。

1. 打开 Postman 应用。 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“集合”。  命名集合，然后选择“创建”按钮。 本文档中的示例的其余部分将使用此集合。

2. 若要创建请求，请再次选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中，输入请求名称。 选择在上一步中创建的集合，然后选择“保存”。

3. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 在此请求中，我们要搜索特定的地址： `400 Braod St, Seattle, WA 98109` 。

    对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. 单击蓝色的 " **发送** " 按钮。 响应正文将包含单一位置的数据。

5. 现在，我们将搜索包含多个可能位置的地址。 在 " **Params** " 部分中，将 `query` 键更改为 `400 Broad, Seattle` 。 单击蓝色的 " **发送** " 按钮。

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="搜索地址":::

6. 接下来，尝试将 `query` 密钥设置为 `400 Broa` 。

7. 单击“发送”按钮。  你现在可以看到响应包含来自多个国家/地区的响应。 若要将结果 geobias 到用户的相关区域，请始终向请求添加尽可能多的位置详细信息。

## <a name="using-fuzzy-search-api"></a>使用模糊搜索 API

Azure Maps [模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 支持标准单行和自由格式搜索。 如果不知道搜索请求的用户输入类型，建议使用 Azure Maps 搜索模糊 API。  查询输入可以是完整或部分地址。 它还可以是 (POI) 令牌的兴趣点，如 POI、POI 类别或品牌名称。 而且，若要改善搜索结果的相关性，可以通过坐标位置和半径或通过定义边界框来约束查询结果。

>[!TIP]
>大多数搜索查询默认为 maxFuzzyLevel = 1，以获得性能并减少异常结果。 您可以使用或参数调整色阶级别 `maxFuzzyLevel` `minFuzzyLevel` 。 有关 `maxFuzzyLevel` 和所有可选参数的完整列表的详细信息，请参阅 [模糊搜索 URI 参数](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>使用模糊搜索搜索地址

在此示例中，我们将使用模糊搜索来搜索整个世界 `pizza` 。  接下来，我们将向您演示如何在特定国家/地区搜索范围。 最后，我们将向您演示如何使用坐标位置和半径来确定特定区域的搜索范围，并限制返回的结果数。

>[!IMPORTANT]
>若要将结果 geobias 为用户的相关领域，请始终添加尽可能多的位置详细信息。 若要了解详细信息，请参阅 [搜索的最佳实践](how-to-use-best-practices-for-search.md#geobiased-search-results)。

1. 打开 Postman 应用，单击 " **新建**"，然后选择 " **请求**"。 在“请求名称”中，输入请求名称。 选择在上一部分中创建的集合，或创建一个新集合，然后选择 " **保存**"。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。 请求应如下面的 URL 所示：

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >URL 路径中的 _json_ 属性确定响应格式。 本文使用 json 以方便使用和提高可读性。 若要查找其他受支持的响应格式，请参阅 `format` [URI 参数参考文档](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy#uri-parameters)中的参数定义。

3. 单击“发送”并查看响应正文。****

    "比萨饼" 的明确查询字符串返回了10个 [兴趣点](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) ， ("比萨饼" 和 "餐馆" 类别中的 POI) 。 每个结果都包含 "街道地址"、"纬度" 和 "经度" 值、"查看端口" 和位置入口点等详细信息。 对于此查询，结果现在会变化，并且不会绑定到任何引用位置。
  
    在下一步中，我们将使用 `countrySet` 参数来仅指定你的应用程序需要覆盖的国家/地区。 有关支持的国家/地区的完整列表，请参阅 [搜索范围](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)。

4. 默认行为是搜索整个世界，可能会返回不必要的结果。 接下来，我们将仅搜索美国的比萨饼。 将 `countrySet` 键添加到 **Params** 节，并将其值设置为 `US` 。 将 `countrySet` 密钥设置为 `US` 会将结果绑定到美国。

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="在美国中搜索比萨饼":::

    结果现在受限为国家/地区代码，查询返回了美国境内的比萨餐馆。

5. 若要获取更具针对性的搜索，可以在/lon. 的范围内进行搜索。 坐标对。 在此示例中，我们将使用/lon。 的。 由于我们只想在400米的半径内返回结果，因此我们将添加 `radius` 参数。 此外，我们将添加参数，将 `limit` 结果限制为最近的五个比萨饼位置。

    在 " **Params** " 部分中，添加以下键/值对：

     | 键 | Value |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | radius | 400 |
    | limit | 5|

6. 单击“Send”。 响应包括位于西雅图太空针附近的比萨饼餐厅的结果。

## <a name="search-for-a-street-address-using-reverse-address-search"></a>使用反向地址搜索搜索街道地址

Azure Maps [获取搜索地址反向 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 将坐标转换为可读街道地址。 此 API 通常用于使用 GPS 源的应用程序，并且想要发现特定坐标点上的地址。

>[!IMPORTANT]
>若要将结果 geobias 为用户的相关领域，请始终添加尽可能多的位置详细信息。 若要了解详细信息，请参阅 [搜索的最佳实践](how-to-use-best-practices-for-search.md#geobiased-search-results)。

>[!TIP]
>如果你有一组要反转地理编码的坐标位置，则可以使用 [Post Search 地址反向批处理 API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) 通过单个 API 调用发送一批查询。

在此示例中，我们将使用几个可用的可选参数进行反向搜索。 有关可选参数的完整列表，请参阅 [反向搜索参数](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters)。

1. 在 Postman 应用中，单击 " **新建**"，然后选择 " **请求**"。 在“请求名称”中，输入请求名称。 选择在第一个部分中创建的集合，或创建一个新集合，然后选择 " **保存**"。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. 单击 " **发送**"，并查看响应正文。 应该会看到一个查询结果。 该响应包括有关 Safeco Field 的关键地址信息。
  
4. 现在，我们将以下键/值对添加到 **Params** 节：

    | 键 | Value | 返回
    |-----|------------|------|
    | 数字 | 1 |响应可能包含街道两侧 (左/右) 以及数字的偏移位置。|
    | returnSpeedLimit | 是 | 返回地址的速度限制。|
    | returnRoadUse | 是 | 返回地址使用的公路类型。 有关所有可能的公路使用类型，请参阅 [公路使用类型](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters)。|
    | returnMatchType | 是| 返回匹配的类型。 对于所有可能的值，请参阅 [反向地址搜索结果](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="反向搜索。":::

5. 单击 " **发送**"，并查看响应正文。

6. 接下来，我们将添加 `entityType` 密钥，并将其值设置为 `Municipality` 。 `entityType`该键将覆盖 `returnMatchType` 上一步中的密钥。 我们还需要删除 `returnSpeedLimit` 和， `returnRoadUse` 因为我们请求有关市政府的信息。  有关所有可能的实体类型，请参阅 [实体类型](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#entitytype)。

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="搜索反向 entityType。":::

7. 单击“Send”。 将结果与步骤5中返回的结果进行比较。  由于请求的实体类型现在为 `municipality` ，因此响应不包含街道地址信息。 此外，还 `geometryId` 可以使用返回的 Azure Maps 获取 [搜索多边形 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)来请求边界多边形。

>[!TIP]
>若要获取有关这些参数的详细信息以及如何了解其他参数，请参阅 " [反向搜索参数" 部分](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters)。

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>使用反向地址跨街道搜索搜索交叉街道

在此示例中，我们将根据地址的坐标搜索交叉街道。

1. 在 Postman 应用中，单击 " **新建**"，然后选择 " **请求**"。 在“请求名称”中，输入请求名称。 选择在第一个部分中创建的集合，或创建一个新集合，然后选择 " **保存**"。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。 请求应如下面的 URL 所示：
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="搜索交叉街道。":::
  
3. 单击 " **发送**"，并查看响应正文。 你会注意到，响应包含的 `crossStreet` 值为 `Occidental Avenue South` 。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Maps 搜索服务 REST API](https://docs.microsoft.com/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps 搜索服务最佳实践](how-to-use-best-practices-for-search.md)
