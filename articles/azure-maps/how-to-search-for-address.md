---
title: 如何使用 Azure Maps 搜索服务搜索地址 | Microsoft Docs
description: 了解如何使用 Azure Maps 搜索服务搜索地址
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1acb95af7b62641c371627d6250067f9c2eac99c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36323632"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>如何使用 Azure Maps 搜索服务查找地址

Maps 搜索服务是一组 RESTful API，可让开发人员搜索地址、地点、兴趣点、商业列表和其他地理信息。 该服务将纬度/经度分配到特定的地址、十字路口、地理特征或兴趣点 (POI)。 在诸如路线和交通流之类的其他 Maps 服务中，可将搜索返回的纬度和经度值用作参数。

## <a name="prerequisites"></a>先决条件

若要调用任何 Maps 服务 API，需要具有 Maps 帐户和密钥。 有关创建帐户和检索密钥的信息，请参阅[如何管理 Azure Maps 帐户和密钥](how-to-manage-account-keys.md)。

本文使用 [Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 可以使用你喜欢的任何 API 开发环境。 


## <a name="using-fuzzy-search"></a>使用模糊搜索

该搜索服务的默认 API 是[模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)，它可以处理包含地址或 POI 令牌任意组合形式的输入。 此搜索 API 采用规范的“单行搜索”，在不知道用户输入哪些信息作为搜索查询时，此 API 非常有用。 模糊搜索 API 是 POI 搜索和地理编码的组合。 还可以使用完全受坐标和半径约束的上下文位置 （纬度/经度对）为该 API 加权，或者，可以在不使用任何地理偏置定位点的情况下，以更泛的方式执行该 API。

大多数搜索查询默认指定“maxFuzzyLevel=1”，以提高性能并减少不正常的结果。 可以根据需要，通过传入查询参数“maxFuzzyLevel=2”或“maxFuzzyLevel=3”，按请求重写此默认值。

### <a name="search-for-an-address-using-fuzzy-search"></a>使用模糊搜索搜索地址

1. 打开 Postman 应用，单击“新建”>“新建”，选择“GET 请求”。 输入“模糊搜索”作为请求名称，选择用于保存该请求的集合或文件夹，单击“保存”。

2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL。

    ![模糊搜索 ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | 参数 | 建议的值 |
    |---------------|------------------------------------------------|
    | HTTP 方法 | GET |
    | 请求 URL | https://atlas.microsoft.com/search/fuzzy/json? |
    | 授权 | 无身份验证 |

    URL 路径中的 **json** 属性确定响应格式。 为方便使用和阅读，整篇文章都会使用 json。 可以在 [Maps 功能 API 参考] (https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 的 **Get 模糊搜索**定义中找到可用的响应格式。

3. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数：

    ![模糊搜索 ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | 密钥 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<你的 Azure Maps 密钥\> |
    | query | pizza |

4. 单击“发送”并查看响应正文。 

    “pizza”模糊查询字符串返回了 10 条兴趣点 (POI) 结果，其类别属于“pizza”和“restaurant”。 每条结果返回了相应位置的街道地址、纬度/经度值、视口和入口点。
    
    此查询的结果会有变化，并不与任何特定的参照位置紧密相关。 可以使用 **countrySet** 参数来指定只搜索应用程序需要覆盖的国家/地区，因为默认行为是搜索全球，这可能会返回不必要的结果。

5. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |------------------|-------------------------|
    | countrySet | 美国 |
    
    结果现在受限为国家/地区代码，查询返回了美国境内的比萨餐馆。
    
    若要提供带有特定位置的方位的结果，可以查询兴趣点，并在模糊搜索服务的调用中使用返回的纬度和经度值。 本例使用搜索服务返回了西雅图太空针塔 (Seattle Space Needle) 的位置，并使用了纬度 /经度 值来定位搜索。
    
4. 在“参数”中，输入以下键值对并单击“发送”：

    ![模糊搜索 ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | 密钥 | 值 |
    |-----|------------|
    | lat | 47.62039 |
    | lon | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>搜索地址属性和坐标 

可将整个或部分街道地址传递给搜索地址 API，并接收包含详细地址属性（例如市政当局或分部，以及以纬度和经度表示的位置值）的响应。

1. 在 Postman 中，单击“新建请求” | “GET 请求”，并将请求命名为“地址搜索”。
2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL，并选择授权协议（如果有）。

    ![地址搜索 ](./media/how-to-search-for-address/address_search_url.png)
    
    | 参数 | 建议的值 |
    |---------------|------------------------------------------------|
    | HTTP 方法 | GET |
    | 请求 URL | https://atlas.microsoft.com/search/address/json? |
    | 授权 | 无身份验证 |

2. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数：
    
    ![地址搜索 ](./media/how-to-search-for-address/address_search_params.png)
    
    | 密钥 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<你的 Azure Maps 密钥\> |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. 单击“发送”并查看响应正文。 
    
    本例中指定了完整地址查询，并在响应正文中收到了一条结果。 
    
4. 在“参数”中，将查询字符串编辑为以下值：
    ```
        400 Broad, Seattle
    ```

5. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | typeahead | 是 |

    **typeahead** 标志告知地址搜索 API 要将查询视为部分输入，并返回预测值的数组。

## <a name="search-for-a-street-address-using-reverse-address-search"></a>使用反向地址搜索搜索街道地址
1. 在 Postman 中，单击“新建请求” | “GET 请求”，并将请求命名为“反向地址搜索”。

2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL。
    
    ![反向地址搜索 URL ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | 参数 | 建议的值 |
    |---------------|------------------------------------------------|
    | HTTP 方法 | GET |
    | 请求 URL | https://atlas.microsoft.com/search/address/reverse/json? |
    | 授权 | 无身份验证 |
    
2. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数：
    
    ![反向地址搜索参数 ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | 密钥 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<你的 Azure Maps 密钥\> |
    | query | 47.59093,-122.33263 |
    
3. 单击“发送”并查看响应正文。 
    
    响应包含 Safeco Field 的 POI 条目，其 POI 类别为“stadium”。 
    
4. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | 数字 | 是 |

    如果连同请求一起发送了 [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 查询参数，则响应可能包含街道的边侧（左/右），以及该编号的偏移位置。
    
5. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | spatialKeys | 是 |

    如果设置了 [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 查询参数，响应将会包含指定位置的专属地域空间键信息。

6. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | returnSpeedLimit | 是 |
    
    如果设置了 [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 查询参数，响应将返回发布的速度限制。

7. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | returnRoadUse | 是 |

    如果设置了 [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 查询参数，响应将返回街道级别的 reversegeocodes 的道路用途数组。

8. 将以下键/值对添加至 Params 部分，并单击“发送”：

    | 密钥 | 值 |
    |-----|------------|
    | roadUse | 是 |

    可以使用 [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) 查询参数，将反向地理编码查询限制为特定类型的道路用途。
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>使用反向地址十字路口搜索搜索十字路口

1. 在 Postman 中，单击“新建请求” | “GET 请求”，并将请求命名为“反向地址十字路口搜索”。

2. 在“生成器”选项卡上，选择“GET”HTTP 方法，输入 API 终结点的请求 URL。
    
    ![反向地址十字路口搜索 ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | 参数 | 建议的值 |
    |---------------|------------------------------------------------|
    | HTTP 方法 | GET |
    | 请求 URL | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | 授权 | 无身份验证 |
    
3. 单击“参数”，输入以下键值对用作请求 URL 中的查询或路径参数：
    
    | 密钥 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<你的 Azure Maps 密钥\> |
    | query | 47.59093,-122.33263 |
    
4. 单击“发送”并查看响应正文。 

## <a name="next-steps"></a>后续步骤
- 浏览 [Azure Maps 搜索服务](https://docs.microsoft.com/rest/api/maps/search) API 文档 
