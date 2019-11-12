---
title: 图像合作伙伴集成
description: 介绍了图像合作伙伴集成
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 232ade55350ad1ac85612cf8c69117c19cb20d0c
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927797"
---
# <a name="imagery-partner-integration"></a>图像合作伙伴集成

本文介绍如何使用 Azure FarmBeats 转换器组件将图像数据发送到 FarmBeats。 农业图像数据可以来自各种源，包括 multispectral 相机、卫星和无人机。 农业图像合作伙伴可与 FarmBeats 集成，为客户提供其场的自定义生成的地图。

数据一经提供，就可以通过 FarmBeats 加速器进行可视化，并可能用于由农业企业或客户系统集成商构建的数据合成和（机器学习/人工智能） ML/AI 模型。

FarmBeats 提供以下功能：

- 使用扩展类型 Api 定义自定义图像类型、源、文件格式
- 通过场景 & SceneFile Api，从各种源引入图像数据。

以下信息重点介绍如何在 FarmBeats 系统中获取任何形式的图像。

选择 "无人机图像" 部分时，会打开一个弹出窗口，显示无人机 orthomosaic 的高分辨率图像。 你可以访问合作伙伴软件，该软件有助于规划无人机航班和获取原始数据。 你将继续使用合作伙伴的软件进行路径规划和 orthomosaic 图像拼接。

无人机合作伙伴需要使客户能够将其客户帐户与其在 Azure 上的 FarmBeats 实例链接在一起。

你必须在无人机合作伙伴软件中使用以下凭据来链接 FarmBeats：

- API 终结点
- 租户 ID
- 客户端 ID
- 客户端机密

## <a name="api-development"></a>API 开发

Api 包含 Swagger 技术文档。 查看[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)以获取有关 api 和相应请求/响应的信息。

## <a name="authentication"></a>身份验证

FarmBeats 利用 Microsoft Azure 的[Active Directory]((https://docs.microsoft.com/azure/app-service/overview-authentication-authorization))。 身份验证 Azure App Service 提供内置的身份验证和授权支持。 

有关的详细信息，请参阅[Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。   

FarmBeats 数据中心使用持有者身份验证，需要以下凭据：

- 客户端 ID
- 客户端机密
- 租户 ID

使用上述凭据，调用方可以在标头部分中请求需要在后续 API 请求中发送的访问令牌，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

下面是一个用于检索访问令牌的 Python 代码示例。 然后，可以使用该令牌对 FarmBeat：  的后续 API 调用  
导入 azure 

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

以下是在对 FarmBeats 数据中心进行 API 调用时需要指定的最常见请求标头：

**标头** | **说明和示例**
--- | ---
Content-Type  | FarmBeats 数据中心 API 格式的请求格式（Content-type： application/<format>）为 json。 Content-type： application/json
授权 | 指定进行 API 调用所需的访问令牌。 授权：持有者 < 访问令牌 >
Accept  | 响应格式。 对于 FarmBeats 数据中心 Api，格式为 json Accept： application/json


## <a name="api-requests"></a>API 请求

若要发出 REST API 请求，请将 HTTP 方法（GET/POST/PUT）、API 服务的 URL、资源 URI （用于查询、提交数据、更新或删除）以及一个或多个 HTTP 请求标头组合在一起。

或者，您可以在 GET 调用中包含查询参数以筛选、限制的大小，并对响应中的数据进行排序。

下面的示例请求是获取设备的列表：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

下面的示例请求是创建一个设备（它具有带有请求正文的输入 JSON）。


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>数据格式

JSON （JavaScript 对象表示法）是一种与语言无关的公共数据格式，提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅[JSON org](https://JSON.org)。

## <a name="ingesting-imagery-into-farmbeats"></a>引入图像进入 FarmBeats

合作伙伴有权连接到 FarmBeats 数据中心后，该合作伙伴会在转换器组件中执行以下操作：

1.  根据要上传的图像的类型，为以下字段创建新的扩展类型：

    - 场景源：例如，< drone_partner_name >
    - 场景类型：例如，<drone>
    - 场景文件类型：例如，<chlorophyll index>
    - 场景文件内容类型：例如，< image/tiff >

2.  调用场 API，从 Azure FarmBeats 系统中获取场列表。
3.  让客户能够从场列表中选择单个场。

    合作伙伴系统必须在合作伙伴软件中显示场，才能执行路径规划和无人机航班和图像收集。

4.  调用场景 API 并提供所需的详细信息，以使用唯一 SceneID 创建新场景。
5.  接收 Blob SAS URL，以将所需的映像上传到 FarmBeats 数据中心（在所选场的上下文中）到 FarmBeats 系统。

下面是 API 调用的详细流：

### <a name="step-1-extendedtype"></a>步骤1： ExtendedType

如果类型和文件源在 FarmBeats 上可用，请查看 ExtendedType API。 可以通过在/ExtendedType API 上调用 GET 来实现此目的。

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

这将是一次性设置，此新 scenetype 的范围限制为在其中部署 FarmBeats 项目的订阅。

示例：若要添加 SceneSource： "SlantRange"，请将/ExtendedType 的 ID 放在输入负载为 "SceneSource" 的上：

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

绿色字段是系统定义的场景源值的新添加项。

### <a name="step-2-get-farmdetails"></a>步骤2：获取 FarmDetails

幕后（tiff 或 .csv 文件）将位于场的上下文中。 你需要通过执行 get on/Farm API 获取场详细信息。 API 将返回 FarmBeats 中提供的场列表，你可以选择要为其引入数据的场。

获取/Farm 响应：

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

### <a name="step-3-create-ascene-id-post-call"></a>步骤3：创建/场景 ID （post 调用）

使用给定的信息创建新的场景（tiff 或 .csv 文件），提供与该场景关联的日期、序列和场 ID。 可以在 "属性" 下定义与场景关联的元数据，包括 "持续时间" 和 "度量值" 类型。

这将创建一个新的 SceneID，它将与场关联。 创建 SceneID 后，用户可以使用相同的创建新文件（tiff 或 .csv） & 存储文件的内容。

/场景 API 上的 Post 调用的示例输入负载

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

**Create/SceneFile**

步骤3中返回的场景 ID 是 SceneFile 的输入。 SceneFile 返回一个 SAS URL 令牌，该令牌的有效时间为24小时。

如果用户需要以编程方式上传图像流，则可以使用 blob 存储 SDK 来定义使用 Scenefile ID、location & URL 的方法。

/Scenefile API 上的 Post 调用的示例输入负载：

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

对/SceneFile API 的 Post 调用返回一个 SAS 上载 URL，该 URL 可用于使用 Azure Blob 存储客户端/库上传 csv 或 tiff 文件。


## <a name="next-steps"></a>后续步骤

有关基于 Rest API 的集成详细信息的详细信息，请参阅[REST API](references-for-farmbeats.md#rest-api)。
