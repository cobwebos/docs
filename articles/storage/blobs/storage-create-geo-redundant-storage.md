---
title: "在 Azure 中实现应用程序数据的高可用性 | Microsoft Docs"
description: "使用读取访问异地冗余存储实现应用程序数据的高可用性"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 547ca7843f53bd11fdb922af8e0ae77e38f813d9
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2017
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>使用 Azure 存储实现应用程序数据的高可用性

本教程是一个系列中的第一部分。 本教程介绍了如何在 Azure 中实现应用程序数据的高可用性。 完成本教程后，将会生成一个控制台应用程序，用于检索 blob 并将它上传到[读取访问异地冗余](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) 存储帐户。 RA-GRS 的工作方式是将事务从主要区域复制到次要区域。 此复制过程可保证次要区域中的数据最终保持一致。 应用程序使用[断路器](/azure/architecture/patterns/circuit-breaker.md)模式，确定要连接到的终结点。 模拟故障时，应用程序会切换到辅助终结点。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 下载示例
> * 设置连接字符串
> * 运行控制台应用程序

## <a name="prerequisites"></a>先决条件

完成本教程：

* 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
  - **Azure 开发**

  ![Azure 开发（在“Web 和云”下）](media/storage-create-geo-redundant-storage/workloads.png)

* 下载并安装 [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-storage-account"></a>创建存储帐户

存储帐户提供唯一的命名空间来存储和访问 Azure 存储数据对象。

请按照以下步骤操作，创建读取访问异地冗余存储帐户：

1. 选择 Azure 门户左上角的“新建”按钮。

2. 选择“新建”页上的“存储”，再选择“特别推荐”下的“存储帐户 - blob、文件、表、队列”。
3. 在存储帐户表单中填写以下信息（如下图所示），再选择“创建”：

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | 存储帐户的唯一值 |
   | **部署模型** | 资源管理器  | “资源管理器”包含最新功能。  |
   | **帐户种类** | 常规用途 | 若要详细了解帐户类型，请参阅[存储帐户类型](../common/storage-introduction.md#types-of-storage-accounts) |
   | **性能** | 标准 | “标准”足以满足示例方案需求。 |
   | **复制**| 读取访问异地冗余存储 (RA-GRS) | 此为示例正常运行所必需。 |
   |**需要安全传输** | 已禁用| 对于此方案，无需安全传输。 |
   |**订阅** | 用户订阅 |有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   |**ResourceGroup** | myResourceGroup |如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   |**位置** | 美国东部 | 选择一个位置。 |

![创建存储帐户](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>下载示例

[下载示例项目 ](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)。

解压缩 storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 文件。
示例项目包含一个控制台应用程序。

## <a name="set-the-connection-string"></a>设置连接字符串

在 Visual Studio 中，打开 storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs 控制台应用程序。

在 App.config 文件中的 appSettings 节点下，将 StorageConnectionString 值替换为存储帐户连接字符串。 此值的检索方式为，在 Azure 门户中，选择存储帐户中“设置”下的“访问密钥”。 将连接字符串从主密钥或辅助密钥复制粘贴到 App.config 文件。 完成后，选择“保存”，保存此文件。

![App.config 文件](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>运行控制台应用程序

在 Visual Studio 中，按 F5 或选择“启动”，开始调试应用程序。 Visual studio 会自动还原缺少的 Nuget 包（若已配置）。若要了解详情，请参阅[通过包还原安装和重新安装包](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)。 

此时，控制台窗口启动，应用程序开始运行。 应用程序将 HelloWorld.png 映像从解决方案上传到存储帐户。 应用程序会进行检查，以确保映像已复制到辅助 RA-GRS 终结点。 然后，它开始下载映像，最多可下载 999 次。 每次读取由 P 或 S 表示。其中，P 表示主终结点，S 表示辅助终结点。

![控制台应用程序正在运行](media/storage-create-geo-redundant-storage/figure3.png)

在示例代码中，`Program.cs` 文件中的 `RunCircuitBreakerAsync` 任务使用 [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) 方法从存储帐户下载映像。 下载前，将会先定义 [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet)。 操作上下文定义了在下载成功完成或下载失败并重试时触发的事件处理程序。

### <a name="retry-event-handler"></a>重试事件处理程序

当映像下载失败并设置为重试时，将调用 `Operation_context_Retrying` 事件处理程序。 如果达到应用程序中定义的重试次数上限，请求的 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) 变为 `SecondaryOnly`。 此设置强制应用程序尝试从辅助终结点下载映像。 此配置减少了请求获取映像所需的时间，因为不会无限重试主终结点。

```csharp
private static void Operation_context_Retrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>已完成请求的事件处理程序

当映像成功下载时，将调用 `Operation_context_RequestCompleted` 事件处理程序。 如果应用程序使用的是辅助终结点，应用程序将继续使用此终结点（最多 20 次）。 20 次后，应用程序会将 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) 设置回 `PrimaryThenSecondary`，并重试主终结点。 如果请求成功，应用程序会继续从主终结点读取内容。

```csharp
private static void Operation_context_RequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

本系列教程的第一部分介绍了如何使用 RA-GRS 存储帐户实现应用程序的高可用性，如执行以下操作：

> [!div class="checklist"]
> * 创建存储帐户
> * 下载示例
> * 设置连接字符串
> * 运行控制台应用程序

请继续学习本系列教程的第二部分，了解如何模拟故障和强制应用程序使用辅助 RA-GRS 终结点。

> [!div class="nextstepaction"]
> [模拟在连接到主存储终结点时发生的故障](storage-simulate-failure-ragrs-account-app.md)
