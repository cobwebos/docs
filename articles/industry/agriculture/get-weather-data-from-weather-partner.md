---
title: 从天气合作伙伴获取天气数据
description: 本文介绍如何从合作伙伴获取天气数据。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266157"
---
# <a name="get-weather-data-from-weather-partners"></a>从天气合作伙伴获取天气数据

Azure FarmBeats 可帮助您使用基于 Docker 的连接器框架从天气数据提供程序中带来天气数据。 使用此框架，天气数据提供程序实现可与 FarmBeats 集成的 Docker。 目前支持以下天气数据提供程序：

[来自 Azure 开放数据集的 NOAA 数据](https://azure.microsoft.com/services/open-datasets/)

天气数据可用于生成可操作的见解，并在 FarmBeats 上构建 AI/ML 模型。

## <a name="before-you-start"></a>开始之前

要获取天气数据，请确保已安装 FarmBeats。 **1.2.11 版或更高版本支持天气集成**。 要安装 Azure 服务器场节拍，请参阅[安装服务器场节拍](https://aka.ms/farmbeatsinstalldocumentation)。

## <a name="enable-weather-integration-with-farmbeats"></a>使用 FarmBeats 实现天气集成

要开始获取 FarmBeats 数据中心的天气数据，请按照以下步骤操作：

1. 转到服务器场数据中心摇曳 （https://yourdatahub.azurewebsites.net/swagger)

2. 导航到 /合作伙伴 API，并使用以下输入负载发出 POST 请求：

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   例如，要通过 Azure 开放数据集从 NOAA 获取天气数据，请使用下面的有效负载。 您可以根据您的喜好修改名称和说明。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > 有关合作伙伴对象的详细信息，请参阅[附录](get-weather-data-from-weather-partner.md#appendix)

   前面的步骤将预配资源，使 Docker 能够在客户的 FarmBeats 环境中运行。  

   提供上述资源大约需要 10-15 分钟。

3. 检查在步骤 2 中创建的 /Partner 对象的状态。 为此，请在 /合作伙伴 API 上发出 GET 请求，并检查合作伙伴对象**的状态**。 一旦 FarmBets 成功为合作伙伴提供，状态将设置为 **"活动**"。

4. 导航到 /作业类型 API，并在同一个上发出 GET 请求。 检查作为步骤 2 中的合作伙伴添加过程的一部分创建的天气作业。 天气作业中的**管道名称**字段将采用以下格式："合作伙伴-name_partner-type_job名称"。

5. 现在，您的 FarmBeats 实例具有活动天气数据合作伙伴，您可以运行作业以请求特定位置（纬度/经度）和日期范围的天气数据。 JobTypes 将详细介绍运行天气作业所需的参数。

   例如，对于 Azure 开放数据集中的 NOAA 数据，将创建作业类型以下的数据：

   - get_weather_data（获取 ISD/历史天气数据）
   - get_weather_forecast_data（获取 GFS/预测天气数据）

6. 记下作业类型的**ID**和参数。

7. 导航到 /jobs API，并在 /Jobs 上使用以下输入负载发出 POST 请求：

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   例如，要运行**get_weather_data**，请使用以下有效负载：

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 前面的步骤将运行合作伙伴 Docker 中定义的天气作业，并将天气数据引入到 FarmBeats 中。 您可以通过在 /Jobs 上发出 GET 请求来检查作业的状态，并在响应中查找**当前状态**。 完成后，当前状态设置为 **"成功**"。

## <a name="query-ingested-weather-data"></a>查询引入的天气数据

天气作业完成后，您可以查询引入的天气数据以生成模型或可操作的见解。 有两种方法可以从 FarmBeats 访问和查询天气数据：

- API 和
- 时间序列见解 （TSI）。

### <a name="query-using-rest-api"></a>使用 REST API 查询

要使用 FarmBeats REST API 查询天气数据，请按照以下步骤操作：

1. 在服务器场节拍数据中心摇曳器中（，https://yourdatahub.azurewebsites.net/swagger)导航到 /WeatherDataLocation API 并发出 GET 请求）。 响应将具有为作为作业运行的一部分指定的位置（纬度/经度）创建 /WeatherDataDatalocation 对象。 记下对象的**ID**和**天气数据模型Id。**

2. 在 /WeatherDataModel API 上为**天气数据模型 Id**制作 GET/{id}，如步骤 1 中所述。 "天气数据模型"具有有关引入的天气数据的所有元数据和详细信息。 例如，"**天气数据模型"** 对象中的 **"天气度量"** 包含有关支持哪些天气信息以及支持哪些类型和单位的详细信息。 例如，

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   记下来自天气数据模型 GET/{id} 调用的响应。

3. 导航到**遥测**API，并发出具有以下输入负载的 POST 请求：

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. 包含可用于指定时间范围的天气数据的响应如下所示：

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

在前面的示例中，响应具有两个时间戳的数据以及两个时间戳中的度量值名称（"温度"）和报告的天气数据的值。 您需要参考关联的天气数据模型（如上一步 2 所述），以解释报告值的类型和单位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 时间序列见解 （TSI） 查询

FarmBeats 使用[Azure 时间序列见解 （TSI）](https://azure.microsoft.com/services/time-series-insights/)在 IoT 比例范围内引入、存储、查询和可视化数据，这些数据高度上下文化和针对时间序列进行了优化。

在 EventHub 上接收天气数据，然后推送到 FarmBeats 资源组中的 TSI 环境。 然后可以直接从 TSI 查询数据。 有关详细信息，请参阅[TSI 文档](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)。

按照以下步骤在 TSI 上可视化数据：

1. 转到**Azure 门户** > **FarmBeats DataHub 资源组**>选择**时间序列见解**环境 （tsi-xxx） >**数据访问策略**。 使用读取器或参与者访问权限添加用户。

2. 转到**时间序列见解**环境 （tsi-xxxx）**的概述**页面，然后选择**时序见解资源管理器 URL**。 您现在可以可视化引入的天气数据。

除了存储、查询和可视化天气数据外，TSI 还支持集成到 Power BI 仪表板。 有关详细信息，请参阅[Power BI 中的时间序列见解可视化数据](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)。

## <a name="appendix"></a>附录

|        Partner   |  详细信息   |
| ------- | -------             |
|     Docker 详细信息 - 图像名称         |          Docker 映像名称。 例如，docker.io/azurefarmbeats/farmbeats-noaa（hub.docker.com中的图像）或myazureacr.azurecr.io/mydockerimage（Azure 容器注册表中的图像）等。 如果未提供注册表，则hub.docker.com      |
|          Docker 详细信息 - 图像标签             |         标记 docker 映像的名称。 默认值为"最新"     |
|  Docker 详细信息 - 凭据      |  访问专用 Docker 的凭据。 这将由合作伙伴提供给客户   |
|  Docker 详细信息 - azure BatchVM 详细信息 - 批处理VMSKU     |    Azure 批处理 VM SKU。 有关所有可用的 Linux 虚拟机，请参阅[此处](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 Valid values are: "Small', 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4'"STANDARD_G5"，"STANDARD_D5_V2"，"BASIC_A1"， "BASIC_A2"，"BASIC_A3"，"BASIC_A4"，"STANDARD_A1"，"STANDARD_A2"，"STANDARD_A3"，"STANDARD_A4"，"STANDARD_A5"，"STANDARD_A6"，"STANDARD_A7"，"STANDARD_A8"，"STANDARD_A9"，"STANDARD_A10"，"STANDARD_A11"，"STANDARD_D15_V2"，"STANDARD_F1"，"STANDARD_F2"，"STANDARD_F4"，"STANDARD_F8 STANDARD_F16"，"STANDARD_F16"，"STANDARD_NV6"STANDARD_NV24，"STANDARD_NV12"，"STANDARD_NC24r STANDARD_NC24 STANDARD_NC6 STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12，"STANDARD_H8"，"STANDARD_H8m"，"STANDARD_H16"，"STANDARD_H16m"，"STANDARD_H16mr"，"STANDARD_H16r"，"STANDARD_A1_V2"，"STANDARD_A2_V2 STANDARD_A1_V2"，"STANDARD_A1_V2"，"STANDARD_A4_V2"，"STANDARD_A8_V2"，"STANDARD_A2m_V2"，"STANDARD_A4m_V2"，"STANDARD_A8m_V2"，"STANDARD_M64ms"，"STANDARD_M128s"，"STANDARD_D2_V3"。 **默认值为"standard_d2_v2"**  |
|    Docker 详细信息 - azureBatchVM 详细信息 - 专用计算机节点   |  不是。 批处理池的专用计算机节点。 默认值为 1。 |
|    Docker 详细信息 - azure BatchVM 详细信息 - 节点代理          |    Azure 批处理节点代理 SKU ID。 目前仅支持"batch.node.ubuntu 18.04"批处理节点代理。    |
| Docker 详细信息 - 合作伙伴凭据 | 用于在 docker 中调用合作伙伴 API 的凭据。 合作伙伴需要根据支持的 auth 机制向客户提供此信息。例如。 用户名/密码或 API 密钥。 |
| 合作伙伴类型 | "天气"（FarmBeats 中的其他合作伙伴类型是"传感器"和"图像"）  |
|  name   |   FarmBeats 系统中合作伙伴的所需名称   |
|  description |  说明   |

## <a name="next-steps"></a>后续步骤

现在，您从 Azure FarmBeats 实例中查询了传感器数据。 现在，了解如何为服务器场[生成地图](generate-maps-in-azure-farmbeats.md#generate-maps)。
