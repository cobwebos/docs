---
title: Azure Data Lake Storage Gen2 中的 & Acl 使用 Java （预览）
description: 使用用于 Java 的 Azure 存储库管理已启用分层命名空间（HNS）的存储帐户中的目录和文件和目录访问控制列表（ACL）。
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 4593c16905c331b0d76c9c5c93faac6accfb0977
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485206"
---
# <a name="use-java-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2 中的 & Acl 使用 Java （预览）

本文介绍如何使用 Java 创建和管理已启用分层命名空间（HNS）的存储帐户中的目录、文件和权限。 

> [!IMPORTANT]
> 本文中所述的 Java 库当前为公共预览版。

[包（Maven）](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake/12.0.0-preview.6/jar) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html) | [Gen1 到 Gen2 映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 已启用分层命名空间（HNS）的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

若要开始，请在文本编辑器中打开*pom .xml*文件。 将以下依赖项元素添加到依赖项组。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-file-datalake</artifactId>
  <version>12.0.0-preview.6</version>
</dependency>
```

然后，将这些 imports 语句添加到代码文件中。

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>连接到帐户 

若要使用本文中的代码段，需要创建表示存储帐户的**DataLakeServiceClient**实例。 若要获取一个帐户，最简单的方法是使用帐户密钥。 

此示例使用帐户密钥创建**DataLakeServiceClient**的实例。

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      

```
## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以通过调用**DataLakeServiceClient. createFileSystem**方法来创建一个。

此示例将创建一个名为 `my-file-system`的文件系统。 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>创建目录

通过调用**DataLakeFileSystemClient. createDirectory**方法创建目录引用。

此示例将名为 `my-directory` 的目录添加到文件系统，然后添加名为 `my-subdirectory`的子目录。 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用**DataLakeDirectoryClient**方法重命名或移动目录。 传递所需目录的路径。 

此示例将子目录重命名为 `my-subdirectory-renamed`的名称。

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename("my-directory/my-subdirectory-renamed");
}
```

此示例将名为 `my-subdirectory-renamed` 的目录移动到名为 `my-directory-2`的目录的子目录中。 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>删除目录

通过调用**DataLakeDirectoryClient. deleteWithResponse**方法删除目录。

此示例删除名为 `my-directory`的目录。   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>管理目录 ACL

此示例获取并设置名为 `my-directory`的目录的 ACL。 此示例为拥有用户授予 "读取"、"写入" 和 "执行" 权限，为拥有的组提供 "读取" 和 "执行" 权限，并为其他所有用户提供读取访问权限。

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    PathPermissions permissions = new PathPermissions()

      .group(new RolePermissions().execute(true).read(true))
      .owner(new RolePermissions().execute(true).read(true).write(true))
      .other(new RolePermissions().read(true));

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

首先，通过创建**DataLakeFileClient**类的实例在目标目录中创建文件引用。 通过调用**DataLakeFileClient**方法上传文件。 请确保通过调用**DataLakeFileClient. FlushAsync**方法完成上传。

此示例将文本文件上传到名为 `my-directory`的目录。 "

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

## <a name="manage-a-file-acl"></a>管理文件 ACL

此示例获取并设置名为 `upload-file.txt`的文件的 ACL。 此示例为拥有用户授予 "读取"、"写入" 和 "执行" 权限，为拥有的组提供 "读取" 和 "执行" 权限，并为其他所有用户提供读取访问权限。

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    PathPermissions permissions = new PathPermissions()

        .group(new RolePermissions().execute(true).read(true))
        .owner(new RolePermissions().execute(true).read(true).write(true))
        .other(new RolePermissions().read(false));

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>从目录下载

首先，创建表示要下载的文件的**DataLakeFileClient**实例。 使用**DataLakeFileClient**方法读取该文件。 使用任何 .NET 文件处理 API 将字节从流保存到文件。 

请确保通过调用**DataLakeFileClient**方法完成下载。

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();

    fileClient.flush(file.length());
        
}

```

## <a name="list-directory-contents"></a>列出目录内容

此示例将打印位于名为 `my-directory`的目录中的每个文件的名称。

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>请参阅

* [API 参考文档](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html)
* [包（Maven）](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake/12.0.0-preview.6/jar)
* [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知功能缺口](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)