---
title: 使用 Azure Maps 搜索服务搜索位置 |Microsoft Azure 映射
description: 在本文中，你将了解如何使用地理编码和反向地理编码的 Microsoft Azure 映射搜索服务搜索位置。
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 40066f24fec00610a1efd10b2cb874b1100acdee
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209879"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>使用 Azure Maps 搜索服务搜索位置

Azure Maps[搜索服务](https://docs.microsoft.com/rest/api/maps/search)是一组 RESTful api，旨在帮助开发人员按名称或类别搜索地址、位置、业务列表和其他地理信息。 除了支持传统的地理编码，服务还可以根据纬度和经度来反转地理编码地址和交叉街道。 搜索返回的纬度和经度值可用作其他 Azure Maps 服务（如[路由](https://docs.microsoft.com/rest/api/maps/route)和[天气](https://docs.microsoft.com/rest/api/maps/weather)服务）中的参数。

在本文中，你将学习如何：

* 使用[搜索地址 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)请求地址的纬度和经度坐标（地理编码地址位置）
* 使用[模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)搜索地址或兴趣点（POI）
* 搜索地址以及属性和坐标
* 进行[反向地址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，将坐标位置转换为街道地址
* 使用[搜索地址反向交叉街道 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)搜索交叉街道

## <a name="prerequisites"></a>必备条件

若要完成本文中的步骤，你需要先创建一个 Azure Maps 帐户，并向你显示 Maps 帐户订阅密钥。 按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明创建 Azure Maps 帐户订阅，并按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤获取帐户的主密钥。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

本文使用 [Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 可以使用你喜欢的任何 API 开发环境。

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>请求地址的纬度和经度（地理编码）

在此示例中，我们使用 Azure Maps[获取搜索地址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)将街道地址转换为纬度和经度坐标。 可以将完整或部分街道地址传递到 API，并接收包含详细地址属性（如街道、邮政编码和国家/地区）以及纬度和经度中的位置值的响应。

如果你有一组要地理编码的地址，则可以使用[Post Search 地址批处理 API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch)通过单个 API 调用发送一批查询。

1. 在 Postman 中，单击“新建请求” **“GET 请求”，并将请求命名为“地址搜索”。**  | 

2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL，并选择授权协议（如果有）。

![地址搜索](./media/how-to-search-for-address/address_search_url.png)

| 参数 | 建议的值 |
|---------------|------------------------------------------------| 
| HTTP 方法 | GET |
| 请求 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| 授权 | 无身份验证 |

3. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数： 

![地址搜索](./media/how-to-search-for-address/address_search_params.png) 

| 密钥 | 值 | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<你的 Azure Maps 密钥\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. 单击“发送”并查看响应正文。 

本例中指定了完整地址查询，并在响应正文中收到了一条结果。 

5. 在“参数”中，将查询字符串编辑为以下值： 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. 将以下键/值对添加至 Params 部分，并单击“发送”： 

| 密钥 | 值 | 
|-----|------------| 
| typeahead | true | 

**typeahead** 标志告知地址搜索 API 要将查询视为部分输入，并返回预测值的数组。

## <a name="using-fuzzy-search-api"></a>使用模糊搜索 API

如果你不知道用户输入用于搜索查询，则建议使用 Azure Maps[模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 该 API 将兴趣点（POI）搜索和地理编码组合到规范的 "单行搜索" 中。 例如，该 API 可以处理任何地址或 POI 令牌组合的输入。 还可以使用完全受坐标和半径约束的上下文位置 （纬度/经度对）为该 API 加权，或者在不使用任何地理偏置定位点的情况下，以更泛的方式执行该 API。

大多数搜索查询默认指定 `maxFuzzyLevel=1`，以提高性能并减少不正常的结果。 可以根据需要，通过传入查询参数 `maxFuzzyLevel=2` 或 `3`，按请求重写此默认值。

### <a name="search-for-an-address-using-fuzzy-search"></a>使用模糊搜索搜索地址

1. 打开 Postman 应用，单击“新建”>“新建”，选择“GET 请求”。 输入“模糊搜索”作为请求名称，选择用于保存该请求的集合或文件夹，单击“保存”。

2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL。

    ![模糊搜索](./media/how-to-search-for-address/fuzzy_search_url.png)

    | 参数 | 建议的值 |
    |---------------|------------------------------------------------|
    | HTTP 方法 | GET |
    | 请求 URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | 授权 | 无身份验证 |

    URL 路径中的 **json** 属性确定响应格式。 本文使用 json 以方便使用和提高可读性。 可以在 **Maps 功能 API 参考**的 [Get 模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)定义中找到可用的响应格式。

3. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数：

    ![模糊搜索](./media/how-to-search-for-address/fuzzy_search_params.png)

    | 密钥 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<你的 Azure Maps 密钥\> |
    | query | pizza |

4. 单击“发送”并查看响应正文。

    "比萨饼" 的不明确查询字符串在 "比萨饼" 和 "餐馆" 类别中返回10个[兴趣点](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse)（POI）。 每个结果都返回街道地址、纬度和经度值、查看端口和位置入口点。
  
    此查询的结果会有变化，并不与任何特定的参照位置紧密相关。 你可以使用**countrySet**参数来仅指定你的应用程序需要覆盖的国家/地区。 默认行为是搜索整个世界，可能会返回不必要的结果。

5. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |------------------|-------------------------|
    | countrySet | 美国 |
  
    结果现在受限为国家/地区代码，查询返回了美国境内的比萨餐馆。
  
    要为某个位置提供结果，可以查询兴趣点，并在模糊搜索服务的调用中使用返回的纬度和经度值。 本例使用搜索服务返回了西雅图太空针塔 (Seattle Space Needle) 的位置，并使用了纬度 /经度 值来定位搜索。
  
6. 在“参数”中，输入以下键值对并单击“发送”：

    ![模糊搜索](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | 密钥 | 值 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>使用反向地址搜索搜索街道地址

Azure Maps[获取搜索地址反向 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)可帮助将坐标（例如：37.786505、-122.3862）转换为易于理解的街道地址。 大多数情况下，在跟踪应用程序时需要此操作，在这些应用程序中，你可以从设备或资产接收 GPS 源，并想知道坐标所在的地址。
如果你有一组要反转地理编码的坐标位置，则可以使用[Post Search 地址反向批处理 API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch)通过单个 API 调用发送一批查询。


1. 在 Postman 中，单击“新建请求” **“GET 请求”，并将请求命名为“反向地址搜索”。**  | 

2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL。
  
    ![反向地址搜索 URL](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 参数 | 建议的值 |
    |---------------|------------------------------------------------|
    | HTTP 方法 | GET |
    | 请求 URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | 授权 | 无身份验证 |
  
3. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数：
  
    ![反向地址搜索参数](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | 密钥 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<你的 Azure Maps 密钥\> |
    | query | 47.591180,-122.332700 |
  
4. 单击“发送”并查看响应正文。

    该响应包括有关 Safeco Field 的关键地址信息。
  
5. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | 数字 | true |

    如果[number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) query 参数与请求一起发送，则响应可能包含街道的一侧（左侧或右侧）以及该数字的偏移位置。
  
6. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | returnSpeedLimit | true |
  
    设置[returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查询参数后，响应将返回已发布的速度限制。

7. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | returnRoadUse | true |

    如果设置了 [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 查询参数，响应将返回街道级别的反向地理编码的道路用途数组。

8. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | roadUse | true |

    可以使用[roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查询参数将反向地理编码查询限制为特定类型的道路。
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>使用反向地址跨街道搜索搜索交叉街道

1. 在 Postman 中，单击“新建请求” **“GET 请求”，并将请求命名为“反向地址十字路口搜索”。**  | 

2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL。
  
    ![反向地址十字路口搜索](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 参数 | 建议的值 |
    |---------------|------------------------------------------------|
    | HTTP 方法 | GET |
    | 请求 URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | 授权 | 无身份验证 |
  
3. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数：
  
    | 密钥 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<你的 Azure Maps 密钥\> |
    | query | 47.591180,-122.332700 |
  
4. 单击“发送”并查看响应正文。

## <a name="next-steps"></a>后续步骤

- 浏览 [Azure Maps 搜索服务](https://docs.microsoft.com/rest/api/maps/search) API 文档。
- 了解[最佳实践](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)。