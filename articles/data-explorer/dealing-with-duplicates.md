---
title: 处理重复数据
description: 本主题介绍处理重复数据的各种方法
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 7a9ccd5139168393019a70aafb9b7b705ca25919
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756359"
---
# <a name="deal-with-duplicate-data"></a>处理重复数据

将数据发送到云的设备会维护数据的本地缓存。 根据具体的数据大小，本地缓存可将数据存储数天甚至数月之久。 我们希望能够保护重新发送缓存数据，并导致分析数据库中出现重复数据的有故障设备中的分析数据库。 本主题将概述为此类方案处理重复数据的最佳做法。

处理重复数据的最佳解决方法是防止重复。 如果可能，请解决数据管道中的旧问题，这可以节省在数据管道中移动数据的相关成本，并可避免投入资源来处理系统中引入的重复数据。 但是，在无法改造源系统的情况下，可通过多种方式来处理这种情况。

## <a name="understand-the-impact-of-duplicate-data"></a>了解重复数据造成的影响

监视重复数据的百分比。 发现重复数据的百分比之后，可以分析问题范围和对业务造成的影响，然后选择适当的解决方法。

用于识别重复记录百分比的示例查询：

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>用于处理重复数据的解决方法

### <a name="solution-1-dont-remove-duplicate-data"></a>解决方法 #1：不要删除重复数据

了解业务要求以及对重复数据的容限。 某些数据集可以处理特定百分比的重复数据。 如果重复数据不会造成重大影响，可以忽略它的存在。 不删除重复数据的优点是，在引入过程中不会产生额外的开销，也不会影响查询性能。

### <a name="solution-2-handle-duplicate-rows-during-query"></a>解决方法 #2：在查询过程中处理重复行

另一种做法是在查询过程中筛选出数据中的重复行。 使用 [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) 聚合函数可以筛选出重复记录，并基于时间戳（或另一列）返回最后一条记录。 使用此方法的优点是引入速度更快，因为重复数据删除是在查询期间发生的。 此外，所有记录（包括重复项）都可用于审核和故障排除。 使用 `arg_max` 函数的缺点是，每次查询数据都会增大查询时间和 CPU 负载。 根据查询的数据量，此解决方法可能不起作用或者消耗过多的内存，因此需要改用其他做法。

在以下示例中，我们将查询针对一组列引入的最后一条记录来确定唯一的记录：

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

也可以将此查询置于某个函数内，而不是直接查询表：

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>解决方法 #3：在引入过程中筛选重复项

另一种解决方法是在引入过程中筛选重复项。 在引入到 Kusto 表期间，系统将忽略重复数据。 删除重复行之后，将数据引入到临时表并复制到另一个表中。 此解决方法的优点是，相比前一种解决方法，查询性能可以得到明显的改善。 缺点包括增大引入时间和数据存储成本。

以下示例演示了此方法：

1. 创建具有相同架构的另一个表：

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. 创建一个函数，用于通过将新记录与以前引入的记录取消联接，来筛选出重复记录。

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > 联接是 CPU 密集型操作，会在系统中施加一个额外的负载。

1. 针对 `DeviceEventsUnique` 表设置[更新策略](/azure/kusto/management/update-policy)。 新数据进入 `DeviceEventsAll` 表时会激活更新策略。 创建新的[范围](/azure/kusto/management/extents-overview)时，Kusto 引擎会自动执行该函数。 处理范围限定为新建的数据。 以下命令将源表 (`DeviceEventsAll`)、目标表 (`DeviceEventsUnique`) 和函数 `RemoveDuplicatesDeviceEvents` 拼接在一起，以创建更新策略。

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > 更新策略会延长引入持续时间，因为数据在引入过程中需要经过筛选，然后引入两次（分别对 `DeviceEventsAll` 表和 `DeviceEventsUnique` 表引入）。

1. （可选）针对 `DeviceEventsAll` 表设置一个更短的数据保留期，以避免存储数据的副本。 选择的天数取决于数据量，并且用于故障排除的数据的保留时长。 可将其设置为 `0d` 天保留期，以节省 COGS 并改善性能，因为数据不会上传到存储。

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>摘要

可以通过多种方式处理重复数据。 请评估不同的做法，同时考虑价格和性能，以确定符合业务需求的适当方法。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 数据资源管理器的编写查询](write-queries.md)
