---
title: 教程：使用 Azure Notebooks (Python) 将传感器数据与天气预报数据相联接 | Microsoft Azure Maps
description: 本教程介绍如何使用 Azure Notebooks (Python) 将传感器数据与 Microsoft Azure Maps 天气服务提供的天气预报数据相联接。
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1a1493033717b18bef5d80b28d06004c901ffb29
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910795"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>教程：使用 Azure Notebooks (Python) 将传感器数据与天气预报数据相联接

风力发电是可以替代化石燃料的，有助于应对气候变化的一种能源。 由于风力本身具有不稳定性，风力发电运营商需要生成 ML（机器学习）模型来预测风电容量是否能够满足用电需求并确保电网的稳定性。 本教程逐步讲解如何将 Azure Maps 天气预报数据，与提供天气读数的传感器位置演示数据集合并到一起。 可以通过调用 Azure Maps 天气服务来请求天气预报数据。

在本教程中，将：

> [!div class="checklist"]
> * 在云中的 [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) 中处理数据文件。
> * 从该文件加载演示数据。
> * 在 Python 中调用 Azure Maps REST API。
> * 在地图上呈现位置数据。
> * 使用 Azure Maps 的[每日预报](https://aka.ms/AzureMapsWeatherDailyForecast)天气数据来扩充演示数据。
> * 在图中绘制预报数据。


## <a name="prerequisites"></a>必备条件

若要完成本教程，首先需要：

1. 按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明，在 S0 定价层中创建一个 Azure Maps 帐户订阅。
2. 要获取帐户的主要订阅密钥，请遵循[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的说明。


有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

若要熟悉 Azure Notebooks 并了解如何开始使用它，请参考[创建 Azure 笔记本](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)中的说明。

> [!Note]
> 可以从 [Maps 天气 Jupyter 笔记本存储库](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)下载此项目的 Jupyter 笔记本文件。

## <a name="load-the-required-modules-and-frameworks"></a>加载所需的模块和框架

若要加载全部所需的模块和框架，请运行以下脚本：

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>导入天气数据

在本教程中，我们将利用安装在四个不同风力涡轮机上的传感器发送的天气数据。 示例数据包含 30 天的天气读数，这些数字是从每个风力涡轮机附近的天气数据中心收集的。 演示数据包含温度、风速和风向的数据读数。 可从[此处](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)下载演示数据。 以下脚本将演示数据导入 Azure 笔记本。

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>请求每日预报数据

在示例方案中，我们希望请求每个传感器位置的每日预报数据。 以下脚本调用 Azure Maps 天气服务的[每日预报 API](https://aka.ms/AzureMapsWeatherDailyForecast)，来获取每个风力涡轮机在未来 15 天（自当前日期算起）的每日天气预报。


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

以下脚本通过调用 Azure Maps [获取地图图像服务](https://docs.microsoft.com/rest/api/maps/render/getmapimage)在地图上呈现涡轮机位置。

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![涡轮机位置](./media/weather-service-tutorial/location-map.png)


为了使用预报数据扩充演示数据，我们将根据天气数据中心的气象站 ID，将预报数据与演示数据分组到一起。

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

下表显示了某个涡轮机位置的、合并后的历史数据和预报数据。

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![分组后的数据](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>绘制预报数据

为了查看风速和风向在未来 15 天的变化，我们将根据预报所针对的日期，在图中绘制预报值。

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

下图可视化了自请求数据当天算起的未来 15 天风速变化（左图）和风向变化（右图）的预报数据。

<center>

![风速图](./media/weather-service-tutorial/speed-date-plot.png) ![风向图](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>后续步骤

本教程已介绍如何调用 Azure Maps REST API 来获取天气预报数据并在图中可视化数据。

若要详细了解如何在 Azure Notebooks 中调用 Azure Maps REST API，请参阅[使用 Azure Notebooks 规划电动车路线](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing)。

若要了解本教程中使用的 Azure Maps API，请参阅：

* [每日预报](https://aka.ms/AzureMapsWeatherDailyForecast)
* [呈现器 - 获取地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

有关 Azure Maps REST API 的完整列表，请参阅 [Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/consumption-model)。

若要详细了解 Azure Notebooks，请参阅 [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)。
