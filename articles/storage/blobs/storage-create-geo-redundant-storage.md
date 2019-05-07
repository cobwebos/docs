---
title: 教程：使用 Blob 存储构建高度可用的应用程序 - Azure 存储
description: 使用读取访问异地冗余存储实现应用程序数据的高可用性
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: d201c0ae04e677a140e575910ff84c9d5a4dca9e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148543"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>教程：使用 Blob 存储构建高度可用的应用程序

本教程是一个系列中的第一部分。 本教程介绍如何在 Azure 中实现应用程序数据的高可用性。

完成本教程后，将会生成一个控制台应用程序，用于上传 Blob 并从[读取访问异地冗余](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) 存储帐户检索它。

RA-GRS 的工作方式是将事务从主要区域复制到次要区域。 此复制过程可保证次要区域中的数据最终保持一致。 应用程序使用[断路器](/azure/architecture/patterns/circuit-breaker)模式来确定要连接到的终结点，在模拟故障和恢复时在终结点之间自动切换。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 设置连接字符串
> * 运行控制台应用程序

## <a name="prerequisites"></a>先决条件

完成本教程：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
  - **Azure 开发**

  ![Azure 开发（在“Web 和云”下）](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* [安装 Python](https://www.python.org/downloads/)
* 下载并安装[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

* 从命令行安装和配置要使用的 [Maven](https://maven.apache.org/download.cgi)
* 安装并配置 [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

* 从命令行安装和配置要使用的 [Maven](http://maven.apache.org/download.cgi)
* 安装并配置 [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

---

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-storage-account"></a>创建存储帐户

存储帐户提供唯一的命名空间来存储和访问 Azure 存储数据对象。

请按照以下步骤操作，创建读取访问异地冗余存储帐户：

1. 选择 Azure 门户左上角的“创建资源”按钮。
2. 从“新建”页面中选择“存储”。
3. 在“特色”下选择“存储帐户 - Blob、文件、表、队列”。
4. 在存储帐户表单中填写以下信息（如下图所示），再选择“创建”：

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **名称** | mystorageaccount | 存储帐户的唯一值 |
   | **部署模型** | 资源管理器  | “资源管理器”包含最新功能。|
   | **帐户种类** | StorageV2 | 若要详细了解帐户类型，请参阅[存储帐户类型](../common/storage-introduction.md#types-of-storage-accounts) |
   | **性能** | 标准 | “标准”足以满足示例方案需求。 |
   | **复制**| 读取访问异地冗余存储 (RA-GRS) | 此为示例正常运行所必需。 |
   |**订阅** | 用户订阅 |有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   |**ResourceGroup** | myResourceGroup |如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   |**位置** | 美国东部 | 选择一个位置。 |

![创建存储帐户](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>下载示例

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[下载示例项目](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)并解压缩 storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个控制台应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[下载示例项目](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)并解压缩 storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个基本的 Python 应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

[下载示例项目](https://github.com/Azure-Samples/storage-java-ha-ra-grs)并解压缩 storage-java-ragrs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个基本的 Java 应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

[下载示例项目](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs)并解压缩 storage-java-ragrs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个基本的 Java 应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

---

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

在应用程序中，必须为存储帐户提供连接字符串。 建议将此连接字符串存储在运行应用程序的本地计算机的环境变量中。 根据操作系统按照下面的一个示例创建环境变量。

在 Azure 门户中导航到存储帐户。 在存储帐户的“设置”下选择“访问密钥”。 从主密钥或辅助密钥复制**连接字符串**。 基于操作系统运行以下命令之一，以便将 \<yourconnectionstring\> 替换为实际的连接字符串。 此命令会将一个环境变量保存到本地计算机。 在 Windows 中，必须重载正在使用的**命令提示符**或 shell，该环境变量才可用。 请替换以下示例中的 **\<storageConnectionString\>**：

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在应用程序中，必须为存储帐户提供连接字符串。 建议将此连接字符串存储在运行应用程序的本地计算机的环境变量中。 根据操作系统按照下面的一个示例创建环境变量。

在 Azure 门户中导航到存储帐户。 在存储帐户的“设置”下选择“访问密钥”。 从主密钥或辅助密钥复制**连接字符串**。 基于操作系统运行以下命令之一，以便将 \<yourconnectionstring\> 替换为实际的连接字符串。 此命令会将一个环境变量保存到本地计算机。 在 Windows 中，必须重载正在使用的**命令提示符**或 shell，该环境变量才可用。 请替换以下示例中的 **\<storageConnectionString\>**：

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

在应用程序中，必须为存储帐户提供连接字符串。 建议将此连接字符串存储在运行应用程序的本地计算机的环境变量中。 根据操作系统按照下面的一个示例创建环境变量。

在 Azure 门户中导航到存储帐户。 在存储帐户的“设置”下选择“访问密钥”。 从主密钥或辅助密钥复制**连接字符串**。 基于操作系统运行以下命令之一，以便将 \<yourconnectionstring\> 替换为实际的连接字符串。 此命令会将一个环境变量保存到本地计算机。 在 Windows 中，必须重载正在使用的**命令提示符**或 shell，该环境变量才可用。 请替换以下示例中的 **\<storageConnectionString\>**：

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

此示例要求安全地存储存储帐户的名称和密钥。 请将其存储在将要运行示例的计算机的本地环境变量中。 根据操作系统使用 Linux 或 Windows 示例创建环境变量。 在 Windows 中，必须重载正在使用的**命令提示符**或 shell，该环境变量才可用。

### <a name="linux-example"></a>Linux 示例

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Windows 示例

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-console-application"></a>运行控制台应用程序

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

在 Visual Studio 中，按 F5 或选择“启动”，开始调试应用程序。 Visual Studio 会自动还原缺少的 NuGet 包（若已配置）。若要了解详情，请参阅[通过包还原安装和重新安装包](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)。

此时，控制台窗口启动，应用程序开始运行。 应用程序将 HelloWorld.png 映像从解决方案上传到存储帐户。 应用程序会进行检查，以确保映像已复制到辅助 RA-GRS 终结点。 然后，它开始下载映像，最多可下载 999 次。 每次读取由 **P** 或 **S** 表示。其中，P 表示主终结点，S 表示辅助终结点。

![控制台应用程序正在运行](media/storage-create-geo-redundant-storage/figure3.png)

在示例代码中，`Program.cs` 文件中的 `RunCircuitBreakerAsync` 任务使用 [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) 方法从存储帐户下载映像。 下载前，将会先定义 [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet)。 操作上下文定义了在下载成功完成或下载失败并重试时触发的事件处理程序。

# <a name="pythontabpython"></a>[Python](#tab/python)

若要在终端或命令提示符处运行应用程序，请转到 **circuitbreaker.py** 目录，然后输入 `python circuitbreaker.py`。 应用程序将 HelloWorld.png 映像从解决方案上传到存储帐户。 应用程序会进行检查，以确保映像已复制到辅助 RA-GRS 终结点。 然后，它开始下载映像，最多可下载 999 次。 每次读取由 **P** 或 **S** 表示。其中，P 表示主终结点，S 表示辅助终结点。

![控制台应用程序正在运行](media/storage-create-geo-redundant-storage/figure3.png)

在示例代码中，使用了 `circuitbreaker.py` 文件中的 `run_circuit_breaker` 方法通过 [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) 方法来下载存储帐户中的映像。

存储对象重试函数设置为线性重试策略。 重试函数决定了是否重试某个请求，并指定了在重试该请求之前需要等待的秒数。 如果应该在针对主终结点的初始请求失败以后重试针对次要终结点的请求，请将 **retry\_to\_secondary** 值设置为 true。 在示例应用程序的存储对象的 `retry_callback` 函数中，定义了自定义重试策略。

在下载之前，定义了服务对象的 [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 和 [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 函数。 这些函数定义了在下载成功完成或下载失败并重试时触发的事件处理程序。

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

若要运行应用程序，可以打开一个终端或命令提示符，其作用域为下载的应用程序所在的文件夹。 在该处输入 `mvn compile exec:java` 即可运行应用程序。 然后，应用程序会将 **HelloWorld.png** 映像从该目录上传到存储帐户并进行检查，确保映像已复制到辅助 RA-GRS 终结点。 检查完成以后，应用程序就会开始重复下载该映像，同时会将从其下载映像的终结点报告回来。

存储对象重试函数设置为使用线性重试策略。 重试函数决定了是否重试某个请求，并指定了在每次重试之前需要等待的秒数。 **BlobRequestOptions** 的 **LocationMode** 属性设置为 **PRIMARY\_THEN\_SECONDARY**。 这样，如果应用程序在尝试下载 **HelloWorld.png** 时无法访问主位置，则会自动将其切换到辅助位置。

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

若要运行示例，请在命令行使用 Maven。

1. 打开 shell 并浏览到已克隆目录中的 **storage-blobs-java-v10-quickstart**。
2. 输入 `mvn compile exec:java` 。

此示例在默认目录中为 Windows 用户创建一个测试文件，该目录为 **AppData\Local\Temp**。然后，示例会提供下述可以输入的命令选项：

- 输入 **P** 可执行一个放置 Blob 操作，将临时文件上传到存储帐户。
- 输入 **L** 可执行一个列表 Blob 操作，列出目前在容器中的 Blob。
- 输入 **G** 可执行一个获取 Blob 操作，将文件从存储帐户下载到本地计算机。
- 输入 **D** 可执行一个删除 Blob 操作，将 Blob 从存储帐户中删除。
- 输入 **E** 可关闭示例，同时也会删除示例创建的所有资源。

如果在 Windows 上运行应用程序，则此示例显示输出。

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

你可以控制此示例，因此请输入命令，让其运行代码。 输入区分大小写。

---

## <a name="understand-the-sample-code"></a>了解示例代码

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>重试事件处理程序

当映像下载失败并设置为重试时，将调用 `OperationContextRetrying` 事件处理程序。 如果达到应用程序中定义的重试次数上限，请求的 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) 会变为 `SecondaryOnly`。 此设置强制应用程序尝试从辅助终结点下载映像。 此配置减少了请求获取映像所需的时间，因为不会无限重试主终结点。

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

### <a name="request-completed-event-handler"></a>已完成请求的事件处理程序

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

# <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>重试事件处理程序

当映像下载失败并设置为重试时，将调用 `retry_callback` 事件处理程序。 如果达到应用程序中定义的重试次数上限，请求的 [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) 会变为 `SECONDARY`。 此设置强制应用程序尝试从辅助终结点下载映像。 此配置减少了请求获取映像所需的时间，因为不会无限重试主终结点。

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

### <a name="request-completed-event-handler"></a>已完成请求的事件处理程序

当映像成功下载时，将调用 `response_callback` 事件处理程序。 如果应用程序使用的是辅助终结点，应用程序将继续使用此终结点（最多 20 次）。 20 次后，应用程序会将 [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) 设置回 `PRIMARY`，并重试主终结点。 如果请求成功，应用程序会继续从主终结点读取内容。

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

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

使用 Java 时，如果 **BlobRequestOptions** 的 **LocationMode** 属性设置为 **PRIMARY\_THEN\_SECONDARY**，则不需定义回调处理程序。 这样，如果应用程序在尝试下载 **HelloWorld.png** 时无法访问主位置，则会自动将其切换到辅助位置。

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

使用 Java V10 SDK 时，仍然不需定义回调处理程序，此 SDK 现在有一些不同于 V7 SDK 的基本区别。 我们使用辅助**管道**，而不使用 LocationMode。 可以通过 **RequestRetryOptions** 定义一个辅助管道。该管道定义好以后，应用程序就可以在无法通过主管道访问数据的情况下自动切换到辅助管道。

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

---

## <a name="next-steps"></a>后续步骤

本系列教程的第一部分介绍了如何使用 RA-GRS 存储帐户实现应用程序的高可用性。

请继续学习本系列教程的第二部分，了解如何模拟故障和强制应用程序使用辅助 RA-GRS 终结点。

> [!div class="nextstepaction"]
> [模拟在连接到主存储终结点时发生的故障](storage-simulate-failure-ragrs-account-app.md)
