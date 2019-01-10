---
title: 向 Azure 时序见解环境发送事件 | Microsoft Docs
description: 了解如何配置事件中心，并运行示例应用程序来推送可以在 Azure 时序见解中查看的事件。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 424476b91537c60a6d7f0f9a854453353bf98633
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557013"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>通过使用事件中心向时序见解环境发送事件

本文介绍如何在 Azure 事件中心中创建和配置事件中心，并运行示例应用程序来推送事件。 如果已经有了一个事件中心，其中的事件采用 JSON 格式，则可跳过本教程，在 [Azure 时序见解](./time-series-insights-update-create-environment.md)中查看环境。

## <a name="configure-an-event-hub"></a>配置事件中心

1. 若要了解如何创建事件中心，请参阅[事件中心文档](https://docs.microsoft.com/azure/event-hubs/)。
1. 在搜索框中，搜索“事件中心”。 在返回的列表中选择“事件中心”。
1. 选择事件中心。
1. 在创建事件中心时，实际上要创建事件中心命名空间。 如果尚未在命名空间中创建事件中心，请在菜单中的“实体”下创建事件中心。  

    ![事件中心的列表][1]

1. 创建事件中心后，请在事件中心列表中选择它。
1. 在菜单中的“实体”下，选择“事件中心”。
1. 选择事件中心的名称对其进行配置。
1. 在“实体”下选择“使用者组”，然后选择“使用者组”。

    ![创建使用者组][2]

1. 请确保创建一个使用者组，由时序见解事件源独占使用。

    > [!IMPORTANT]
    > 请确保该使用者组没有被任何其他服务（例如 Azure 流分析作业或其他时序见解环境）使用。 如果使用者组由其他服务使用，则此环境和其他服务的读取操作会受到负面影响。 如果使用 $Default 作为使用者组，则其他读者可能会重复使用使用者组。

1. 在菜单中的“设置”下，选择“共享访问策略”，然后选择“添加”。

    ![选择“共享访问策略”，然后选择“添加”按钮][3]

1. 在“添加新的共享访问策略”窗格中，创建名为“MySendPolicy”的共享访问。 将使用此共享访问策略在本文后面的 C# 示例中发送事件。

    ![在“策略名称”框中输入“MySendPolicy”][4]

1. 在“声明”下选择“发送”复选框。

## <a name="add-a-time-series-insights-instance"></a>添加时序见解实例

时序见解更新使用实例将上下文数据添加到传入的遥测数据中。 使用时间序列 ID 在查询时加入数据。 在本文后面使用的示例 windmills 项目的时间序列 ID 是 Id。若要详细了解时序见解实例和时间序列 ID，请参阅[时序模型](./time-series-insights-update-tsm.md)。

### <a name="create-a-time-series-insights-event-source"></a>创建时序见解事件源

1. 如果尚未创建事件源，请完成步骤以[创建事件源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。

1. 为 `timeSeriesId` 设置一个值。 若要详细了解时间序列 ID，请参阅[时序模型](./time-series-insights-update-tsm.md)。

### <a name="push-events"></a>推送事件（windmills 示例）

1. 在搜索栏中搜索“事件中心”。 在返回的列表中选择“事件中心”。

1. 选择事件中心。

1. 转到“共享访问策略” > “RootManageSharedAccessKey”。 复制连接字符串 - 主键的值。

    ![复制主键连接字符串的值][5]

1. 转到  https://tsiclientsample.azurewebsites.net/windFarmGen.html 。 URL 运行模拟 windmill 设备。
1. 在网页上的“事件中心连接字符串”框中，粘贴在[推送事件](#push-events)中复制的连接字符串。
  
    ![将主键连接字符串粘贴到“事件中心连接字符串”框中][6]

1. 选择“单击可启动”。 模拟器生成可以直接使用的实例 JSON。

1. 返回到 Azure 门户中的事件中心。 在“概述”页面上，应该可以看到事件中心收到的新事件：

    ![显示事件中心指标的事件中心“概述”页面][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>支持的 JSON 形状

### <a name="sample-1"></a>示例 1

#### <a name="input"></a>输入

一个简单的 JSON 对象：

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>输出：一个事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>示例 2

#### <a name="input"></a>输入

包含两个 JSON 对象的 JSON 数组。 每个 JSON 对象都转换为事件。

```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```

#### <a name="output-two-events"></a>输出：两个事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>示例 3

#### <a name="input"></a>输入

具有嵌套 JSON 数组（其中包含两个 JSON 对象）的 JSON 对象：

```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}
```

#### <a name="output-two-events"></a>输出：两个事件

“location”属性复制到每个事件。

|位置|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>示例 4

#### <a name="input"></a>输入

具有嵌套 JSON 数组（其中包含两个 JSON 对象）的 JSON 对象。 此输入表明复杂 JSON 对象可以表示全局属性。

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```

#### <a name="output-two-events"></a>输出：两个事件

|位置|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|压强|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|振动|abs G|217.09|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在时序见解资源管理器中查看环境](https://insights.timeseries.azure.com)

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
