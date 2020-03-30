---
title: 图像合作伙伴集成
description: 本文介绍影像合作伙伴集成。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131857"
---
# <a name="imagery-partner-integration"></a>图像合作伙伴集成

本文介绍如何使用 Azure FarmBeats 转换器组件将影像数据发送到 FarmBeats。 农业影像数据可以从各种来源生成，如多光谱摄像机、卫星和无人机。 农业影像合作伙伴可以与 FarmBeats 集成，为客户提供为其农场自定义生成的地图。

数据一旦可用，可以通过 FarmBeats 加速器进行可视化，并可能用于农业企业或客户系统集成商的数据融合和机器学习/人工智能 （ML/AI） 模型构建。

FarmBeats 提供以下功能：

- 使用 /扩展类型 API 定义自定义图像类型、源和文件格式。
- 通过 /Scene 和 /SceneFile API 从各种来源引入影像数据。

以下信息侧重于将任何形式的影像引入 FarmBeats 系统。

选择**无人机影像**部分时，将打开一个弹出窗口，显示无人机正射马赛克的高分辨率图像。 您可以访问合作伙伴软件，该软件有助于规划无人机飞行并获得原始数据。 您将继续使用合作伙伴的软件进行路径规划和正交镶嵌图像拼接。

无人机合作伙伴需要使客户能够将其客户帐户与其在 Azure 上的服务器场Beats实例链接。

您必须在无人机合作伙伴软件中使用以下凭据来链接 FarmBeats：

- API 终结点
- 租户 ID
- 客户端 ID
- 客户端机密

## <a name="api-development"></a>API 开发

API 包含斯瓦格技术文档。 有关 API 和相应请求或响应的信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

## <a name="authentication"></a>身份验证

服务器场节拍使用 Microsoft Azure[活动目录](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)（Azure AD）。 Azure 应用服务提供内置身份验证和授权支持。 

有关 Azure AD 的详细信息，请参阅 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。   

FarmBeats Datahub 使用无记名身份验证，这需要以下凭据：

- 客户端 ID
- 客户端机密
- 租户 ID

使用以前的凭据，调用方可以请求访问令牌，该令牌需要在后续 API 请求中（标头部分）中发送，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

以下 Python 代码示例检索访问令牌。 然后，您可以将令牌用于后续对 FarmBeats 的 API 调用。

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

## <a name="http-request-headers"></a>HTTP 请求标头

以下是对 FarmBeats Datahub 进行 API 调用时需要指定的最常见请求标头。

**头** | **说明和示例**
--- | ---
Content-Type  | 请求格式（内容类型：应用程序/<format>）。 对于服务器场节拍 Datahub API，格式为 JSON。 Content-Type: application/json
授权 | 指定进行 API 调用所需的访问令牌。 授权：承载<访问令牌>
Accept  | 响应格式。 对于服务器场节拍 Datahub API，格式为 JSON。 接受：应用程序/json


## <a name="api-requests"></a>API 请求

要发出 REST API 请求，请组合：

- HTTP 方法（获取、POST 和 PUT）。
- API 服务的 URL。
- 资源 URI（用于查询、提交数据、更新或删除）。
- 一个或多个 HTTP 请求标头。

或者，您可以在 GET 调用上包括查询参数，以筛选 、限制 的大小和在响应中对数据进行排序。

以下示例请求是获取设备列表：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

大多数 GET、POST 和 PUT 呼叫都需要 JSON 请求正文。

以下示例请求是创建设备。 此示例具有带有请求正文的输入 JSON。


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>数据格式

JSON 是一种与语言无关的通用数据格式，它提供了任意数据结构的简单文本表示形式。 有关详细信息，请参阅[JSON 组织](https://JSON.org)。

## <a name="ingest-imagery-into-farmbeats"></a>将图像引入服务器场节拍

合作伙伴拥有连接到 FarmBeats Datahub 的凭据后，合作伙伴将执行"翻译"组件中的以下步骤。

1.  根据要上载的图像类型，为以下字段创建新的扩展类型：

    - **场景源**：例如，drone_partner_name
    - **场景类型**：例如，无人机
    - **场景文件类型**：例如，叶绿素索引
    - **场景文件内容类型**：例如，图像/图片

2.  调用 /Farms API 从 Azure FarmBeats 系统获取服务器场列表。
3.  为客户提供从服务器场列表中选择单个服务器场的能力。

    合作伙伴系统必须在合作伙伴软件中显示服务器场，以执行路径规划和无人机飞行和图像收集。

4.  调用 /Scene API 并提供所需的详细信息，以创建具有唯一场景 ID 的新场景。
5.  接收 blob SAS URL，以在服务器场Beats系统中将所需的图像上载到服务器场Beats Datahub（所选服务器场的上下文中）。

下面是 API 调用的详细流。

### <a name="step-1-extendedtype"></a>第 1 步：扩展类型

签入 /扩展类型 API 以查看类型和文件源在 FarmBeats 上是否可用。 为此，请调用 /扩展类型 API 上的 GET。

以下是系统定义的值：

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

此步骤是一次性设置。 此新场景类型的范围仅限于安装 Azure FarmBeats 的订阅。

例如，要添加 SceneSource："SlantRange"，您需要在 /扩展类型 API 的 ID 上使用键"SceneSource"输入负载执行 PUT。

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

绿色字段是系统定义的场景源值的新添加。

### <a name="step-2-get-farm-details"></a>第 2 步：获取服务器场详细信息

场景（.tiff 或 .csv 文件）位于服务器场的上下文中。 您需要通过在 /Farm API 上执行 GET 来获取服务器场详细信息。 API 返回服务器场节拍中可用的服务器场列表。 您可以选择要为其引入数据的服务器场。

GET /农场响应：

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>第 3 步：创建场景 ID（POST 调用）

使用给定的信息创建新的场景（.tiff 或 .csv 文件），该信息提供与场景关联的日期、序列和服务器场 ID。 与场景关联的元数据可以在属性下定义，其中包括度量值的持续时间和类型。

创建新场景将创建一个与服务器场关联的新场景 ID。 创建场景 ID 后，用户可以使用相同的来创建新文件 （.tiff 或 .csv） 并存储文件的内容。

在 /Scene API 上的 POST 调用的示例输入负载：

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API 响应：

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**创建场景文件**

步骤 3 中返回的场景 ID 是场景文件的输入。 场景文件返回一个 SAS URL 令牌，该令牌的有效期为 24 小时。

如果用户需要上传图像流的编程方法，则 Blob 存储 SDK 可用于使用场景文件 ID、位置和 URL 定义方法。

在 /SceneFile API 上的 POST 调用的示例输入负载：

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API 响应：

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

对 /SceneFile API 的 POST 调用返回 SAS 上载 URL，该 URL 可用于使用 Azure Blob 存储客户端或库上载 .csv 或 .tiff 文件。


## <a name="next-steps"></a>后续步骤

有关基于 REST API 的集成详细信息的详细信息，请参阅[REST API](rest-api-in-azure-farmbeats.md)。
