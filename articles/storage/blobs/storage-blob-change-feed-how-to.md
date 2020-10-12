---
title: 处理 Azure Blob 存储中的更改源 | Microsoft Docs
description: 了解如何在 .NET 客户端应用程序中处理更改源日志
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568245"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>处理 Azure Blob 存储中的更改源

更改源提供存储帐户中 Blob 和 Blob 元数据发生的所有更改的事务日志。 本文介绍如何使用 Blob 更改源处理器库读取更改源记录。

若要详细了解更改源，请参阅 [Azure Blob 存储中的更改源](storage-blob-change-feed.md)。

## <a name="get-the-blob-change-feed-processor-library"></a>获取 Blob 更改源处理器库

1. 打开一个命令窗口（例如：Windows PowerShell）。
2. 从你的项目目录中，安装 [**Azure.Storage.Blobs.Changefeed** NuGet 包](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/)。

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>读取记录

> [!NOTE]
> 更改源是存储帐户中的不可变只读实体。 任意数量的应用程序都可以同时独立读取和处理更改源，具体取决于应用程序自身的需求。 当应用程序读取记录时，不会从更改源中删除这些记录。 每个使用方读取器的读取或迭代状态是独立的，仅由应用程序维护。

此示例将循环访问更改源中的所有记录，将它们添加到列表中，然后将该列表返回到调用方。
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

此示例将列表中每个记录的几个值输出到控制台。 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>从已保存的位置继续读取记录

你可以选择保存你在更改源中的读取位置，以后可以继续循环访问记录。 可以通过获取更改源游标来保存读取位置。 该游标是一个**字符串**，你的应用程序可以采用适合你的应用程序设计的任何方式保存该字符串（例如，保存到文件或数据库中）。

此示例将循环访问更改源中的所有记录，将它们添加到列表中，然后保存游标。 列表和游标将返回到调用方。 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>记录的流式处理

你可以选择在更改源记录提交到更改源时处理这些记录。 请参阅[规范](storage-blob-change-feed.md#specifications)。 更改事件以平均 60 秒的时间间隔发布到更改源。 在指定轮询时间间隔时，建议使用此时间间隔来轮询新更改。

此示例定期轮询更改。  如果存在更改记录，此代码将处理这些记录并保存更改源游标。 这样一来，如果进程停止并再次启动，则应用程序可以使用游标继续从上次离开的位置处理记录。 此示例将游标保存到一个本地应用程序配置文件，但你的应用程序可以将其保存为最适合你的方案的任何形式。 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>读取时间范围内的记录

可以读取特定时间范围内的记录。 此示例循环访问更改源中处于 2020 年 3 月 2 日下午 3:00 到 2020 年 8 月 7 日早上 2:00 之间的所有记录，将它们添加到列表中，然后将该列表返回到调用方。

### <a name="selecting-segments-for-a-time-range"></a>根据时间范围选择段

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

你提供的开始时间将向下舍入到最接近的小时，结束时间将向上舍入到最接近的小时。 用户可能会看到在开始时间之前和结束时间之后发生的事件。 在开始和结束时间之间发生的某些事件也可能不会显示。 这是因为事件可能会记录在开始时间之前的小时内，也可能会记录在结束时间之后的小时内。

## <a name="next-steps"></a>后续步骤

详细了解更改源日志。 请参阅 [Azure Blob 存储中的更改源](storage-blob-change-feed.md)
