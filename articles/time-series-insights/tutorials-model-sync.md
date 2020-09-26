---
title: 在 Azure 数字孪生与时序见解之间进行模型同步 |Microsoft Docs
description: 用于将 ADT 中的资产模型转换为 Azure TSI 中的资产模型的最佳做法和工具
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: dpalled
ms.openlocfilehash: c3948a5bdfce583384992fb87bf40e9e7251974d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341552"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Azure 数字孪生与时序见解 Gen2 之间的模型同步

本文介绍了用于将 Azure)  (孪生中的资产模型转换为 Azure 时序见解 (TSI) 中的资产模型的最佳做法和工具。  本文是两部分系列教程的第二部分，介绍如何将 Azure 数字孪生与 Azure 时序见解相集成。 将 Azure 数字孪生与时序见解相集成，可以存档和跟踪数字孪生的 telemetries 和计算属性的历史记录。 这一系列教程面向开发人员，用于将时序见解与 Azure 数字孪生相集成。 第1部分介绍了  [如何建立数据管道，将 Azure 数字孪生中的实际时序数据引入时序见解](https://docs.microsoft.com/azure/digital-twins/how-to-integrate-time-series-insights) ，这是本教程系列的第二部分介绍了 Azure 数字孪生与时序见解之间的资产模型同步。 本教程介绍为时序 ID 选择和建立命名约定 (TS ID) 并在时序模型 (TSM) 中手动建立层次结构的最佳做法。

## <a name="choosing-a-time-series-id"></a>选择时序 ID

时序 ID 是用于标识时序见解中的资产的唯一标识符。 时序数据从字段 (telemetries，这是时间值对) 使用 TSID 下列出的变量表示。 在 Azure 数字孪生中，克隆属性和 telemetries 用于表示每个输出产生的输出的状态。 在当前的 TSM 设计中，Tsid 必须是唯一的。 在 Azure 数字孪生中使用孪生的克隆 Id，或将其与属性或遥测名称结合使用，将始终在 TSM 中提供唯一的 TS ID。 在典型情况下， **_`<Twin ID>`_** 将为 TSID，属性和遥测名称将为 TSM 中的变量。 但是，在某些情况下，可以使用复合密钥格式（例如）平展时序见解中的资产层次结构 **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** 。 我们来举例说明稍后的情况。 将建筑中的一个房间看作是一个克隆，并具有 Room22 的克隆 ID。 其温度设置属性将转换为 **_TSID Room22_TempSetting_** ，并将温度度量转换为 TSM **_Room22_TempMea_** 。

[![选择时序 ID](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualizing 时序

) 时序见解中的 Contextualization 数据 (大部分空间都是通过资产层次结构实现的，并且在时序见解资源管理器的树视图中使用相同的数据轻松导航。 时序类型和层次结构是使用时序模型 (时序见解中的 TSM) 定义的。 TSM 中的类型有助于定义变量，而层次结构级别和实例字段值用于在时序见解资源管理器中构造树视图。 有关 TSM 的详细信息，请参阅 [联机时序见解文档](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview)。

在 Azure 数字孪生中，资产间的连接使用克隆关系表示。 "克隆关系" 只是连接的资产的关系图。 但在时序见解中，资产之间的关系在本质上是分层的。 也就是说，资产共享父子类型 od 关系，并使用树结构表示。 若要将关系信息从 Azure 数字孪生转换为时序见解层次结构，需要从 Azure 数字孪生选择相关的层次结构关系。 Azure 数字孪生使用开放式标准建模语言（称为数字克隆定义语言 (DTDL) ）。 在 DTDL 模型中，使用名为 JSON 的 JSON 的变体描述。 有关规范的完整详细信息，请参阅 [DTDL 文档](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 。

[![资产间的连接](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>将 Azure 数字孪生中的图形表示形式转换为时序见解中的树状结构

本教程的以下各节将捕获一些核心方案，这些方案将 Azure 数字孪生中的图形结构手动转换为时序见解中的树状结构。

示例系统：本教程使用下面的示例来解释下面讨论的概念。 它是一个简单的虚构构建管理系统，具有一层，两个房间。 它有三个恒温器、，其中一个位于房间，另一个是楼层公用的。 此外，它还具有一个水源流量计量器，通过房间间的管道连接来测量从 Room21 到 Room22 的水源。 查看孪生之间的空间关系，它具有两种类型的关系。

1. 最常见的层次结构关系。 例如，Building40-> Floor01-> FloorTS * > 温度
1. 这并不是如此常见的循环关系。 例如，从 Building40 开始，可以通过两个不同的路径跟踪 FlowMtr。

   1. Building40-> Floor01-> Room21-> FlowMtr *-> Flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> Flow  
      其中，"Flow" 是测量 Room21 和 Room22 之间的水流量的实际遥测

> [!Note]
>
> `*` FloorTS 代表 FloorThermoStat，而 FlowMtr 表示 Flow 计量，通常选择为 TSID。 温度和流量是指时序见解中的变量的原始遥测数据。

对于时序见解中的当前限制，如果一个资产无法在多个分支中表示，以下部分说明了时序见解中分层和循环关系的建模。

## <a name="case-1-hierarchical-parent-child-relationship"></a>情况1：分层 (父子) 关系

这是孪生之间最常见的关系类型。 下图说明了建模纯父子关系。 实例字段和 TSID 派生自克隆的 id，如下所示。 尽管可以使用时序见解资源管理器手动更新实例字段，但下面的 "使用 Api 更新实例字段" 一节说明了如何使用 Azure 函数侦听 Azure 数字孪生中的模型更改，以及如何更新时序见解中的实例字段。

[![映射克隆的 Id](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![映射双子 Id 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>案例2：循环关系

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Azure 数字孪生中的循环关系到时序见解中的单一层次结构关系

假设 TSID 必须唯一，并且只能在一个层次结构中表示，这种情况下，将表示 _"FlowMtr"_ ，其 _‘Flow’_ 遥测数据应在 _"Room21"_ 下。 将来，当时序见解可以支持 TSM 中时序的多个表示形式时，遥测 _"Flow_ " 将在 _"会议室 21"_ 和 _"房间 22"_ 下表示

以下屏幕截图显示了如何将 Azure 数字孪生中的克隆 Id 手动映射到 TSM 中的实例字段和时序见解中产生的层次结构。

[![映射 Azure 数字孪生中的双子 Id](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Azure 数字孪生中的循环关系到时序见解中的多个层次结构，使用重复项

本教程的第1部分说明了客户端程序如何 (Azure function) 帮助将遥测数据从 IoT 中心或其他事件源传输到 Azure 数字孪生。 此方法建议使用相同的客户端程序对父孪生的相关属性进行更新。 在给定的示例中，从 IoT 中心读取 FlowMtr 遥测并更新 FlowMtr 中的属性 "Flow" 时，该程序还可以更新源的所有可能父孪生中的相应属性。 在我们的示例中，它将为 "outflowmea" (使用 Room21 的 "流出") 关系和 Room22 的 "inflowmea" 属性进行标识。  下面的屏幕截图显示时序见解资源管理器中的最终用户体验。 必须注意的是，我们使用这种方法进行了数据重复。

[![时序见解资源管理器](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

下面的代码片段演示了客户端应用程序如何使用 Azure 数字孪生 Api 来浏览克隆关系。

> [!Note]
>
> 此代码段示例假设读者熟悉本教程的 [第01部分](https://docs.microsoft.com/azure/digital-twins/tutorial-end-to-end#set-up-the-sample-function-app) ，并且在 "ProcessHubToDTEvents" 函数内进行了此代码更改。

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>使用 Api 更新实例字段

本教程的此部分介绍了如何使用时序见解模型 Api 以编程方式在 Azure 数字孪生中侦听模型更改，如创建、删除孪生或更改孪生和更新实例字段和层次结构之间的关系。 此更新时序见解模型的方法通常是通过 Azure 函数实现的。 在 Azure 数字孪生中，事件通知（如克隆添加或删除）可以是路由下游服务（如事件中心），进而可以送入 Azure 功能。 有关事件路由和筛选的详细信息，请参阅 [此处](https://docs.microsoft.com/azure/digital-twins/how-to-manage-routes-portal)。  本部分的其余部分介绍如何在 Azure 函数中使用时序见解模型 Api 来更新时序见解模型，以响应 Azure 数字孪生中一种类型的模型更改) 的克隆 (加法。

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>接收和识别克隆加法事件通知

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>创建时序见解客户端和添加实例详细信息

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>将层次结构信息应用到实例

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>后续步骤

第三篇教程介绍了如何使用时序见解 Api 从 Azure 数字孪生查询历史数据。 它是正在进行的工作，并且在准备就绪时将更新该部分。 同时，建议读者参阅 [时序见解数据查询 API 文档](https://docs.microsoft.com/azure/time-series-insights/concepts-query-overview)。
