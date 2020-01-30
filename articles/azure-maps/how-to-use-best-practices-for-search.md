---
title: 使用 Azure Maps 搜索服务有效地搜索 |Microsoft Azure 映射
description: 了解如何使用 Microsoft Azure 映射应用搜索服务的最佳实践
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845752"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>使用 Azure Maps 搜索服务的最佳实践

Azure Maps[搜索服务](https://docs.microsoft.com/rest/api/maps/search)包括具有各种功能的 api。 例如，地址搜索 API 用于搜索兴趣点（POI）或围绕特定位置的数据。 本文演示从 Azure Maps 搜索服务调用数据时要应用的声音方案。 你将了解如何执行以下操作：

* 生成查询以返回相关的匹配项
* 限制搜索结果
* 了解各种结果类型之间的差异
* 阅读地址搜索响应结构


## <a name="prerequisites"></a>必备组件

若要对映射服务 Api 进行任何调用，需要 Azure Maps 帐户和密钥。 如果需要，请按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)和[获取](quick-demo-map-app.md#get-the-primary-key-for-your-account)主密钥中的说明进行操作。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

> [!Tip]
> 若要查询搜索服务，可以使用[Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用，也可以使用所需的任何 API 开发环境。


## <a name="best-practices-for-geocoding-address-search"></a>地理编码的最佳做法（地址搜索）

使用 Azure Maps 搜索服务搜索完整或部分地址时，API 将从搜索查询中读取关键字，并返回地址的经度和纬度坐标。 此过程称为地理编码。 在国家/地区中进行地理编码的能力取决于道路数据覆盖范围和地理编码服务的地理编码精度。

若要详细了解按国家/地区 Azure Maps 地理编码功能，请参阅[地理编码覆盖率](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)。

### <a name="limit-search-results"></a>限制搜索结果

 Azure Maps 搜索 API 可以帮助你适当地限制搜索结果，以便你可以向用户显示相关数据。

   > [!Note]
   > 下面列出了不是所有搜索 Api 支持的参数

   **地理偏向搜索结果**

   为了将结果地理偏向到用户的相关区域，你应该始终添加最大可能的详细位置输入。 若要限制搜索结果，请考虑添加以下输入类型：

   1. 设置 `countrySet` 参数，例如 "US，FR"。 默认的搜索行为是搜索整个世界，可能会返回不必要的结果。 如果查询确实有 `countrySet` 参数，搜索可能会返回不准确的结果。 例如，搜索名为 " **Bellevue** " 的城市将返回来自美国和法国的结果，因为在法国和美国的 broth 中有一个名为**Bellevue**的城市。

   2. 您可以使用 `btmRight` 和 `topleft` 参数设置边界框，以将搜索限制到地图上的特定区域。

   3. 若要影响结果的相关性区域，可以使用 `radius` 参数定义 `lat`和 `lon` 坐标参数并设置搜索区域的半径。


   **模糊搜索参数**
   
  如果你不知道用户输入用于搜索查询，则建议使用 Azure Maps[模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 该 API 将兴趣点（POI）搜索与地理编码组合到一个规范的*单行搜索*中。

   1. 即使查询参数与所需信息不完全匹配，`minFuzzyLevel` 和 `maxFuzzyLevel` 帮助也返回相关的匹配项。 若要提高性能和减少异常结果，请 `minFuzzyLevel=1` 默认搜索查询，并 `maxFuzzyLevel=2`。 采用搜索词 "restrant" 的示例，当 `maxFuzzyLevel` 设置为2时，它将与 "餐馆" 匹配。 可以根据需要重写默认的模糊级别。  

   2. 你还可以使用 `idxSet` 参数确定要返回的确切结果类型集的优先级。 为此，可以提交以逗号分隔的索引列表;项顺序并不重要。 支持以下索引：

       * `Addr` - **地址范围**：对于某些街道，有一些地址点，其中包含街道的开头和结尾部分。 这些点表示为地址范围。
       * `Geo` - **地区**：地图上表示土地的管理部门的区域，即国家/地区、州和城市。
       * `PAD` - **点地址**：位于地图上的点，其中包含街道名称和号码的特定地址位于索引中，例如 Soquel Dr 2501。 此 idxSet 值是地址的最高准确性。  
       * `POI`**的  - 点**：需要注意的地图上的点，并且可能会很感兴趣。  [Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不会返回 poi。  
       *  - **街道**`Str`：地图上的街道的表示形式。
       * `XStr` - **跨街道/交集**：联接的表示形式;两个街道相交的位置。


       **用法示例**：

       * idxSet = POI （仅搜索兴趣点） 

       * idxSet = PAD，Addr （仅限搜索地址，PAD = 点地址，地址 = 地址范围）

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>反向地理编码和地理实体类型筛选器

使用[搜索地址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)执行反向地理编码搜索时，服务可以返回管理区域的多边形。 通过在请求中提供参数 `entityType`，可以缩小指定地理实体类型的搜索范围。 生成的响应将包含地理 ID 和匹配的实体类型。 如果提供了多个实体，则终结点将返回**可用的最小实体**。 返回的 Geometry ID 可用于通过[获取多边形服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)获取该地理位置的几何。

**示例请求：**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**响应：**

```JSON
{
    "summary": {
        "queryTime": 14,
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

`language` 参数允许你选择 API 返回的结果语言。 如果未在请求中设置语言，则搜索服务将自动默认为该国家/地区的最常见语言。 此外，当指定语言中的数据不可用时，将使用默认语言。 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)，以获得按国家/地区 Azure Maps 服务中支持的语言列表。


### <a name="predictive-mode-autosuggest"></a>预测模式（自动建议）

若要查找部分查询的更多匹配项，`typeahead` 参数应设置为 "true"。 查询将被解释为部分输入，搜索将进入 "预测" 模式。 否则，服务将假定已传入所有相关信息。

在下面的示例查询中，你可以看到，搜索地址服务在查询 "Microso" 时，将 `typeahead` 参数设置为**true**。 如果观察到响应，可以看到搜索服务将查询解释为部分查询。 响应包含 autosuggested 查询的结果。

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
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>用于处理特殊字符的 URI 编码 

若要查找跨街道地址，必须对 URI 进行编码，以便处理地址中的特殊字符。 请考虑此地址示例： "第一次 & 联合街道，西雅图"。 在发送请求之前，需要对特殊字符 "&" 进行编码。 建议在 URI 中编码字符数据，其中，所有字符均使用 "%" 字符和两个字符的十六进制值（对应于其 UTF-8 字符）进行编码。

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
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
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

### <a name="nearby-search"></a>附近搜索

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
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometry

当响应类型为**Geometry**时，它可以包括在 "geometry" 和 "ID" 下的数据**源**对象中返回的 geometry ID。 例如，"[获取多边形服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)" 允许以 GeoJSON 格式请求几何数据。 例如，一组实体的 "城市" 或 "机场" 大纲。 可以将此边界数据用于[地理围栏](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或在[几何图形内搜索 poi](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)。


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
