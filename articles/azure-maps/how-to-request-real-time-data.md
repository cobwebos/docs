---
title: 如何请求 Azure Maps 中的实时数据 |Microsoft Docs
description: 请求使用的 Azure Maps 移动服务的实时数据。
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: bb99a3c063f69aa5aeb00efdb51319a53d05b2d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067607"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>使用 Azure Maps 移动服务的请求实时数据

本文介绍如何使用 Azure Maps[移动服务](https://aka.ms/AzureMapsMobilityService)请求实时传输数据。

在本文中，您将学习如何：


 * 请求到达给定停止的所有行到达下一步的实时请求
 * 请求给定的自行车扩展坞的实时信息。


## <a name="prerequisites"></a>必备组件

若要使 Azure Maps 公共传输 Api 调用，需要一个 Maps 帐户和密钥。 有关创建帐户和检索密钥的信息，请参阅[如何管理 Azure Maps 帐户和密钥](how-to-manage-account-keys.md)。

本文使用 [Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 可以使用你喜欢的任何 API 开发环境。


## <a name="request-real-time-arrivals-for-a-stop"></a>请求到达停下来购买的实时请求

若要请求的特定公共传输停止实时到达的请求数据，您将需要对发出请求[实时到达的请求 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)的 Azure Maps[移动服务](https://aka.ms/AzureMapsMobilityService)。 你将需要**metroID**并**stopID**来完成该请求。 若要了解有关如何请求这些参数的详细信息，请参阅我们操作方法指南[请求公共传输路由](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。 

让我们使用作为我们 metro"522"ID，即在停止 ID 为"华盛顿州贝尔维尤西雅图 – 塔科马 –"区域中，并使用停止 ID"2060603"，即总线 metro"Ne 24 & 162nd Ave Ne，华盛顿州贝尔维尤"。 若要请求在此停止所有下一步实时到达的接下来五个实时到达的请求数据，请完成以下步骤：

1. 创建要在其中存储请求的集合。 在 Postman 应用中，选择**新建**。 在中**创建新**窗口中，选择**集合**。 命名集合，并选择**创建**按钮。

2. 若要创建该请求，请选择**新建**试。 在中**创建新**窗口中，选择**请求**。 输入**请求名称**请求中，选择要保存该请求，然后选择在其中的位置上一步中创建的集合**保存**。

    ![在 Postman 中创建请求](./media/how-to-request-transit-data/postman-new.png)

3. 选择生成器选项卡上的 GET HTTP 方法并输入以下 URL 来创建 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. 后成功的请求，用户会收到以下响应。  请注意 scheduleType 该参数定义的估计的到达时间基于实时或静态数据。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }


## Real-time data for bike docking station

The [Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) of the Azure Maps Mobility Service, allows to request static and real-time information such as availability and vacancy information for a given bike or scooter docking station. We will make a request to get real-time data for a docking station for bikes.

In order to make a request to the Get Transit Dock Info API, you will need the **dockId** for that station. You can get the dock ID by making a search request to the [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) and setting the **objectType** parameter to "bikeDock". Follow the steps below to get real-time data of a docking station for bikes.


### Get dock ID

To get **dockID**, follow the steps below to make a request to the Get Nearby Transit API:

1. In Postman, click **New Request** | **GET request** and name it **Get dock ID**.

2.  On the Builder tab, select the **GET** HTTP method, enter the following request URL, and click **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 后成功的请求，用户会收到以下响应。 请注意，现在，我们**id**在响应中，这可用作更高版本中对获取传输停靠信息 API 的请求的查询参数。

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
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


### <a name="get-real-time-bike-dock-status"></a>获取实时自行车停靠状态

请按照以下步骤来向 API 发出请求获取传输停靠信息以获取所选的停靠的实时数据。

1. 在 Postman 中，单击**新的请求** | **GET 请求**并将其命名**获取实时停靠数据**。

2.  在生成器选项卡上选择**获取**的 HTTP 方法，输入以下请求 URL，然后单击**发送**。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 在成功的请求后将收到以下结构的响应：

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>后续步骤

了解如何请求使用的移动服务的传输数据：

> [!div class="nextstepaction"]
> [如何请求传输数据](how-to-request-transit-data.md)

探索 Azure Maps 移动服务 API 文档：

> [!div class="nextstepaction"]
> [移动服务 API 文档](https://aka.ms/AzureMapsMobilityService)
