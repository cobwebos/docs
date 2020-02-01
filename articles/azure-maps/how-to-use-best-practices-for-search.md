---
title: 使用 Azure Maps 搜索服务有效地搜索 |Microsoft Azure 映射
description: 了解如何使用 Microsoft Azure 映射为搜索服务应用最佳实践。
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e29b3d70c576955637424208aeb0f980669b67bb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899166"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure Maps 搜索服务的最佳实践

Azure Maps[搜索服务](https://docs.microsoft.com/rest/api/maps/search)包括提供各种功能的 api。 例如，搜索地址 API 可以查找感兴趣点（POI）或围绕特定位置的数据。 

本文介绍如何在调用 Azure Maps 搜索服务中的数据时应用声音方案。 将了解如何执行以下操作：

* 生成查询以返回相关的匹配项。
* 限制搜索结果。
* 了解结果类型之间的差异。
* 阅读地址搜索-响应结构。

## <a name="prerequisites"></a>必备组件

若要调用 Azure Maps 服务 Api，需要 Azure Maps 帐户和密钥。 有关详细信息，请参阅[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)和[获取主键](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 

有关 Azure Maps 中的身份验证的信息，请参阅[Azure Maps 中的管理身份验证](./how-to-manage-authentication.md)。

> [!TIP]
> 若要查询搜索服务，可以使用[Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 或者，你可以使用你喜欢的任何 API 开发环境。

## <a name="best-practices-to-geocode-addresses"></a>地理编码地址的最佳实践

使用 Azure Maps 搜索服务搜索完整或部分地址时，API 将从搜索查询中读取关键字。 然后，它返回地址的经度和纬度坐标。 此过程称为*地理编码*。 

地理编码在某个国家/地区的能力取决于公路数据的可用性以及地理编码服务的精度。 有关按国家或地区 Azure Maps 地理编码功能的详细信息，请参阅[地理编码范围](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)。

### <a name="limit-search-results"></a>限制搜索结果

 Azure Maps 搜索 API 可以帮助你适当地限制搜索结果。 你可以限制结果，以便向用户显示相关数据。

> [!NOTE]
> 与本文讨论的参数相比，搜索 Api 支持更多的参数。

#### <a name="geobiased-search-results"></a>Geobiased 搜索结果

若要将结果 geobias 到用户的相关区域，请始终添加尽可能多的位置详细信息。 您可能想要通过指定某些输入类型来限制搜索结果：

* 设置 `countrySet` 参数。 例如，可以将其设置为 `US,FR`。 默认情况下，API 会搜索整个世界，因此它可能会返回不必要的结果。 如果查询没有 `countrySet` 参数，搜索可能会返回不准确的结果。 例如，名为*Bellevue*的城市的搜索将返回来自美国和法国的结果，因为这两个国家都包含名为*Bellevue*的城市。

* 您可以使用 `btmRight` 和 `topleft` 参数来设置边界框。 这些参数将搜索限制到地图上的特定区域。

* 若要影响结果的相关性区域，请定义 `lat` 和 `lon` 坐标参数。 使用 `radius` 参数设置搜索区域的半径。


#### <a name="fuzzy-search-parameters"></a>模糊搜索参数

如果不知道搜索查询的用户输入，我们建议使用 Azure Maps[搜索模糊 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 该 API 将 POI 搜索和地理编码合并为规范的*单行搜索*： 

* 即使查询参数与用户需要的信息不完全匹配，`minFuzzyLevel` 和 `maxFuzzyLevel` 参数也有助于返回相关的匹配项。 若要最大程度提高性能和减少异常结果，请将搜索查询设置为 `minFuzzyLevel=1` 和 `maxFuzzyLevel=2`的默认值。 

    例如，将 `maxFuzzyLevel` 参数设置为2时，搜索词*restrant*将与*餐馆*匹配。 您可以在需要时覆盖默认的模糊级别。 

* 使用 `idxSet` 参数确定确切的一组结果类型。 若要确定确切的结果集的优先级，可以提交以逗号分隔的索引列表。 在列表中，项顺序并不重要。 Azure Maps 支持以下索引：

    * `Addr` - **地址范围**：从街道开头和末尾插值的地址点。 这些点表示为地址范围。
    * `Geo` - **地区**：土地的管理部门。 例如，地理位置可以是国家/地区、省/市/自治区或市县。
    * `PAD` - **点地址**：包含街道名称和号码的地址。 可在索引中找到点地址。 例如， *Soquel Dr 2501*。 点地址提供地址的最高级别的准确性。  
    * `POI` - **的兴趣点**：位于地图上的点，这些点被视为值得注意或可能会令人感兴趣。 [搜索地址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不返回 poi。  
    * `Str` - **街道**：地图上的街道。
    * `XStr` - **交叉街道或交点**：与两个街道相交的联接或位置。


#### <a name="usage-examples"></a>用法示例

* 仅 `idxSet=POI` 搜索 Poi。 

* 仅限 `idxSet=PAD,Addr` 搜索地址。 `PAD` 指示点地址，`Addr` 指示地址范围。

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Geography 实体类型的地理编码和筛选

当你在[搜索地址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)中执行反向地理编码搜索时，服务可以返回管理区域的多边形。 若要将搜索范围缩小到特定地理实体类型，请在请求中包含 `entityType` 参数。 

生成的响应包含地域 ID 和匹配的实体类型。 如果提供了多个实体，则终结点将返回*可用的最小实体*。 您可以使用返回的几何 ID 通过[搜索多边形服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)获取地理位置的几何。

#### <a name="sample-request"></a>示例请求

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>响应

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

### <a name="set-the-results-language"></a>设置结果语言

使用 `language` 参数为返回的搜索结果设置语言。 如果请求未设置语言，则默认情况下搜索服务使用国家或地区的最常见语言。 当指定语言中没有可用的数据时，将使用默认语言。 

有关详细信息，请参阅[Azure Maps 支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)。


### <a name="use-predictive-mode-automatic-suggestions"></a>使用预测模式（自动建议）

若要查找部分查询的更多匹配项，请将 `typeahead` 参数设置为 `true`。 此查询被解释为部分输入，搜索进入预测模式。 如果未将 `typeahead` 参数设置为 `true`，则该服务会假定所有相关信息都已传入。

在下面的示例查询中，查询搜索地址服务以获取*Microso*。 此处，`typeahead` 参数设置为 `true`。 响应显示搜索服务将查询解释为部分查询。 响应包含自动建议的查询的结果。

#### <a name="sample-query"></a>示例查询

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>响应

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


### <a name="encode-a-uri-to-handle-special-characters"></a>编码用于处理特殊字符的 URI 

若要查找跨街道地址，必须对 URI 进行编码，以便处理地址中的特殊字符。 请考虑此地址示例：*第一址 & 联合街道，西雅图*。 在此处，发送请求之前，对 "&" 字符（`&`）进行编码。 

建议在 URI 中对字符数据进行编码。 在 URI 中，使用百分号（`%`）和两个字符的十六进制值（与字符的 UTF-8 代码相对应）对所有字符进行编码。

#### <a name="usage-examples"></a>用法示例

从此地址开始：

```
query=1st Avenue & E 111th St, New York
```

对地址进行编码：

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

您可以使用以下方法。

JavaScript 或 TypeScript：
```Javascript
encodeURIComponent(query)
```

C#或 Visual Basic：
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


## <a name="best-practices-for-poi-searching"></a>POI 搜索的最佳实践

在 POI 搜索中，可以按名称请求 POI 结果。 例如，您可以按名称搜索企业。 

强烈建议使用 `countrySet` 参数指定应用程序需要覆盖的国家/地区。 默认行为是搜索整个世界。 此搜索范围可能会返回不必要的结果，搜索可能需要较长时间。

### <a name="brand-search"></a>品牌搜索

若要改善结果与响应中信息的相关性，POI 搜索响应中包含品牌信息。 您可以使用此信息进一步分析响应。

在请求中，可以提交以逗号分隔的品牌名称列表。 使用此列表，通过设置 `brandSet` 参数将结果限制为特定品牌。 在列表中，项顺序并不重要。 如果提供多个品牌列表，则返回的结果必须属于至少一个列表。

若要探索品牌搜索，请进行[POI 类别搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)请求。 在下面的示例中，我们将寻找位于华盛顿州雷蒙德的 Microsoft 校园附近的加油站。 响应显示返回的每个 POI 的品牌信息。


#### <a name="sample-query"></a>示例查询

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>响应

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

通过使用搜索 POI API，你可以使用其官方代码查找机场。 例如，可以使用*海平面*查找西雅图-Tacoma 国际机场： 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>附近搜索

若要检索围绕特定位置的 POI 结果，可以尝试使用 "[搜索附近的 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)"。 终结点仅返回 POI 结果。 它不接受搜索查询参数。 

为了限制结果，我们建议您设置半径。

## <a name="understanding-the-responses"></a>了解响应

让我们通过向 Azure Maps 搜索服务发出地址搜索请求来查找西雅图的地址。 在下面的请求 URL 中，我们将 `countrySet` 参数设置为 `US` 以搜索 USA 中的地址。

### <a name="sample-query"></a>示例查询

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>支持的结果类型

* **点地址**：位于具有街道名称和编号的特定地址的地图上的点。 点地址为地址提供最高的准确性。 

* **地址范围**：从街道的开头和结尾处插值的地址点的范围。  

* **Geography**：地图上表示土地的管理部门的区域，如国家/地区、省/市/自治区。 

* **POI**：在地图上需要注意的点，这些点可能会很感兴趣。

* **街道**：地图上的街道地址。 地址解析为包含地址的街道的纬度和经度坐标。 可能无法处理房子号。 

* **叉号**：交集。 十字叉积表示两个街道相交的联接。

### <a name="response"></a>响应

让我们看一下响应结构。 在下面的响应中，结果对象的类型不同。 如果您仔细看看，您将看到三种类型的结果对象：

* 点地址
* 街道
* 叉号

请注意，地址搜索不会返回 Poi。  

每个响应对象的 `Score` 参数表示匹配分数如何与同一响应中其他对象的分数相关。 有关 response object 参数的详细信息，请参阅[获取搜索地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)。

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

*几何*的响应类型可以包含在 `geometry` 和 `id`下的 `dataSources` 对象中返回的 geometry ID。 例如，您可以使用 "[搜索多边形" 服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)以 GeoJSON 格式请求几何数据。 使用此格式可以获取一组实体的 "城市" 或 "机场" 大纲。 然后，可以使用此边界数据在几何图形内[设置地域隔离区内](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或[搜索 poi](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)。


[搜索地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)Api 或[搜索模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)API 的响应可以包括在 `geometry` 和 `id`下的 `dataSources` 对象中返回的 geometry ID：


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>后续步骤

* 了解[如何生成 Azure Maps 搜索服务请求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)。
* 浏览 Azure Maps[搜索服务 API 文档](https://docs.microsoft.com/rest/api/maps/search)。 
