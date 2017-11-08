---
title: "Azure 快速入门 - 使用 Java 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象 | Microsoft Docs"
description: "快速了解如何使用 Java 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 0ed27077f649df2d0673351cda708b9a358ff92b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>使用 Java 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象

本快速入门介绍如何使用 Java 上传、下载和列出 Azure Blob 存储的容器中的块 blob。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* 安装一个具有 Maven 集成的 IDE，或者安装 Maven 并将其配置为从命令行工作
    * 本教程使用具有“适用于 Java 开发者的 Eclipse IDE”配置的 [Eclipse](http://www.eclipse.org/downloads/)
    


如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-storage-account-using-the-azure-portal"></a>使用 Azure 门户创建存储帐户

首先，创建用于本快速入门的新通用存储帐户。 

1. 转到 [Azure 门户](https://portal.azure.com)，使用 Azure 帐户登录。 
2. 在“中心”菜单上，选择“新建” > “存储” > “存储帐户 - blob、文件、表、队列”。 
3. 输入存储帐户的名称。 名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 不能与其他名称重复。
4. 将 `Deployment model` 设置为“资源管理器”。
5. 将“`Account kind`”设置为“常规用途”。
6. 将“`Performance`”设置为“标准”。 
7. 将“`Replication`”设置为“本地冗余存储(LRS)”。
8. 将“`Storage service encryption`”设置为“禁用”。
9. 将“`Secure transfer required`”设置为“禁用”。
10. 选择订阅。 
11. 对于“`resource group`”，创建一个新资源组，并为其赋予唯一名称。 
12. 选择要用于存储帐户的“`Location`”。
13. 选中“固定到仪表板”，然后单击“创建”，创建存储帐户。 

创建存储帐户后，它被固定到仪表板。 单击可将其打开。 在“设置”下，单击“访问密钥”。 选择一个密钥并将其复制到剪贴板，然后再粘贴到文本编辑器中，以供将来使用。 此外，复制存储帐户的名称，并将其粘贴到文本编辑器中，供将来使用。

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中使用的[示例应用程序](https://github.com/Azure-Samples/storage-blobs-java-quickstart)是基本的控制台应用程序。 

使用 [git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

此命令会将存储库克隆到本地 git 文件夹。 要打开项目，请启动 Eclipse 并关闭欢迎屏幕。 选择“文件”，然后选择“从文件系统打开项目...”。请确保已选中“检测并配置项目性质”。 选择“目录”，然后导航到存储克隆存储库的位置，在该位置中选择 javaBlobsQuickstart 文件夹。 请确保 javaBlobsQuickstarts 项目显示为 Eclipse 项目，然后选择“完成”。

完成项目导入后，打开 AzureApp.java（位于 src/main/java 内的 blobQuickstart.blobAzureApp 中），并替换 `storageConnectionString` 字符串中的 `accountname` 和 `accountkey`。 然后运行应用程序。
     

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串
    
在应用程序中，必须为存储帐户提供连接字符串。 打开 AzureApp.Java 文件。 查找 `storageConnectionString` 变量。 将连接字符串中的 `AccountName` 和 `AccountKey` 值替换为从 Azure 门户保存的值。 `storageConnectionString` 应如下图所示：

```java
    public static final String storageConnectionString ="DefaultEndpointsProtocol=https;" +
     "AccountName=<Namehere>;" +
    "AccountKey=<Keyhere>";
```

## <a name="run-the-sample"></a>运行示例

此示例在默认目录（对于 windows 用户，为“我的文档”）中创建一个测试文件，将其上传到 Blob 存储，列出容器中的 blob，然后下载具有新名称的文件，以便比较旧文件和新文件。 

通过在 Eclipse 中按“Ctrl+F11”来运行示例。

如果要在命令行使用 Maven 运行示例，请打开 shell 并导航到克隆目录中的 blobAzureApp。 然后输入 `mvn compile exec:java`。

如果打算在 Windows 上运行应用程序，以下是一个输出示例。

```
Location of file: C:\Users\<user>\Documents\results.txt
File has been written
URI of blob is: http://myexamplesacct.blob.core.windows.net/quickstartblobs/results.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

 检查这两个文件的默认目录（对于 windows 用户，为“我的文档”），再继续操作。 可以打开它们，并看到它们完全相同。 从控制台窗口复制 blob 的 URL，将其粘贴到浏览器，查看 Blob 存储中的文件的内容。 按 Enter 时，会删除存储容器和文件。

还可以使用工具（如 [Azure 存储资源管理器](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)）查看 Blob 存储中的文件。 Azure 存储资源管理器是免费的跨平台工具，可用于访问存储帐户信息。 

验证文件后，按 Enter 键可完成演示并删除测试文件。 现在已了解此示例的用途，打开 AzureApp.java 文件可查看代码。 

## <a name="get-references-to-the-storage-objects"></a>获取对存储对象的引用

首先创建对用于访问和管理 Blob 存储的对象的引用。 这些对象相互关联 - 每个对象被列表中的下一个对象使用。

* 创建指向[存储帐户](/java/api/com.microsoft.azure.management.storage._storage_account)的 CloudStorageAccount 对象的实例。 

* 创建 CloudBlobClient 对象的实例，该对象指向存储帐户中的 [Blob 服务](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client)。 

* 创建 CloudBlobContainer 对象的实例，该对象代表所访问的[容器](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container)。 容器用于组织 blob，就像使用计算机上的文件夹组织文件一样。

有了 CloudBlobContainer 后，就可以创建 CloudBlockBlob 对象（该对象指向你感兴趣的特定 [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob)）的实例，然后执行上传、下载、复制等操作。

在本部分中，将创建对象的实例、创建新容器，并对容器设置权限，使 blob 公开，只需 URL 即可对其进行访问。 容器名称为 quickstartblobs。 

此示例使用 CreateIfNotExists，因为我们想要每次运行示例时都创建新容器。 在整个应用程序中使用相同容器的生产环境中，建议仅调用 CreateIfNotExists 一次。 或者可以提前创建容器，这样就无需在代码中创建它。

```java
CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
        
CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

// Get a reference to a container.
// The container name must be lower case
CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

// Create the container if it does not exist.
container.createIfNotExists();

// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 最常用，此快速入门中也使用块 blob。 

为了将文件上传到 blob，需获取对目标容器中 blob 的引用。 有了 blob 引用后，可以通过使用 [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long) 将数据上传到其中。 此操作会创建 blob（若尚不存在），或者覆盖它（若已存在）。

示例代码创建一个用于上传和下载的本地文件，存储作为“源”上传的文件和 blob 中的 blob 名称。 以下示例将文件上传到名为“quickstartblobs”的容器。

```java
//Getting the path to user's myDocuments folder
String myDocs = FileSystemView.getFileSystemView().getDefaultDirectory().getPath();

//Creating a file in the myDocuments folder
File source = new File(myDocs + File.separator + "results.txt");
try( Writer output = new BufferedWriter(new FileWriter(source)))
{
    System.out.println("Location of file: " + source.toString());

    output.write("Hello Azure!");
    output.close();
    
    System.out.println("File has been written");
}
catch(IOException x) 
{
    System.err.println(x);
}

//Getting blob reference
CloudBlockBlob blob = container.getBlockBlobReference("results.txt");

//Creating a FileInputStream as it is necessary for the upload
FileInputStream myFile = new FileInputStream(source);

//Creating blob and uploading file to it
blob.upload( myFile, source.length());

//Closing FileInputStream
myFile.close();
```

Blob 存储支持多种上传方法。 例如，如果有内存流，可以使用 UploadFromStreamAsync 方法，无需使用 UploadFromFileAsync。 

块 blob 可以是任何类型的文本或二进制文件。 页 blob 主要用于用于备份 IaaS VM 的 VHD 文件。 追加 blob 用于日志记录，例如有时需要写入到文件，再继续添加更多信息。 存储在 Blob 存储中的大多数对象都是块 blob。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

可以使用 [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs) 获取容器中的文件列表。 下面的代码检索 blob 列表，然后循环访问它们，显示找到的 blob 的 URI。 可以从命令窗口中复制 URI，然后将其粘贴到浏览器以查看文件。

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

## <a name="download-blobs"></a>下载 Blob

使用 [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String) 将 blob 下载到本地磁盘。

以下代码下载上一部分上传的 blob，对 blob 名称添加“_DOWNLOADED”后缀，以便可以在本地磁盘上看到两个文件。 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in your default directory.
blob.downloadToFile(myDocs + File.separator + "results_DOWNLOADED.txt");
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要在此快速入门中上传的 blob，可使用 [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists) 删除整个容器。 此操作也会删除容器中的文件。

```java
//Deletes container if it exists then deletes files created locally
container.deleteIfExists();
downloadedFile.deleteOnExit();
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>后续步骤

在此快速入门教程中，介绍了如何使用 Java 在本地磁盘和 Azure Blob 存储之间传输文件。 要深入了解如何使用 Blob 存储，请继续学习 Blob 存储操作说明。

> [!div class="nextstepaction"]
> [Blob 存储操作说明](storage-java-how-to-use-blob-storage.md)

若要详细了解存储资源管理器和 Blob，请参阅[使用存储资源管理器管理 Azure Blob 存储资源](../../vs-azure-tools-storage-explorer-blobs.md)。