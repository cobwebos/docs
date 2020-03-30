---
title: 请求实时传输数据 |微软 Azure 地图
description: 使用 Microsoft Azure 地图移动服务请求实时数据。
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335484"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>使用 Azure 地图移动服务请求实时数据

本文介绍如何使用 Azure 地图[移动服务](https://aka.ms/AzureMapsMobilityService)请求实时传输数据。

在本文中，你将学习如何：


 * 请求下一个实时到达到达给定停靠点的所有线路
 * 请求给定自行车坞站的实时信息。


## <a name="prerequisites"></a>先决条件

首先需要具有 Azure 地图帐户和订阅密钥，才能对 Azure 映射公共交通 API 进行任何调用。 有关详细信息，请按照[创建帐户中的](quick-demo-map-app.md#create-an-account-with-azure-maps)说明创建 Azure 地图帐户。 按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)的步骤获取帐户的主密钥。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。


本文使用 [Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 可以使用你喜欢的任何 API 开发环境。


## <a name="request-real-time-arrivals-for-a-stop"></a>要求实时到达，以便停车

为了请求特定公共交通站的实时到达数据，您需要向 Azure 地图[移动服务](https://aka.ms/AzureMapsMobilityService)[的实时到达 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)发出请求。 您需要**metroID**和**stopID**才能完成请求。 要了解有关如何请求这些参数的更多信息，请参阅我们的指南，了解如何[请求公共交通路线](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。 

让我们使用"522"作为我们的地铁 ID，这是"华盛顿州西雅图-塔科马-贝尔维尤"地区的地铁 ID。 使用"522---2060603"作为停车ID，此巴士站位于"Ne 24街&162大道内，贝尔维尤WA"。 要请求接下来的五个实时到达数据，对于在此停靠点的所有下一个实时到达者，请完成以下步骤：

1. 打开 Postman 应用，让我们创建一个集合来存储请求。 在邮递员应用的顶部附近，选择 **"新建**"。 在 **"创建新"** 窗口中，选择 **"集合**"。  命名集合并选择"**创建**"按钮。

2. 要创建请求，请再次选择 **"新建**"。 在 **"创建新"** 窗口中，选择 **"请求**"。 输入请求**的请求名称**。 选择您在上一步中创建的集合，作为保存请求的位置。 然后，选择 **"保存**"。

    ![在邮递员中创建请求](./media/how-to-request-transit-data/postman-new.png)

3. 选择生成器选项卡上的**GET** HTTP 方法，然后输入以下 URL 以创建 GET 请求。 使用`{subscription-key}`Azure 映射主键替换 。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 成功请求后，您将收到以下响应。  请注意，参数"计划类型"定义估计到达时间是基于实时数据还是静态数据。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>自行车坞站的实时数据

[获取传输停靠信息 API](https://aka.ms/AzureMapsMobilityTransitDock)允许用户请求静态和实时信息。 例如，用户可以请求自行车或滑板车坞站的可用性和空置信息。 [获取传输停靠站信息 API](https://aka.ms/AzureMapsMobilityTransitDock)也是 Azure 地图[移动服务的](https://aka.ms/AzureMapsMobilityService)一部分。

为了向[获取传输停靠站信息 API](https://aka.ms/AzureMapsMobilityTransitDock)发出请求，您需要该站点的**dockId。** 您可以通过向["获取附近的传输 API"](https://aka.ms/AzureMapsMobilityNearbyTransit)发出搜索请求，将**对象类型**参数分配给"bikeDock"来获取扩展坞 ID。 按照以下步骤获取自行车坞的实时数据。


### <a name="get-dock-id"></a>获取停靠 ID

要获取**dockID，** 请按照以下步骤向"获取附近的传输 API"发出请求：

1. 在邮递员中，单击 **"新请求** | **GET"请求**并将其命名为 **"获取停靠 ID**"。

2.  在"生成器"选项卡上，选择**GET** HTTP 方法，输入以下请求 URL，然后单击"**发送**"。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 成功请求后，您将收到以下响应。 请注意，我们现在在响应中具有**ID，** 稍后可以在获取传输停靠信息 API 的请求中用作查询参数。

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>获取实时自行车停靠站状态

按照以下步骤向获取传输停靠站信息 API 发出请求，以获得所选扩展坞的实时数据。

1. 在邮递员中，单击 **"新请求** | **GET 请求**"并命名它**获取实时停靠数据**。

2.  在"生成器"选项卡上，选择**GET** HTTP 方法，输入以下请求 URL，然后单击"**发送**"。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 请求成功后，您将收到以下结构的响应：

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>后续步骤

了解如何使用移动服务请求传输数据：

> [!div class="nextstepaction"]
> [如何请求传输数据](how-to-request-transit-data.md)

浏览 Azure 地图移动服务 API 文档：

> [!div class="nextstepaction"]
> [移动服务 API 文档](https://aka.ms/AzureMapsMobilityService)
