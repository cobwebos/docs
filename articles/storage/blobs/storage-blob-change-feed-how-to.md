---
title: Azure Blob 存储（预览）中的处理更改源 |微软文档
description: 了解如何处理 .NET 客户端应用程序中的更改源日志
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111855"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob 存储中的处理更改源（预览）

更改源提供存储帐户中 Blob 和 blob 元数据发生的所有更改的事务日志。 本文介绍如何使用 Blob 更改馈送处理器库读取更改源记录。

要了解有关更改源的更多信息，请参阅[Azure Blob 存储中的更改源（预览）。](storage-blob-change-feed.md)

> [!NOTE]
> 更改源处于公共预览版中，可在**西中部**和**西部 2**区域提供。 要了解有关此功能以及已知问题和限制的更多信息，请参阅 Azure [Blob 存储 中的更改源支持](storage-blob-change-feed.md)。 更改源处理器库在现在和此库普遍可用之间可能会发生变化。

## <a name="get-the-blob-change-feed-processor-library"></a>获取 Blob 更改馈送处理器库

1. 在 Visual Studio 中`https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json`，将 URL 添加到 NuGet 包源。 

   要了解如何使用，请参阅[包源](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)。

2. 在 NuGet 包管理器中，查找**Microsoft.Azure.Storage.更改源**包，并将其安装到项目中。 

   要了解如何使用，请参阅[查找并安装包](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

## <a name="connect-to-the-storage-account"></a>连接到存储帐户

通过调用[云存储帐户.TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse)方法解析连接字符串。 

然后，通过调用[云存储帐户.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)方法，创建一个表示存储帐户中的 Blob 存储的对象。

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>初始化更改源

将以下使用语句添加到代码文件的顶部。 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

然后，通过调用**GetContainerReference**方法创建**ChangeFeed**类的实例。 传递更改源容器的名称。

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>读取记录

> [!NOTE]
> 更改源是存储帐户中的不可变和只读实体。 任意数量的应用程序都可以在方便的时候同时独立地读取和处理更改源。 当应用程序读取记录时，不会从更改源中删除记录。 每个使用读取器的读取或迭代状态仅由应用程序保持独立。

读取记录的最简单方法是创建**更改源阅读器**类的实例。 

此示例遍历更改源中的所有记录，然后从每个记录打印到控制台几个值。 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>从保存的位置恢复读取记录

您可以选择在更改源中保存读取位置，并在将来继续迭代记录。 您可以随时使用**ChangeFeedReader.序列化状态（）** 方法保存更改源的迭代状态。 状态是**字符串**，应用程序可以基于应用程序的设计保存该状态（例如：到数据库或文件）。

```csharp
    string currentReadState = processor.SerializeState();
```

您可以使用**创建更改源阅读器从PointerAsync**方法创建**更改源阅读器**，从而继续从上次状态中迭代记录。

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>记录的流处理

您可以选择在更改源记录到达时处理这些更改。 请参阅[规格](storage-blob-change-feed.md#specifications)。

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>在时间范围内读取记录

更改源根据更改事件时间组织成每小时段。 请参阅[规格](storage-blob-change-feed.md#specifications)。 可以从属于特定时间范围的更改源段读取记录。

此示例获取所有段的开始时间。 然后，它会遍载该列表，直到开始时间超过最后一个消耗品段的时间或超出所需范围的结束时间。 

### <a name="selecting-segments-for-a-time-range"></a>选择时间范围的段

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>读取段中的记录

可以从单个段或段范围读取记录。

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>从时间开始读取记录

可以从起始段读取更改源的记录，直到结束。 与在时间范围内读取记录类似，您可以列出段并选择要开始迭代的段。

此示例获取要处理的第一个段的[DateTime 偏移](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8)。

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

此示例处理从起始段[的 DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8)开始的更改源记录。

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> 的一段可以在一个或多个*块 FilePath*中具有更改源日志。 在多个*块FilePath*的情况下，系统已在内部将记录分区为多个分片，以管理发布吞吐量。 保证段的每个分区将包含互斥 blob 的更改，并且可以独立处理，而不会违反排序。 如果对方案最有效，则可以使用**ChangeFeed段ShardReader**类在分片级别迭代记录。

## <a name="next-steps"></a>后续步骤

了解有关更改源日志的更多信息。 请参阅[Azure Blob 存储中的更改源（预览）](storage-blob-change-feed.md)
