---
title: "如何通过 Java 使用 Azure Blob 存储（对象存储）| Microsoft Docs"
description: "使用 Azure Blob 存储（对象存储）将非结构化数据存储在云中。"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 4b243aa7a4e9aa3028259258292d1271867d28f4
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="how-to-use-blob-storage-from-java"></a>如何通过 Java 使用 Blob 存储
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>概述
Azure Blob 存储是一种将非结构化数据作为对象/Blob 存储在云中的服务。 Blob 存储可以存储任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。 Blob 存储也称为对象存储。

本文将演示如何使用 Microsoft Azure Blob 存储执行常见任务。 这些示例用 Java 编写并使用[用于 Java 的 Azure 存储 SDK][Azure Storage SDK for Java]。 涉及的任务包括**上传**、**列出**、**下载**和**删除**Blob。 有关 Blob 的详细信息，请参阅[后续步骤](#Next-Steps)部分。

> [!NOTE]
> SDK 提供给在 Android 设备上使用 Azure 存储的开发人员。 有关详细信息，请参阅[用于 Android 的 Azure 存储 SDK][Azure Storage SDK for Android]。
>
>

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>创建 Java 应用程序
在本文中，将使用存储功能，这些功能可在本地 Java 应用程序中运行，或在 Azure 的 Web 角色或辅助角色中通过运行的代码来运行。

为此，需要安装 Java 开发工具包 (JDK)，并在 Azure 订阅中创建一个 Azure 存储帐户。 完成此操作后，需要验证开发系统满足最低要求和 GitHub 上[用于 Java 的 Azure 存储 SDK][Azure Storage SDK for Java] 存储库中列出的依赖项。 如果系统满足这些要求，可以按照说明从该存储库将用于 Java 的 Azure 存储库下载并安装到你的系统中。 完成这些任务后，能够创建一个 Java 应用程序，以便使用本文中的示例。

## <a name="configure-your-application-to-access-blob-storage"></a>配置应用程序以访问 Blob 存储
将下列导入语句添加到要在其中使用 Azure 存储 API 以访问 Blob 的 Java 文件的顶部：

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串
Azure 存储客户端使用存储连接字符串来存储用于访问数据管理服务的终结点和凭据。 在客户端应用程序中运行时，必须提供以下格式的存储连接字符串，并对 AccountName 和 AccountKey 值使用 [Azure 门户](https://portal.azure.com)中列出的存储帐户的名称和存储帐户的主访问密钥。 下面的示例演示如何声明一个静态字段以保存连接字符串。

```java
// Define the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

在 Microsoft Azure 的角色内运行的应用程序中，此字符串可存储在服务配置文件 *ServiceConfiguration.cscfg* 中，并可通过调用 **RoleEnvironment.getConfigurationSettings** 方法进行访问。 下面的示例从服务配置文件中名为 StorageConnectionString 的 **Setting** 元素获取连接字符串。

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

下面的示例假定使用了这两个方法之一来获取存储连接字符串。

## <a name="create-a-container"></a>创建容器
利用 **CloudBlobClient** 对象，可以获得容器和 Blob 的引用对象。 以下代码将创建 **CloudBlobClient** 对象。

> [!NOTE]
> 还有其他方式来创建 **CloudStorageAccount** 对象；有关详细信息，请参阅 [Azure 存储客户端 SDK 参考]中的 **CloudStorageAccount**。
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

使用 **CloudBlobClient** 对象获取对要使用的容器的引用。 如果容器不存在，可使用 **createIfNotExists** 方法创建容器，否则将返回现有容器。 默认情况下，新容器是专用容器，因此必须指定存储访问密钥（如之前所做的那样）才能从该容器下载 Blob。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Get a reference to a container.
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist.
    container.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

### <a name="optional-configure-a-container-for-public-access"></a>可选：配置进行公共访问的容器
默认情况下，容器的权限已配置为允许进行私有访问，但你也可以轻松地将容器的权限配置为允许 Internet 上的用户进行公开的、只读的访问：

```java
// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-a-blob-into-a-container"></a>将 Blob 上传到容器中
要将文件上传到 Blob，请获取容器引用，并使用它获取 Blob 引用。 获取 Blob 引用后，可以通过对该 Blob 引用调用 upload 来上传任何数据流。 此操作将创建 Blob（如果该 Blob 不存在），或者覆盖它（如果该 Blob 存在）。 下面的代码示例演示了这一点，并假定已创建容器。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Define the path to a local file.
    final String filePath = "C:\\myimages\\myimage.jpg";

    // Create or overwrite the "myimage.jpg" blob with contents from a local file.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File(filePath);
    blob.upload(new FileInputStream(source), source.length());
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob
若要列出容器中的 Blob，请先获取容器引用，就像上传 Blob 时执行的操作一样。 可将容器的 **listBlobs** 方法用于 **for** 循环。 以下代码将容器中每个 Blob 的 URI 输出到控制台。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

请注意，可以命名 Blob，并在其名称中包含路径信息。 这会创建一个虚拟目录结构，可以像传统文件系统一样组织和遍历。 注意，该目录结构仅仅是虚拟的 - Blob 存储中唯一可用的资源是容器和 Blob。 但是，客户端库提供 **CloudBlobDirectory** 对象来引用虚拟目录，并简化了以此方式组织的 Blob 的使用过程。

例如，可以创建一个名为“photos”的容器，可以在其中上传名为“rootphoto1”、“2010/photo1”、“2010/photo2”和“2011/photo1”的 Blob。 这将在“photos”容器中创建虚拟目录“2010”和“2011”。 对“photos”容器调用 **listBlobs** 时，返回的集合将包含表示最高层所含目录和 Blob 的 **CloudBlobDirectory** 和 **CloudBlob** 对象。 在本例中，将返回目录“2010”和“2011”以及照片“rootphoto1”。 可使用 **instanceof** 运算符来区分这些对象。

还可以向 **listBlobs** 方法传入参数，并将 **useFlatBlobListing** 参数设置为 true。 这会导致返回每个 Blob，而无论目录如何。 有关详细信息，请参阅 [Azure 存储客户端 SDK 参考]中的 **CloudBlobContainer.listBlobs**。

## <a name="download-a-blob"></a>下载 Blob
若要下载 Blob，请执行之前用于上传 Blob 的相同步骤以获取 Blob 引用。 在上传示例中，Blob 对象调用了 upload。 在下面的示例中，调用 download 以将 Blob 内容传输到可用于将 Blob 保存到本地文件的流对象（如 **FileOutputStream**）。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop through each blob item in the container.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory.
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob"></a>删除 Blob
要删除 Blob，请获取 Blob 引用，并调用 **deleteIfExists**。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob.
    blob.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob-container"></a>删除 Blob 容器
最后，要删除 Blob 容器，请获取 Blob 容器引用，并调用 **deleteIfExists**。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container.
    container.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>后续步骤
现在，已了解有关 Blob 存储的基础知识，可单击下面的链接来了解更复杂的存储任务。

* [用于 Java 的 Azure 存储 SDK][Azure Storage SDK for Java]
* [Azure 存储客户端 SDK 参考][Azure 存储客户端 SDK 参考]
* [Azure 存储 REST API][Azure Storage REST API]
* [Azure 存储团队博客][Azure Storage Team Blog]

有关详细信息，另请参阅[面向 Java 开发人员的 Azure](/java/azure)。

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure 存储客户端 SDK 参考]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
