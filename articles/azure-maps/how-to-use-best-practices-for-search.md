---
title: Azure 地图搜索服务的最佳做法 |微软 Azure 地图
description: 了解如何在使用 Microsoft Azure 地图中的搜索服务时应用最佳做法。
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335306"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure 地图搜索服务的最佳做法

Azure 地图[搜索服务](https://docs.microsoft.com/rest/api/maps/search)包括提供各种功能的 API。 例如，搜索地址 API 可以查找特定位置周围的感兴趣点 （POI） 或数据。 

本文介绍如何在从 Azure 地图搜索服务调用数据时应用声音实践。 将了解如何执行以下操作：

* 生成查询以返回相关匹配项。
* 限制搜索结果。
* 了解结果类型之间的差异。
* 读取地址搜索-响应结构。

## <a name="prerequisites"></a>先决条件

要调用 Azure 地图服务 API，需要 Azure 地图帐户和密钥。 有关详细信息，请参阅[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)并[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 

有关 Azure 映射中的身份验证的信息，请参阅[在 Azure 映射中管理身份验证](./how-to-manage-authentication.md)。

> [!TIP]
> 要查询搜索服务，可以使用[Postman 应用](https://www.getpostman.com/apps)生成 REST 调用。 或者，您可以使用任何您喜欢的 API 开发环境。

## <a name="best-practices-to-geocode-addresses"></a>地理编码地址的最佳做法

使用 Azure 地图搜索服务搜索完整或部分地址时，API 将从搜索查询中读取关键字。 然后返回地址的经度和纬度坐标。 此过程称为*地理编码*。 

国家/地区地理编码的能力取决于道路数据的可用性和地理编码服务的准确性。 有关按国家或地区对 Azure 地图进行地理编码功能的详细信息，请参阅[地理编码覆盖范围](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)。

### <a name="limit-search-results"></a>限制搜索结果

 Azure 地图搜索 API 可以帮助您适当地限制搜索结果。 限制结果，以便向用户显示相关数据。

> [!NOTE]
> 搜索 API 支持更多的参数，而不仅仅是本文讨论的参数。

#### <a name="geobiased-search-results"></a>地缘搜索结果

要将地理偏置结果添加到用户的相关区域，请始终添加尽可能多的位置详细信息。 您可能希望通过指定某些输入类型来限制搜索结果：

* 设置`countrySet`参数。 例如，您可以将其设置为`US,FR`。。 默认情况下，API 会搜索整个世界，以便返回不必要的结果。 如果查询没有`countrySet`参数，则搜索可能会返回不准确的结果。 例如，搜索名为*贝尔维尤*的城市返回来自美国和法国的结果，因为这两个国家都包含一个名为*贝尔维尤*的城市。

* 可以使用 和`btmRight``topleft`参数设置边界框。 这些参数将搜索限制为地图上的特定区域。

* 要影响与结果相关的区域，请定义`lat`和`lon`坐标参数。 使用`radius`参数设置搜索区域的半径。


#### <a name="fuzzy-search-parameters"></a>模糊搜索参数

当不知道搜索查询的用户输入时，我们建议您使用 Azure 地图[搜索模糊 API。](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API 将 POI 搜索和地理编码合并为规范*的单行搜索*： 

* `minFuzzyLevel`和`maxFuzzyLevel`参数帮助返回相关匹配项，即使查询参数与用户需要的信息不完全匹配也是如此。 为了最大限度地提高性能和减少异常结果，请将搜索查询设置为 和`minFuzzyLevel=1``maxFuzzyLevel=2`的默认值。 

    例如，当`maxFuzzyLevel`参数设置为 2 时，搜索术语*restrant*与*餐厅*匹配。 您可以在需要时覆盖默认的模糊级别。 

* 使用`idxSet`参数确定结果类型的确切集的优先级。 要确定一组确切结果的优先级，可以提交逗号分隔的索引列表。 在列表中，物料订单并不重要。 Azure 映射支持以下索引：

* `Addr` - **地址范围**：从街道的开头和结尾插值的地址点。 这些点表示为地址范围。
* `Geo` - **地域**：土地的行政区划。 例如，地理位置可以是国家/地区、州或城市。
* `PAD` - **点地址**：包含街道名称和编号的地址。 点地址可以在索引中找到。 索*克尔博士2501就是一*个例子。 点地址为地址提供最高级别的精度。  
* `POI` - **兴趣点**：地图上被认为值得注意或可能有趣的点。 [搜索地址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不会返回 POIs。  
* `Str` - **街道**：地图上的街道。
* `XStr` - **交叉街道或交叉点**：两条街道相交的交汇点或地点。


#### <a name="usage-examples"></a>用法示例

* `idxSet=POI`- 仅搜索 POIs。 

* `idxSet=PAD,Addr`- 仅搜索地址。 `PAD`指示点地址，并`Addr`指示地址范围。

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>地理实体类型的反向地理编码和筛选器

在[搜索地址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)中执行反向地理编码搜索时，服务可以返回管理区域的多边形。要将搜索范围缩小到特定的地理实体类型，请在`entityType`请求中包括参数。 

生成的响应包含地理 ID 和匹配的实体类型。 如果提供了多个实体，则终结点将返回*可用的最小实体*。 您可以使用返回的几何 ID 通过[搜索多边形服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)获取地理的几何体。

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

使用`language`参数设置返回的搜索结果的语言。 如果请求未设置该语言，则默认情况下，搜索服务使用国家或地区中最常见的语言。 当指定语言没有可用的数据时，将使用默认语言。 

有关详细信息，请参阅[Azure 映射支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)。


### <a name="use-predictive-mode-automatic-suggestions"></a>使用预测模式（自动建议）

要查找部分查询的更多匹配项，将`typeahead`参数设置为`true`。 此查询被解释为部分输入，并且搜索进入预测模式。 如果未将`typeahead`参数设置为`true`，则服务假定已传入所有相关信息。

在以下示例查询中，搜索地址服务将查询*Microso*。 此处，参数`typeahead`设置为`true`。 响应显示搜索服务将查询解释为部分查询。 响应包含自动建议的查询的结果。

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


### <a name="encode-a-uri-to-handle-special-characters"></a>编码 URI 以处理特殊字符 

要查找跨街地址，必须对 URI 进行编码以处理地址中的特殊字符。 以这个地址为例：*西雅图联合街第一大道&联合街*。 在这里，在发送请求之前对安培`&`字符 （ ） 进行编码。 

我们建议您在 URI 中对字符数据进行编码。 在 URI 中，使用与字符的 UTF-8 代码对应的百分比符号 （`%`） 和两个字符十六进制值对所有字符进行编码。

#### <a name="usage-examples"></a>用法示例

从此地址开始：

```
query=1st Avenue & E 111th St, New York
```

对地址进行编码：

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

可以使用以下方法。

JavaScript 或类型脚本：
```Javascript
encodeURIComponent(query)
```

C# 或视觉基本：
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

红宝石：
```Ruby
CGI::escape(query) 
```

Swift：
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

去：
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>POI 搜索的最佳实践

在 POI 搜索中，您可以按名称请求 POI 结果。 例如，您可以按名称搜索企业。 

我们强烈建议您使用该`countrySet`参数指定应用程序需要覆盖的国家/地区。 默认行为是搜索整个世界。 此广泛搜索可能会返回不必要的结果，并且搜索可能需要很长时间。

### <a name="brand-search"></a>品牌搜索

为了提高结果和响应中信息的相关性，POI 搜索响应包括品牌信息。 您可以使用此信息进一步分析响应。

在请求中，您可以提交逗号分隔的品牌名称列表。 使用列表通过设置`brandSet`参数将结果限制为特定品牌。 在列表中，物料订单并不重要。 当您提供多个品牌列表时，返回的结果必须至少属于您的一个列表。

为了探索品牌搜索，让我们提出[一个POI类别搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)请求。 在下面的示例中，我们在华盛顿雷德蒙德的 Microsoft 园区附近寻找加油站。 响应显示返回的每个 POI 的品牌信息。

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

通过使用搜索 POI API，您可以使用其官方代码查找机场。 例如，您可以使用*SEA*查找西雅图-塔科马国际机场： 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>附近搜索

若要在特定位置周围检索 POI 结果，可以尝试使用["搜索附近"API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)。 终结点仅返回 POI 结果。 它不采用搜索查询参数。 

要限制结果，我们建议您设置半径。

## <a name="understanding-the-responses"></a>了解响应

让我们通过向 Azure 地图搜索服务发出地址搜索请求来查找西雅图的地址。 在以下请求 URL 中，我们将`countrySet`参数`US`设置为搜索美国的地址。

### <a name="sample-query"></a>示例查询

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>支持的结果类型

* **点地址**：地图上具有街道名称和编号的特定地址的点。 点地址为地址提供最高级别的精度。 

* **地址范围**：从街道的开头和结尾插值的地址点的范围。  

* **地理位置**：地图上表示土地行政区划的区域，例如国家、州或城市。 

* **POI**：地图上值得注意的点，可能很有趣。

* **街道**：地图上的街道。 地址解析为包含地址的街道的纬度和经度坐标。 房屋号码可能无法处理。 

* **十字街**：交叉路口。 交叉街道表示两条街道相交的交汇点。

### <a name="response"></a>响应

让我们来看看响应结构。 在随后的响应中，结果对象的类型不同。 如果仔细查看，您将看到三种类型的结果对象：

* 点地址
* 街道
* 十字街

请注意，地址搜索不会返回 POIs。  

每个`Score`响应对象的参数指示匹配分数如何与同一响应中其他对象的分数相关。 有关响应对象参数的详细信息，请参阅[获取搜索地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)。

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

*几何体的*响应类型可以包括在 和`dataSources``geometry``id`下的对象中返回的几何 ID。 例如，可以使用[搜索多边形服务](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)以 GeoJSON 格式请求几何数据。 通过使用此格式，您可以获取一组实体的城市或机场大纲。 然后，可以使用此边界数据在几何体内[设置地理围栏](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或[搜索 POIs。](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)


[搜索地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)API 或[搜索模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)API 的响应可以包括在 和 下`dataSources``geometry`的对象中返回的几何`id`ID 和 ：


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>后续步骤

要了解更多信息，请参阅：

> [!div class="nextstepaction"]
> [如何生成 Azure 地图搜索服务请求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [搜索服务 API 文档](https://docs.microsoft.com/rest/api/maps/search)