---
title: 如何使用 Azure Maps 搜索服务有效搜索 |Microsoft Docs
description: 了解如何使用 Azure Maps 搜索服务搜索的最佳实践
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88f864abc82ea6ba70559c8db5db2d0fe07383b1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768820"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>使用 Azure Maps 搜索服务的最佳实践

Azure Maps[搜索服务](https://docs.microsoft.com/rest/api/maps/search)包括具有各种功能的 api，例如，从地址搜索，到围绕特定位置搜索兴趣点（POI）数据。 本文介绍如何通过 Azure Maps 搜索服务来调用数据的最佳做法。 你将了解如何执行以下操作：

* 生成查询以返回相关的匹配项
* 限制搜索结果
* 了解各种结果类型之间的差异
* 阅读地址搜索响应结构


## <a name="prerequisites"></a>必备组件

若要调用任何 Maps 服务 API，需要具有 Maps 帐户和密钥。 有关创建帐户的信息，请按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明进行操作，并按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤来检索帐户的主密钥（订阅）。 有关 Azure Maps 中的身份验证的详细信息，请参阅[Azure Maps 中的管理身份验证](./how-to-manage-authentication.md)。

> [!Tip]
> 若要查询搜索服务，可以使用[Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用，也可以使用所需的任何 API 开发环境。


## <a name="best-practices-for-geocoding-address-search"></a>地理编码的最佳做法（地址搜索）

使用 Azure Maps 搜索服务搜索完整或部分地址时，将使用搜索词并返回地址的经度和纬度坐标。 此过程称为地理编码。 在国家/地区中进行地理编码的能力取决于道路数据覆盖范围和地理编码服务的地理编码精度。

若要详细了解按国家/地区 Azure Maps 地理编码功能，请参阅[地理编码覆盖率](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)。

### <a name="limit-search-results"></a>限制搜索结果

   在本部分中，你将了解如何使用 Azure Maps 搜索 Api 来限制搜索结果。 

   > [!Note]
   > 并非所有搜索 Api 完全支持下面列出的参数

   **地理偏向搜索结果**

   为了将结果地理偏向到用户的相关区域，你应该始终添加最大可能的详细位置输入。 若要限制搜索结果，请考虑添加以下输入类型：

   1. 设置 `countrySet` 参数，例如 "US，FR"。 默认的搜索行为是搜索整个世界，可能会返回不必要的结果。 如果查询不包含 `countrySet` 参数，搜索可能会返回不准确的结果。 例如，搜索名为 " **Bellevue** " 的城市将返回来自美国和法国的结果，因为在法国和美国有名为**Bellevue**的城市。

   2. 您可以使用 `btmRight` 和 `topleft` 参数设置边界框，以将搜索限制到地图上的特定区域。

   3. 若要影响结果的相关性区域，可以使用 `radius` 参数定义 `lat`和 `lon` 坐标参数并设置搜索区域的半径。


   **模糊搜索参数**
   
   如果你不知道用户输入用于搜索查询，则建议使用 Azure Maps[模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 该 API 将兴趣点（POI）搜索与地理编码组合到一个规范的*单行搜索*中。 

   1. 即使查询参数与所需信息不完全对应，`minFuzzyLevel` 和 `maxFuzzyLevel`帮助也返回相关的匹配项。 大多数搜索查询都默认为 `minFuzzyLevel=1` 和 `maxFuzzyLevel=2`，以获得性能并减少异常结果。 采用搜索词 "restrant" 的示例，当 `maxFuzzyLevel` 设置为2时，它将与 "餐馆" 匹配。 根据请求的需要，可以重写默认的模糊级别。 

   2. 你还可以使用 `idxSet` 参数确定要返回的确切结果类型集的优先级。 为此，可以提交以逗号分隔的索引列表;项顺序并不重要。 支持以下索引：

       * `Addr` - **地址范围**：对于某些街道，存在从街道的开头和结尾处插值的地址点;这些点表示为地址范围。
       * `Geo` - **地区**：地图上表示土地的管理部门的区域，即国家/地区、州和城市。
       * `PAD` - **点地址**：位于地图上的点，其中包含街道名称和号码的特定地址位于索引中，例如 Soquel Dr 2501。 它是可用于地址的最高级别的准确性。  
       * `POI`**的  - 点**：需要注意的地图上的点，并且可能会很感兴趣。  [Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不会返回 poi。  
       *  - **街道**`Str`：地图上的街道的表示形式。
       * `XStr` - **跨街道/交集**：联接的表示形式;两个街道相交的位置。


       **用法示例**：

       * idxSet = POI （仅搜索兴趣点） 

       * idxSet = PAD，Addr （仅限搜索地址，PAD = 点地址，地址 = 地址范围）

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>反向地理编码和地理实体类型筛选器

使用[搜索地址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)执行反向地理编码搜索时，服务可以返回管理区域的多边形。 通过在请求中提供参数 `entityType`，可以缩小指定地理实体类型的搜索范围。 生成的响应将包含 geography ID 以及匹配的实体类型。 如果提供了多个实体，则终结点将返回**可用的最小实体**。 返回的 Geometry ID 可用于通过[获取多边形服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)获取该地理位置的几何。

**示例请求：**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**响应：**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>搜索结果语言

`language` 参数允许您设置应在其中返回语言搜索结果的。 如果未在请求中设置语言，则搜索服务将自动默认为国家/地区的最常见语言。 此外，当指定语言中的数据不可用时，将使用默认语言。 有关按国家/地区 Azure Maps 服务的支持语言的列表，请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)。


### <a name="predictive-mode-auto-suggest"></a>预测模式（自动建议）

若要查找部分查询的更多匹配项，`typeahead` 参数应设置为 "true"。 查询将被解释为部分输入，搜索将进入 "预测" 模式。 否则，服务将假定已传入所有相关信息。

在下面的示例查询中，你可以看到，搜索地址服务在查询 "Microso" 时，将 `typeahead` 参数设置为**true**。 如果观察到响应，可以看到搜索服务将查询解释为部分查询，响应包含自动建议查询的结果。

**示例查询：**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**响应：**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>用于处理特殊字符的 URI 编码 

若要查找跨街道地址，即 "第一号 & 联合街道，西雅图"，需要在发送请求之前对特殊字符 "&" 进行编码。 建议在 URI 中编码字符数据，其中，所有字符均使用 "%" 字符和两个字符的十六进制值（对应于其 UTF-8 字符）进行编码。

**用法示例**：

获取搜索地址：

```
query=1st Avenue & E 111th St, New York
```

 应编码为：

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


下面是用于不同语言的不同方法： 

JavaScript/TypeScript：
```Javascript
encodeURIComponent(query)
```

C#VB
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++：
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP：
```PHP
urlencode(query)
```

拼音
```Ruby
CGI::escape(query) 
```

Swift：
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

至
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>POI 搜索的最佳实践

兴趣点（POI）搜索允许按名称请求 POI 结果，例如，按名称搜索业务。 我们强烈建议使用 `countrySet` 参数指定应用程序需要覆盖的国家/地区，因为默认行为是搜索整个世界，可能会返回不必要的结果和/或导致更长的搜索时间。

### <a name="brand-search"></a>品牌搜索

若要改善结果与响应中的信息的相关性，兴趣点（POI）搜索响应包括可进一步用于分析响应的品牌信息。

你还可以提交请求中以逗号分隔的品牌名称列表。 您可以使用此列表，通过使用 `brandSet` 参数将结果限制为特定品牌。 项顺序并不重要。 如果提供了多个品牌，则仅返回属于（至少）一个提供的列表的结果。

接下来，让我们在 Microsoft 校园（Redmond，WA）附近对加油站进行[POI 类别搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)请求。 如果观察到响应，可以查看每个返回的 POI 的品牌信息。


**示例查询：**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**响应：**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>机场搜索

POI 搜索支持使用官方机场代码搜索机场。 例如，**海平面**（西雅图-Tacoma 国际机场）。 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>邻近搜索

若要仅检索特定位置周围的 POI 结果，[附近的搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)可能是正确的选择。 此终结点将只返回 POI 结果，而不会在搜索查询参数中使用。 若要限制结果，建议设置半径。

## <a name="understanding-the-responses"></a>了解响应

让我们对西雅图中某个地址的 Azure Maps[搜索服务](https://docs.microsoft.com/rest/api/maps/search)发出地址搜索请求。 如果你仔细查看下面的请求 URL，我们已将 `countrySet` 参数设置为**US** ，以便在美国美国中搜索该地址。

**示例查询：**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

接下来，让我们看一下下面的响应结构。 响应中的结果对象的结果类型不同。 如果你仔细观察，你会看到我们具有三种不同类型的结果对象，即 "点地址"、"街道" 和 "十字街道"。 请注意，地址搜索不返回 Poi。 每个响应对象的 `Score` 参数指示相同响应中其他对象的分数的相对匹配分数。 请参阅[获取搜索地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)了解有关 response object 参数的详细信息。

**支持的结果类型：**

* **点地址：** 使用带有街道名称和号码的特定地址的地图上的点。 地址的最高准确性。 

* **地址范围：** 对于某些街道，存在从街道的开头和结尾处插值的地址点;这些点表示为地址范围。 

* **地域：** 地图上表示土地的管理部门的区域，即国家/地区、省/市/自治区。 

* **POI-（兴趣点）：** 需要注意的地图上的点，并且可能会很感兴趣。

* **街道：** 地图上的街道的表示形式。 地址解析为包含地址的街道的纬度/经度坐标。 门牌号码可能不会处理。 

* **交叉街道：** 交. 联接的表示形式;两个街道相交的位置。

**响应：**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometry

当响应类型为**Geometry**时，它可以包括在 "geometry" 和 "ID" 下的数据**源**对象中返回的 geometry ID。 例如，"[获取多边形服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)" 允许您以 GeoJSON 格式请求几何数据，例如一组实体的 "城市" 或 "机场" 大纲。 可以将此边界数据用于[地理围栏](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或在[几何图形内搜索 poi](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)。


[搜索地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)或[搜索模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)API 响应可以包括在 "geometry" 和 "ID" 下的数据源对象中返回的**geometry ID** 。


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>后续步骤

* 了解[如何构建 Azure Maps 搜索服务请求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)。
* 浏览 Azure Maps[搜索服务 API 文档](https://docs.microsoft.com/rest/api/maps/search)。 
