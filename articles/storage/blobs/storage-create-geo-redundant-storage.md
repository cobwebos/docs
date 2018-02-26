---
title: "在 Azure 中实现应用程序数据的高可用性 | Microsoft Docs"
description: "使用读取访问异地冗余存储实现应用程序数据的高可用性"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 7b7625b3250d7e5b4cdb4090f34072eb58dda07c
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>使用 Azure 存储实现应用程序数据的高可用性

本教程是一个教程系列的第一部分，介绍了如何在 Azure 中实现应用程序数据的高可用性。 完成本教程后，将会生成一个控制台应用程序，用于检索 blob 并将它上传到[读取访问异地冗余](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) 存储帐户。 RA-GRS 的工作方式是将事务从主要区域复制到次要区域。 此复制过程可保证次要区域中的数据最终保持一致。 应用程序使用[断路器](/azure/architecture/patterns/circuit-breaker)模式，确定要连接到的终结点。 模拟故障时，应用程序会切换到辅助终结点。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 下载示例
> * 设置连接字符串
> * 运行控制台应用程序

## <a name="prerequisites"></a>先决条件

完成本教程：
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
  - **Azure 开发**

  ![Azure 开发（在“Web 和云”下）](media/storage-create-geo-redundant-storage/workloads.png)

* （可选）下载并安装 [Fiddler](https://www.telerik.com/download/fiddler)
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* [安装 Python](https://www.python.org/downloads/)
* 下载并安装[用于 Python 的 Azure 存储 SDK](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python)
* （可选）下载并安装 [Fiddler](https://www.telerik.com/download/fiddler)

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-storage-account"></a>创建存储帐户

存储帐户提供唯一的命名空间来存储和访问 Azure 存储数据对象。

请按照以下步骤操作，创建读取访问异地冗余存储帐户：

1. 选择 Azure 门户左上角的“创建资源”按钮。

2. 选择“新建”页上的“存储”，再选择“特别推荐”下的“存储帐户 - blob、文件、表、队列”。
3. 在存储帐户表单中填写以下信息（如下图所示），再选择“创建”：

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | 存储帐户的唯一值 |
   | **部署模型** | 资源管理器  | “资源管理器”包含最新功能。|
   | **帐户种类** | 常规用途 | 若要详细了解帐户类型，请参阅[存储帐户类型](../common/storage-introduction.md#types-of-storage-accounts) |
   | **性能** | 标准 | “标准”足以满足示例方案需求。 |
   | **复制**| 读取访问异地冗余存储 (RA-GRS) | 此为示例正常运行所必需。 |
   |**需要安全传输** | 已禁用| 对于此方案，无需安全传输。 |
   |**订阅** | 用户订阅 |有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   |**ResourceGroup** | myResourceGroup |如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   |**位置** | 美国东部 | 选择一个位置。 |

![创建存储帐户](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>下载示例

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[下载示例项目](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)并解压缩 storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个控制台应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[下载示例项目](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)并解压缩 storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个基本的 Python 应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>设置连接字符串

在应用程序中，必须为存储帐户提供连接字符串。 建议将此连接字符串存储在运行应用程序的本地计算机的环境变量中。 根据操作系统按照下面的一个示例创建环境变量。

在 Azure 门户中导航到存储帐户。 在存储帐户的“设置”下选择“访问密钥”。 从主密钥或辅助密钥复制**连接字符串**。 基于操作系统运行以下命令之一，以便将 \<yourconnectionstring\> 替换为实际的连接字符串。 此命令会将一个环境变量保存到本地计算机。 在 Windows 中，必须重载正在使用的**命令提示符**或 shell，该环境变量才可用。 请替换以下示例中的 **\<storageConnectionString\>**：

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>运行控制台应用程序

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
在 Visual Studio 中，按 F5 或选择“启动”，开始调试应用程序。 Visual Studio 会自动还原缺少的 NuGet 包（若已配置）。若要了解详情，请参阅[通过包还原安装和重新安装包](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)。

此时，控制台窗口启动，应用程序开始运行。 应用程序将 HelloWorld.png 映像从解决方案上传到存储帐户。 应用程序会进行检查，以确保映像已复制到辅助 RA-GRS 终结点。 然后，它开始下载映像，最多可下载 999 次。 每次读取由 **P** 或 **S** 表示。其中，P 表示主终结点，S 表示辅助终结点。

![控制台应用程序正在运行](media/storage-create-geo-redundant-storage/figure3.png)

在示例代码中，`Program.cs` 文件中的 `RunCircuitBreakerAsync` 任务使用 [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) 方法从存储帐户下载映像。 下载前，将会先定义 [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet)。 操作上下文定义了在下载成功完成或下载失败并重试时触发的事件处理程序。

# <a name="python-tabpython"></a>[Python] (#tab/python) 
若要在终端或命令提示符处运行应用程序，请转到 **circuitbreaker.py** 目录，然后输入 `python circuitbreaker.py`。 应用程序将 HelloWorld.png 映像从解决方案上传到存储帐户。 应用程序会进行检查，以确保映像已复制到辅助 RA-GRS 终结点。 然后，它开始下载映像，最多可下载 999 次。 每次读取由 **P** 或 **S** 表示。其中，P 表示主终结点，S 表示辅助终结点。

![控制台应用程序正在运行](media/storage-create-geo-redundant-storage/figure3.png)

在示例代码中，使用了 `circuitbreaker.py` 文件中的 `run_circuit_breaker` 方法通过 [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) 方法来下载存储帐户中的映像。 

存储对象重试函数设置为线性重试策略。 重试函数决定了是否重试某个请求，并指定了在重试该请求之前需要等待的秒数。 如果应该在针对主终结点的初始请求失败以后重试针对次要终结点的请求，请将 **retry\_to\_secondary** 值设置为 true。 在示例应用程序的存储对象的 `retry_callback` 函数中，定义了自定义重试策略。
 
在下载之前，定义了服务对象的 [retry_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 和 [response_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 函数。 这些函数定义了在下载成功完成或下载失败并重试时触发的事件处理程序。  

---

### <a name="retry-event-handler"></a>重试事件处理程序

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

当映像下载失败并设置为重试时，将调用 `OperationContextRetrying` 事件处理程序。 如果达到应用程序中定义的重试次数上限，请求的 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) 会变为 `SecondaryOnly`。 此设置强制应用程序尝试从辅助终结点下载映像。 此配置减少了请求获取映像所需的时间，因为不会无限重试主终结点。

在示例代码中，`Program.cs` 文件中的 `RunCircuitBreakerAsync` 任务使用 [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet) 方法从存储帐户下载映像。 下载前，将会先定义 [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet)。 操作上下文定义了在下载成功完成或下载失败并重试时触发的事件处理程序。
 
```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 
当映像下载失败并设置为重试时，将调用 `retry_callback` 事件处理程序。 如果达到应用程序中定义的重试次数上限，请求的 [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) 会变为 `SECONDARY`。 此设置强制应用程序尝试从辅助终结点下载映像。 此配置减少了请求获取映像所需的时间，因为不会无限重试主终结点。  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

---


### <a name="request-completed-event-handler"></a>已完成请求的事件处理程序
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

当映像成功下载时，将调用 `OperationContextRequestCompleted` 事件处理程序。 如果应用程序使用的是辅助终结点，应用程序将继续使用此终结点（最多 20 次）。 20 次后，应用程序会将 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) 设置回 `PrimaryThenSecondary`，并重试主终结点。 如果请求成功，应用程序会继续从主终结点读取内容。
 
```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

当映像成功下载时，将调用 `response_callback` 事件处理程序。 如果应用程序使用的是辅助终结点，应用程序将继续使用此终结点（最多 20 次）。 20 次后，应用程序会将 [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) 设置回 `PRIMARY`，并重试主终结点。 如果请求成功，应用程序会继续从主终结点读取内容。

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

---

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
