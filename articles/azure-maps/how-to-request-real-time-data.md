---
title: 请求实时传输数据 |Microsoft Azure 映射
description: 使用 Microsoft Azure 映射移动服务来请求实时数据。
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 169764f015f332d07c21ef815e6044c653489774
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911435"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>使用 Azure Maps 移动服务请求实时数据

本文介绍如何使用 Azure Maps[移动服务](https://aka.ms/AzureMapsMobilityService)来请求实时传输数据。

本文介绍如何执行以下操作：


 * 请求到达给定停止处的所有行的下一个实时到达
 * 请求给定自行车扩展坞的实时信息。


## <a name="prerequisites"></a>必备组件

若要对 Azure Maps 公共传输 Api 进行任何调用，需要一个 Maps 帐户和密钥。 有关创建帐户和获取密钥的信息，请按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明创建 Azure Maps 帐户订阅，并按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤获取帐户的主密钥。 有关 Azure Maps 中的身份验证的详细信息，请参阅[Azure Maps 中的管理身份验证](./how-to-manage-authentication.md)。


本文使用 [Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 可以使用你喜欢的任何 API 开发环境。


## <a name="request-real-time-arrivals-for-a-stop"></a>请求停止的实时到达

若要为特定的公共传输停止请求实时到达数据，需要向 Azure Maps[移动服务](https://aka.ms/AzureMapsMobilityService)的[实时到达 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)发出请求。 你将需要**metroID**和**stopID**来完成请求。 若要详细了解如何请求这些参数，请参阅[请求公用传输路由](https://aka.ms/AMapsHowToGuidePublicTransitRouting)的 "操作方法" 指南。 

让我们使用 "522" 作为地铁 ID，即 "西雅图– Tacoma – Bellevue，WA" 区域的地铁 ID，并使用 stop ID "522---2060603"，该 id 是 "Ne 24 日 St & 162nd，Bellevue WA" 的总线停止。 若要在此时间段内请求接下来的五个实时到达请求数据，请完成以下步骤：

1. 创建用于存储请求的集合。 在 Postman 应用中，选择 "**新建**"。 在 "**新建**" 窗口中，选择 "**集合**"。 将该集合命名为，然后选择 "**创建**" 按钮。

2. 若要创建请求，请再次选择 "**新建**"。 在 "**新建**" 窗口中，选择 "**请求**"。 输入请求的**请求名称**，选择你在上一步中创建的集合作为要保存请求的位置，然后选择 "**保存**"。

    ![在 Postman 中创建请求](./media/how-to-request-transit-data/postman-new.png)

3. 在 "生成器" 选项卡上选择 "获取 HTTP" 方法，然后输入以下 URL 创建 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 成功请求后，你将收到以下响应。  请注意，参数 "scheduleType" 定义了预计的到达时间是基于实时数据还是静态数据。

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


## <a name="real-time-data-for-bike-docking-station"></a>自行车扩展坞的实时数据

Azure Maps 移动服务的[获取传输坞站信息 API](https://aka.ms/AzureMapsMobilityTransitDock)允许请求静态和实时信息，如给定自行车或 scooter 扩展坞的可用性和空缺信息。 我们将发出请求，以获取用于自行车的扩展坞的实时数据。

为了向获取传输坞站信息 API 发出请求，你将需要该工作站的**dockId** 。 可以通过对 "[获取附近的传输 API](https://aka.ms/AzureMapsMobilityNearbyTransit) " 发出搜索请求并将**objectType**参数设置为 "bikeDock" 来获取 dock ID。 按照以下步骤获取用于自行车的扩展坞的实时数据。


### <a name="get-dock-id"></a>获取 dock ID

若要获取**dockID**，请按照以下步骤发出对 "获取附近传输" API 的请求：

1. 在 Postman 中，单击 "**新建请求**" | **获取请求**并将其命名为**获取 dock ID**。

2.  在 "生成器" 选项卡上，选择 "**获取**HTTP" 方法，输入以下请求 URL，然后单击 "**发送**"。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 成功请求后，你将收到以下响应。 请注意，我们现在在响应中有了**id** ，以后可将其作为请求中的查询参数用于获取传输停靠信息 API。

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


### <a name="get-real-time-bike-dock-status"></a>获取实时自行车固定状态

按照以下步骤发出对获取中转坞信息 API 的请求，以获取所选停靠的实时数据。

1. 在 Postman 中，单击 "**新建请求** | **获取请求**并将其命名为**获取实时停靠数据**"。

2.  在 "生成器" 选项卡上，选择 "**获取**HTTP" 方法，输入以下请求 URL，然后单击 "**发送**"。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 成功请求后，你将收到以下结构的响应：

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

浏览 Azure Maps 移动服务 API 文档：

> [!div class="nextstepaction"]
> [移动服务 API 文档](https://aka.ms/AzureMapsMobilityService)
