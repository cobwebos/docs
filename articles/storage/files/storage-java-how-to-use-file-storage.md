---
title: 使用 Java 针对 Azure 文件进行开发 | Microsoft Docs
description: 了解如何开发使用 Azure 文件来存储文件数据的 Java 应用程序和服务。
services: storage
documentationcenter: java
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: a9585bc77a73cbd84fb2efa201a5745c62f3360a
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738194"
---
# <a name="develop-for-azure-files-with-java"></a>使用 Java 针对 Azure 文件进行开发
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>关于本教程
本教程将演示使用 Java 开发应用程序或服务的基础知识，这些应用程序或服务可以使用 Azure 文件来存储文件数据。 在本教程中，我们将创建一个控制台应用程序，并演示如何通过 Java 和 Azure 文件执行基本操作：

* 创建和删除 Azure 文件共享
* 创建和删除目录
* 枚举 Azure 文件共享中的文件和目录
* 上传、下载和删除文件

> [!Note]  
> 由于 Azure 文件可以通过 SMB 进行访问，因此可以编写应用程序，通过标准的 Java I/O 类来访问 Azure 文件共享。 本文介绍如何编写使用 Azure 存储 Java SDK 的应用程序，该 SDK 使用 [Azure 文件 REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) 与 Azure 文件通信。

## <a name="create-a-java-application"></a>创建 Java 应用程序
若要生成示例，需要 Java 开发工具包 (JDK) 和[用于 Java 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-java)。 此外，应该已经创建了一个 Azure 存储帐户。

## <a name="set-up-your-application-to-use-azure-files"></a>设置应用程序以使用 Azure 文件
要使用 Azure 存储 API，请将下列语句添加到要通过其来访问存储服务的 Java 文件的顶部：

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串
要使用 Azure 文件，需要连接到 Azure 存储帐户。 第一步是配置连接字符串，我们会使用该字符串连接到存储帐户。 为此，我们需要定义一个静态变量。

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> 将 your_storage_account_name 和 your_storage_account_key 替换为存储帐户的实际值。
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>连接到 Azure 存储帐户
要连接到存储帐户，则需要使用 **CloudStorageAccount** 对象，以便将连接字符串传递到 **parse** 方法。

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** 会引发 InvalidKeyException，因此需将其置于 try/catch 块内。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
Azure 文件中的所有文件和目录都位于名为 Share 的容器内。 存储帐户可以拥有无数的共享，只要帐户容量允许。 要获得共享及其内容的访问权限，需要使用 Azure 文件客户端。

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

使用 Azure 文件客户端可以获取对共享的引用。

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

实际创建共享时，请使用 CloudFileShare 对象的 **createIfNotExists**方法。

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

而在目前，**share** 保留对名为 **sampleshare** 的共享的引用。

## <a name="delete-an-azure-file-share"></a>删除 Azure 文件共享
删除共享时，可针对 CloudFileShare 对象调用 **deleteIfExists** 方法。 以下是具有此类功能的示例代码。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>创建目录
也可以将文件置于子目录中，不必将其全部置于根目录中，以便对存储进行有效的组织。 使用 Azure 文件可以创建帐户允许的任意数目的目录。 以下代码会在根目录下创建名为 **sampledir** 的子目录。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>删除目录
删除目录很简单，但需注意的是，不能删除仍然包含文件或其他目录的目录。

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>枚举 Azure 文件共享中的文件和目录
可以轻松获取共享中文件和目录的列表，只需针对 CloudFileDirectory 引用调用 **listFilesAndDirectories** 即可。 该方法将返回可以对其进行循环访问的 ListFileItem 对象的列表。 例如，下面的代码将列出根目录中的文件和目录。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>上传文件
在本部分，学习如何将文件从本地存储上传到共享所在的根目录。

上传文件的第一步是获取对文件所在的目录的引用。 为此，需要调用共享对象的 **getRootDirectoryReference** 方法。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

现在，你已经有了共享所在的根目录的引用，因此可以使用以下代码来上传文件。

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>下载文件
对于 Azure 文件，需要更频繁执行的一项操作是下载文件。 在下面的示例中，代码会下载 SampleFile.txt 并显示其内容。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>删除文件
另一项常见的 Azure 文件操作是删除文件。 下面的代码会删除名为 SampleFile.txt 的文件，该文件存储在名为 **sampledir** 的目录中。

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>后续步骤
如果还想更多地了解其他 Azure 存储 API，请点击以下链接。

* [面向 Java 开发人员的 Azure](/java/azure)
* [用于 Java 的 Azure 存储 SDK](https://github.com/azure/azure-storage-java)
* [用于 Android 的 Azure 存储 SDK](https://github.com/azure/azure-storage-android)
* [Azure 存储客户端 SDK 参考](http://dl.windowsazure.com/storage/javadoc/)
* [Azure 存储空间服务 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage/)
* [使用 AzCopy 命令行实用程序传输数据](../common/storage-use-azcopy.md)
* [排查 Azure 文件问题 - Windows](storage-troubleshoot-windows-file-connection-problems.md)