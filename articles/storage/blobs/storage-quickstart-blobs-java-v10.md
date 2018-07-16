---
title: Azure 快速入门 - 使用 Java 存储 SDK V10 在对象存储中创建 Blob | Microsoft Docs
description: 本快速入门介绍如何使用 Java 存储 SDK 在对象 (Blob) 存储中创建容器、上传文件、列出对象以及进行下载。
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: a789269e73e1817f6a45e1e5948dbfaa21efd283
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704464"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-java-sdk-v10-preview"></a>快速入门：使用 Java SDK V10（预览版）上传、下载和列出 Blob

本快速入门介绍如何使用新的 Java 存储 SDK 上传、下载和列出 Azure Blob 存储的容器中的块 Blob。 新的 Java SDK 将响应式编程模型与 RxJava 配合使用，提供异步操作。 [此处](https://github.com/ReactiveX/RxJava)提供 RxJava 的详细信息。 

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* 安装并配置可以通过命令行运行的 [Maven](http://maven.apache.org/download.cgi)，或者任何首选的 Java IDE
* 安装并配置 [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中使用的[示例应用程序](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart)是基本的控制台应用程序。 

使用 [git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

此命令会将存储库克隆到本地 git 文件夹。

项目导入完以后，请打开 **Quickstart.java**（位于 **src/main/java/quickstart** 中）。

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串
此解决方案要求将存储帐户名称和密钥安全地存储在运行示例的计算机的本地环境变量中。 根据操作系统按照下面的一个示例创建环境变量。

### <a name="for-linux"></a>对于 Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="for-windows"></a>对于 Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>运行示例

此示例在默认目录（Windows 用户为“AppData\Local\Temp”）中创建一个测试文件，然后提示你输入命令，以便将测试文件上传到 Blob 存储，列出容器中的 Blob，并下载具有新名称的已上传文件，以便比较旧文件和新文件。 

如果要在命令行使用 Maven 运行示例，请打开 shell 并导航到已克隆目录中的 **storage-blobs-java-v10-quickstart**。 然后输入 `mvn compile exec:java`。

如果打算在 Windows 上运行应用程序，以下是一个输出示例。

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

你可以控制示例，因此请输入命令，让其执行代码。 请注意，输入区分大小写。

还可以使用工具（如 [Azure 存储资源管理器](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)）查看 Blob 存储中的文件。 Azure 存储资源管理器是免费的跨平台工具，可用于访问存储帐户信息。 

验证文件后，请键入 **E** 并按 Enter，以便完成演示并删除测试文件。 了解此示例的用途以后，请打开 **Quickstart.java** 文件，查看代码。 

## <a name="understand-the-sample-code"></a>了解示例代码

接下来逐步介绍示例代码，以便展示其工作方式。

### <a name="get-references-to-the-storage-objects"></a>获取对存储对象的引用

首先创建对用于访问和管理 Blob 存储的对象的引用。 这些对象相互关联 - 每个对象被列表中的下一个对象使用。

* 创建指向存储帐户的 StorageURL 对象的实例。

    [**StorageURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) 对象是存储帐户的表示形式，可用于生成新的管道。 管道（[HTTP 管道](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline)）是一组策略，用于通过授权、日志记录和重试机制处理请求和响应。 可以使用管道创建 [**ServiceURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview) 对象的实例，以便创建 [**ContainerURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview) 的实例，这是在 Blob 容器上运行操作所必需的。

* 创建 ContainerURL 对象的实例，该对象代表所访问的容器。 容器用于组织 blob，就像使用计算机上的文件夹组织文件一样。

    **ContainerURL** 可以充当访问点来访问容器服务。 使用 **ContainerURL** 可创建 [**BlobURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview) 对象的实例，这是创建 Blob 所必需的。

* 创建 [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview) 对象的实例，该对象指向你感兴趣的特定 Blob。

> [!IMPORTANT]
> 容器名称必须为小写。 有关容器名称和 blob 名称的详细信息，请参阅[命名和引用容器、Blob 和元数据](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

### <a name="create-a-container"></a>创建容器 

在此部分，请创建 ContainerURL 的实例，并使用它创建一个新容器。 示例中的容器名为 **quickstart**。 

此示例使用 [containerURL.create](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview) ，因为我们想要每次运行示例时都创建新容器。 或者可以提前创建容器，这样就无需在代码中创建它。

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 最常用，此快速入门中也使用块 blob。 

为了将文件上传到 blob，需获取对目标容器中 blob 的引用。 有了 Blob 引用以后，即可将一个文件上传到其中，方法是在 BlockBlobURL 的实例中使用低级别 API，例如 [Upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview)（又称 PutBlob）、[StageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_)（又称 PutBLock），或者使用 [TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview) 类中提供的高级别 API，例如 [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview) 方法。 此操作会创建 blob（若尚不存在），或者覆盖它（若已存在）。

示例代码创建用于上传和下载的本地文件。 将要上传的文件存储为 **sourceFile**，并在 **blob** 中存储 Blob 的 URL。 以下示例将文件上传到名为“quickstart”的容器。

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

块 blob 可以是任何类型的文本或二进制文件。 页 blob 主要用于用于备份 IaaS VM 的 VHD 文件。 追加 Blob 用于将数据追加到末尾，通常用于日志记录。 存储在 Blob 存储中的大多数对象都是块 blob。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

可以使用 [containerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview) 获取容器中对象的列表。 此方法一次最多返回 5000 个对象，如果容器中还有更多需要列出的对象，则还会返回一个继续操作标记（后续操作标记）。 为此，我们创建一个帮助程序函数，只要在上一 listBlobsFlatSegment 响应中存在后续操作标记，该函数就会重复进行自身调用。

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>下载 Blob

使用 [blobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview) 将 Blob 下载到本地磁盘。

以下代码下载上一部分上传的 blob，对 blob 名称添加“_DOWNLOADED”后缀，以便可以在本地磁盘上看到两个文件。 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>清理资源

如果不再需要在本快速入门中上传的 Blob，可使用 [containerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview) 删除整个容器。 此方法也会删除容器中的文件。

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>后续步骤

在此快速入门教程中，介绍了如何使用 Java 在本地磁盘和 Azure Blob 存储之间传输文件。 

> [!div class="nextstepaction"]
> [用于 Java 的存储 SDK V10 源代码](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [API 参考](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview)
> [详细了解 RxJava](https://github.com/ReactiveX/RxJava)