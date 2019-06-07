---
title: 如何请求 Azure Maps 中的传输数据 |Microsoft Docs
description: 请求使用的 Azure Maps 移动服务的公共传输数据。
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e8250763153f7c5b71f3906a560365dadfd55694
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735566"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>请求使用的 Azure Maps 移动服务的公共传输数据 

本文介绍如何使用 Azure Maps[移动服务](https://aka.ms/AzureMapsMobilityService)请求公共传输数据，包括将停止，将路由信息，以及旅行时间估计。

在本文中，您将学习如何：

* 获取 metro 区域 ID 使用[获取 Metro 区域 API](https://aka.ms/AzureMapsMobilityMetro)
* 请求附近传输会停止使用[获取附近传输](https://aka.ms/AzureMapsMobilityNearbyTransit)服务。
* 查询[获取传输路由 API](https://aka.ms/AzureMapsMobilityTransitRoute)计划通过使用公共传输路由。
* 请求传输路由几何图形和路由使用的详细的计划[获取传输路线 API](https://aka.ms/ https://azure.microsoft.com/services/azure-maps/)。


## <a name="prerequisites"></a>必备组件

若要使 Azure Maps 公共传输 Api 调用，需要一个 Maps 帐户和密钥。 有关创建帐户和检索密钥的信息，请参阅[如何管理 Azure Maps 帐户和密钥](how-to-manage-account-keys.md)。

本文使用 [Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 可以使用你喜欢的任何 API 开发环境。


## <a name="get-a-metro-area-id"></a>获取 metro 区域 ID

若要请求将需要特定的大城市区域传输信息`metroId`你想要请求的传输数据的区域。 [获取 Metro 区域 API](https://aka.ms/AzureMapsMobilityMetro)可以请求 Azure Maps 移动服务可大都市区。 响应包括详细信息如下所述`metroId`，`metroName`和 metro 区域几何图形以 GeoJSON 格式的表示形式。

让我们来发出请求，以获取 Metro 区域中的西雅图塔科马 metro 区域 id。 若要请求 ID 为 metro 区域时，完成以下步骤：

1. 创建要在其中存储请求的集合。 在 Postman 应用中，选择**新建**。 在中**创建新**窗口中，选择**集合**。 命名集合，并选择**创建**按钮。

2. 若要创建该请求，请选择**新建**试。 在中**创建新**窗口中，选择**请求**。 输入**请求名称**请求中，选择要保存该请求，然后选择在其中的位置上一步中创建的集合**保存**。
    
    ![在 Postman 中创建请求](./media/how-to-request-transit-data/postman-new.png)

3. 选择生成器选项卡上的 GET HTTP 方法并输入以下 URL 来创建 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. 后成功的请求，用户会收到以下响应：

    ```JSON
    {
        "results": [
            {
                "metroId": "522",
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            [
                                -121.95725,
                                47.18314
                            ],
                            ...,
                            ...,
                            ...,
                            ...,
                            [
                                -122.18711,
                                47.15571
                            ],
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                }
            }
        ]
    }
    ```

5. 复制`metroId`，供以后使用。

## <a name="request-nearby-transit-stops"></a>附近的传输停止请求

Azure Maps[获取附近传输](https://aka.ms/AzureMapsMobilityNearbyTransit)服务，可搜索的传输对象，例如，公共传输停止并共享自行车围绕给定的位置返回对象的详细信息的传输。 接下来我们将向发出请求，服务搜索附近的公用传输停止 300 米半径范围内围绕给定位置。 我们需要在请求中，包括`metroId`之前中检索到。

要向发送请求[获取附近传输](https://aka.ms/AzureMapsMobilityNearbyTransit)，按照以下步骤：

1. 在 Postman 中，单击**新的请求** | **GET 请求**并将其命名**获取附近停止**。

2. 在生成器选项卡上选择**获取**的 HTTP 方法，输入 API 终结点的以下请求 URL，然后单击**发送**。

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 以后成功的请求，响应结构看起来应类似于下面：

    ```JSON
    {
        "results": [
            {
                "id": "2060603",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "Ne 24th St & 162nd Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.63241381296315,
                        "longitude": -122.12659096560266
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594172088166,
                        "longitude": -122.12395908007201
                    }
                }
            },
            {
                "id": "2061020",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318791818726,
                        "longitude": -122.12845199584025
                    },
                    "btmRightPoint": {
                        "latitude": 47.63049919323126,
                        "longitude": -122.12582004983427
                    }
                }
            },
            {
                "id": "2060604",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474784183636,
                        "longitude": -122.12912401149087
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655200865796,
                        "longitude": -122.12649203418405
                    }
                }
            }
        ]
    }    
    ```

如果仔细观察响应结构，则可以看到，它包含参数的每个传输对象，例如`id`， `type`， `stopName`， `mainTransitType`，`mainAgencyName`和对象的位置 （区坐标中）。

对于理解，我们将使用`id`之一的下一节中路由为原点公交车站。  


## <a name="request-a-transit-route"></a>请求的传输路由

Azure Maps[获取传输路由 API](https://aka.ms/AzureMapsMobilityTransitRoute)允许差旅计划源和目标之间返回最可能的路由选项。 服务提供多种行驶模式，包括步行、 骑车旅行，和公共传输。 接下来我们将在西雅图中搜索到太空针塔从最接近的公交车站的路由。

### <a name="get-location-coordinates-for-destination"></a>获取目标位置坐标

为了获取位置坐标太空针塔，允许您使用 Azure Maps[模糊搜索服务](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)。

若要向模糊搜索服务发出请求，请执行以下步骤：

1. 在 Postman 中，单击**新的请求** | **GET 请求**并将其命名**获取位置坐标**。

2.  在生成器选项卡上选择**获取**的 HTTP 方法，输入以下请求 URL，然后单击**发送**。
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. 如果仔细查看响应，它包含在结果中的太空针塔的多个位置，并且还包含每个下的位置坐标信息**位置**。 复制`lat`和`lon`从第一个结果的位置。
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 61,
            "numResults": 8,
            "offset": 0,
            "totalResults": 24,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/8309323",
                "score": 4.674,
                "info": "search:ta:840539000511573-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "monument"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "monument"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States Of America",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
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
            ...,
            ...,
            ...
            
        ]
    }
    ``` 
    

### <a name="request-route"></a>请求路由

若要使路由请求，请完成以下步骤：

1. 在 Postman 中，单击**新的请求** | **GET 请求**并将其命名**获取路线信息**。

2. 在生成器选项卡上选择**获取**的 HTTP 方法，输入 API 终结点的以下请求 URL，然后单击**发送**。

    我们将通过指定请求的总线公共传输路由`modeType`和`transitType`参数。 请求 URL 包含在前面几节中检索到的位置。 作为`originType`现在，我们**stopId**和 as`destionationType`我们有**位置**。

    请参阅[URI 参数的列表](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters)可以为在请求中使用[获取传输路由 API](https://aka.ms/AzureMapsMobilityTransitRoute)。 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 在成功的请求，响应结构应类似于下面所示：

    ```JSON
    {
        "results": [
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:0---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:15:53-07:00",
                "travelTimeInSeconds": 3259,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T14:59:47-07:00",
                        "caption": "545",
                        "lengthInMeters": 16441
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:59:48-07:00",
                        "legEndTime": "2019-04-03T15:03:53-07:00",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:03:53-07:00",
                        "legEndTime": "2019-04-03T15:07:26-07:00",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:07:26-07:00",
                        "legEndTime": "2019-04-03T15:12:12-07:00",
                        "caption": "8",
                        "lengthInMeters": 1057
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:13-07:00",
                        "legEndTime": "2019-04-03T15:15:53-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 268
                    }
                ]
            },
            ...,
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:2---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:19:18-07:00",
                "travelTimeInSeconds": 3464,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T15:01:00-07:00",
                        "caption": "545",
                        "lengthInMeters": 17400
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:01:01-07:00",
                        "legEndTime": "2019-04-03T15:04:59-07:00",
                        "caption": "3rd Avenue",
                        "lengthInMeters": 269
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:04:59-07:00",
                        "legEndTime": "2019-04-03T15:09:14-07:00",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:09:14-07:00",
                        "legEndTime": "2019-04-03T15:12:52-07:00",
                        "caption": "33,24",
                        "lengthInMeters": 947
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:53-07:00",
                        "legEndTime": "2019-04-03T15:19:18-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 474
                    }
                ]
            }
        ]
    }
    ```

4. 如果您仔细观察，有多个**总线**在响应中的路由。 每个路由都有唯一**路线 ID**描述的路由的每个阶段的概述。 接下来我们将请求的最快路由使用的详细信息`itineraryId`在响应中。

## <a name="request-fastest-route-itinerary"></a>请求最快路由路线

Azure Maps[获取传输路线](https://aka.ms/AzureMapsMobilityTransitItinerary)服务，可请求使用的路由的特定路由数据**路线 ID**返回[获取传输路由 API](https://aka.ms/AzureMapsMobilityTransitRoute)服务。 若要发出请求，完成以下步骤：

1. 在 Postman 中，单击**新的请求** | **GET 请求**并将其命名**获取传输信息**。

2. 在生成器选项卡上选择**获取**的 HTTP 方法，输入 API 终结点的以下请求 URL，然后单击**发送**。

    我们将把`detailType`参数**geometry** ，以便响应包含中断的信息的公共传输和打开通过打开导航引导和 bike legs 的路由。

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. 在成功的请求，响应结构应类似于下面所示：

    ```JSON
    {
    "departureTime": "2019-05-01T11:16:56-07:00",
    "arrivalTime": "2019-05-01T12:23:45-07:00",
    "legs": [
                {
                    "legType": "Walk",
                    "legStartTime": "2019-05-01T11:16:56-07:00",
                    "legEndTime": "2019-05-01T11:24:06-07:00",
                    "walkingSteps": [
                        {
                            "direction": {
                                "relativeDirection": "left"
                            },
                            "streetName": "Northeast 24th Street"
                        },
                        {
                            "direction": {
                                "relativeDirection": "right"
                            },
                            "streetName": "156th Avenue Northeast"
                        }
                    ],
                    "walkingOrigin": {
                        "latitude": 47.63096,
                        "longitude": -122.126
                    },
                    "walkingDestination": {
                        "latitude": 47.631843,
                        "longitude": -122.132294
                    },
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.126,
                                47.63096
                            ],
                            [
                                -122.12645,
                                47.63099
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.63184
                            ]
                        ]
                    }
                },
                {
                    "legType": "Wait",
                    "legStartTime": "2019-05-01T11:24:06-07:00",
                    "legEndTime": "2019-05-01T11:25:07-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "waitOnVehicle": "false"
                },
                {
                    "legType": "Bus",
                    "legStartTime": "2019-05-01T11:25:07-07:00",
                    "legEndTime": "2019-05-01T11:30:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        ...,
                        ...,
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.13235,
                                47.63184
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.64398
                            ]
                        ]
                    }
                },
                ...,
                ...,
                ...,
                {
                    "legType": "Tram",
                    "legStartTime": "2019-05-01T12:20:00-07:00",
                    "legEndTime": "2019-05-01T12:22:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 4083239,
                        "agencyId": 1360766,
                        "agencyName": "Seattle Monorail",
                        "lineNumber": "Monorail",
                        "caption1": "Seattle Center - Westlake Center",
                        "caption2": "MONORAIL Seattle Center - Westlake Center",
                        "color": "00AEEF",
                        "transitType": "Tram"
                    },
                    "line": {
                        "lineId": 3769726,
                        "lineGroupId": 4083239,
                        "direction": "backward",
                        "agencyId": 1360766,
                        "lineNumber": "Monorail",
                        "destination": "Seattle Center"
                    },
                    "stops": [
                        {
                            "stopId": 32962125,
                            "stopName": "Westlake Station",
                            "position": {
                                "latitude": 47.611417,
                                "longitude": -122.337089
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        },
                        {
                            "stopId": 32962134,
                            "stopName": "Seattle Center",
                            "position": {
                                "latitude": 47.62123,
                                "longitude": -122.349746
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.3369,
                                47.61201
                            ],
                            ...,
                            ...,
                            [
                                -122.34973,
                                47.6212
                            ]
                        ]
                    }
                },
                {
                    "legType": "PathWayWalk",
                    "legStartTime": "2019-05-01T12:22:00-07:00",
                    "legEndTime": "2019-05-01T12:23:45-07:00"
                }
          ]
    }
    ```

## <a name="next-steps"></a>后续步骤

了解如何请求使用的移动服务的实时数据：

> [!div class="nextstepaction"]
> [如何对请求的实时数据](how-to-request-real-time-data.md)

浏览 Azure Maps 移动服务 API 文档

> [!div class="nextstepaction"]
> [移动服务 API 文档](https://aka.ms/AzureMapsMobilityService)

