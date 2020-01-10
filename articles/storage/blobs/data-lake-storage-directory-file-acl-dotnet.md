---
title: Azure Data Lake Storage Gen2 用于文件 & Acl 的 .NET SDK （预览版）
description: 使用 Azure 存储客户端库来管理已启用分层命名空间（HNS）的存储帐户中的目录和文件和目录访问控制列表（ACL）。
author: normesta
ms.service: storage
ms.date: 01/09/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 76fab93543310252bb9003029573f9d3f1ff62b6
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834999"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 .NET 管理 Azure Data Lake Storage Gen2 （预览版）中的目录、文件和 Acl

本文介绍如何使用 .NET 在已启用分层命名空间（HNS）的存储帐户中创建和管理目录、文件和权限。 

> [!IMPORTANT]
> 本文中所述的[DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) NuGet 包目前处于公共预览版中。

[包（NuGet）](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API 参考](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 到 Gen2 映射](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [提供反馈](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>必备组件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 已启用分层命名空间（HNS）的存储帐户。 按照[以下](data-lake-storage-quickstart-create-account.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

若要开始，请安装[DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 包。

有关如何安装 NuGet 包的详细信息，请参阅[使用 Nuget 包管理器在 Visual Studio 中安装和管理包](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)。

然后，将这些 using 语句添加到代码文件的顶部。

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码段，需要创建表示存储帐户的[DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)实例。 若要获取一个帐户，最简单的方法是使用帐户密钥。 

此示例使用帐户密钥创建[DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?)的实例。

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以通过调用[DataLakeServiceClient. CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)方法来创建一个。

此示例将创建一个名为 `my-file-system`的文件系统。 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>创建目录

通过调用[DataLakeFileSystemClient. CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)方法创建目录引用。

此示例将名为 `my-directory` 的目录添加到文件系统，然后添加名为 `my-subdirectory`的子目录。 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用[DataLakeDirectoryClient. RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync)方法重命名或移动目录。 传递所需目录的路径。 

此示例将子目录重命名为 `my-subdirectory-renamed`的名称。

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

此示例将名为 `my-subdirectory-renamed` 的目录移动到名为 `my-directory-2`的目录的子目录中。 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>删除目录

通过调用[DataLakeDirectoryClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)方法删除目录。

此示例删除名为 `my-directory`的目录。  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>管理目录 ACL

通过调用[DataLakeDirectoryClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync)方法获取目录的访问控制列表（ACL），并通过调用[DataLakeDirectoryClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)方法来设置 ACL。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory （Azure AD）来授予访问权限，请确保已向应用程序用于授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们的影响，请参阅[Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

此示例获取并设置名为 `my-directory`的目录的 ACL。 字符串 `user::rwx,group::r-x,other::rw-` 提供拥有的用户的 "读取"、"写入" 和 "执行" 权限，为拥有的组授予 "读取" 和 "执行" 权限，并为所有其他用户提供读取和写入权限。

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

首先，通过创建[DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient)类的实例在目标目录中创建文件引用。 通过调用[DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)方法上传文件。 请确保通过调用[DataLakeFileClient. FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)方法完成上传。

此示例将文本文件上传到名为 `my-directory`的目录。    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

## <a name="manage-a-file-acl"></a>管理文件 ACL

通过调用[DataLakeFileClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync)方法获取文件的访问控制列表（ACL），并通过调用[DataLakeFileClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)方法来设置 ACL。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory （Azure AD）来授予访问权限，请确保已向应用程序用于授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们的影响，请参阅[Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

此示例获取并设置名为 `my-file.txt`的文件的 ACL。 字符串 `user::rwx,group::r-x,other::rw-` 提供拥有的用户的 "读取"、"写入" 和 "执行" 权限，为拥有的组授予 "读取" 和 "执行" 权限，并为所有其他用户提供读取和写入权限。

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>从目录下载 

首先，创建表示要下载的文件的[DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient)实例。 使用[DataLakeFileClient. ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)方法，并分析返回值以获取[流](https://docs.microsoft.com/dotnet/api/system.io.stream)对象。 使用任何 .NET 文件处理 API 将字节从流保存到文件。 

此示例使用[BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader)和[FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream)将字节保存到文件。 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>列出目录内容

通过调用[FileSystemClient. GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync)方法并枚举结果来列出目录内容。

此示例将打印位于名为 `my-directory`的目录中的每个文件的名称。

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>另请参阅

* [API 参考文档](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [包（NuGet）](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供反馈](https://github.com/Azure/azure-sdk-for-net/issues)

