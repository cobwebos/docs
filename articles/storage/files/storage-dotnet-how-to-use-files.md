---
title: 使用 .NET 针对 Azure 文件进行开发 | Microsoft Docs
description: 了解如何开发使用 Azure 文件来存储文件数据的 .NET 应用程序和服务。
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 361ee5179b20d9488bb477a4e3c9fc0f0e6f266e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190650"
---
# <a name="develop-for-azure-files-with-net"></a>使用 .NET 针对 Azure 文件进行开发

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

本教程演示通过 .NET 开发应用程序的基础知识，此类应用程序使用 [Azure 文件](storage-files-introduction.md)来存储文件数据。 本教程创建一个简单的控制台应用程序，用于对 .NET 和 Azure 文件执行基本操作：

* 获取文件的内容。
* 设置文件共享的最大大小或*配额*。
* 为使用共享上定义的存储访问策略的文件创建共享访问签名（SAS 密钥）。
* 将文件复制到同一存储帐户中的另一个文件。
* 将文件复制到同一存储帐户中的一个 Blob。
* 使用 Azure 存储度量值进行故障排除。

若要了解有关 Azure 文件的详细信息，请参阅[什么是 Azure 文件？](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>了解 .NET API

Azure 文件为客户端应用程序提供两个主要方法：服务器消息块 (SMB) 和 REST。 在 .NET 中，`System.IO` 和 `WindowsAzure.Storage` Api 对这些方法进行了抽象。

API | 何时使用 | 注意
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | 应用程序： <ul><li>需要使用 SMB 读取/写入文件</li><li>是否在可以通过端口 445 访问 Azure 文件帐户的设备上运行</li><li>不需要管理文件共享的任何管理设置</li></ul> | 通过 SMB 通过 Azure 文件实现的文件 i/o 通常与任何网络文件共享或本地存储设备的 i/o 相同。 有关 .NET 中的许多功能（包括文件 i/o）的简介，请参阅[控制台应用程序](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)教程。
[Microsoft。](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | 应用程序： <ul><li>由于防火墙或 ISP 限制，无法使用端口445上的 SMB 访问 Azure 文件</li><li>需要管理功能，例如能够设置文件共享的配额或创建共享访问签名</li></ul> | 本文演示如何使用 REST 的 `Microsoft.Azure.Storage.File` 来代替文件共享的 SMB 和管理。

## <a name="create-the-console-application-and-obtain-the-assembly"></a>创建控制台应用程序，并获取程序集

在 Visual Studio 中创建新的 Windows 控制台应用程序。 以下步骤演示了如何在 Visual Studio 2019 中创建控制台应用程序。 在其他版本的 Visual Studio 中，这些步骤是类似的。

1. 启动 Visual Studio 并选择“创建新项目”。
1. 在 "**创建新项目**" 中，选择C#"**控制台应用（.NET Framework）** "，然后选择 "**下一步**"。
1. 在 "**配置新项目**" 中，输入应用的名称，然后选择 "**创建**"。

您可以将本教程中的所有代码示例添加到控制台应用程序的 `Program.cs` 文件的 `Main()` 方法。

你可以在任何类型的 .NET 应用程序中使用 Azure 存储客户端库。 这些类型包括 Azure 云服务或 web 应用，以及桌面和移动应用程序。 为简单起见，我们在本指南中使用控制台应用程序。

## <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 安装所需包

若要完成本教程，请参阅项目中的这些包：

* [适用于 .NET 的 Microsoft Azure 存储通用库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  此程序包提供对存储帐户中常见资源的编程访问。
* [适用于 .NET 的 Microsoft Azure 存储 Blob 库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  此包提供以编程方式访问存储帐户中的 blob 资源的权限。
* [Microsoft Azure 存储适用于 .NET 的文件库](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  此程序包提供对存储帐户中文件资源的编程访问。
* [适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  在应用程序运行时，此包提供用于分析配置文件中的连接字符串的类。

可以使用 NuGet 获取这两个包。 执行以下步骤:

1. 在**解决方案资源管理器**中，右键单击项目并选择 "**管理 NuGet 包**"。
1. 在**NuGet 包管理器**中，选择 "**浏览**"。 然后搜索并**选择 ""，然后选择 "** **安装**"。

   此步骤将安装包及其依赖项。
1. 搜索并安装以下包：

   * **Microsoft. Common**
   * **Microsoft。**
   * **ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>将存储帐户凭据保存到 App.config 文件

接下来，将你的凭据保存到项目的 `App.config` 文件中。 在**解决方案资源管理器**中，双击 `App.config` 并编辑文件，使其类似于下面的示例。 将 `myaccount` 替换为你的存储帐户名称，将 `mykey` 替换为你的存储帐户密钥。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> 最新版本的 Azure 存储模拟器不支持 Azure 文件。 连接字符串必须针对云中要使用 Azure 文件的 Azure 存储帐户。

## <a name="add-using-directives"></a>添加 using 指令

在**解决方案资源管理器**中，打开 `Program.cs` 文件，然后将以下 using 指令添加到文件顶部。

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>以编程方式访问文件共享

接下来，将以下内容添加到 `Main()` 方法，并在上面显示的代码后面检索连接字符串。 此代码将获取对我们先前创建的文件的引用并输出其内容。

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

运行控制台应用程序以查看输出。

## <a name="set-the-maximum-size-for-a-file-share"></a>设置文件共享的最大大小

从 Azure 存储客户端库的版本1.x 开始，可以设置文件共享的配额（最大大小）。 你还可以查看共享当前存储了多少数据。

设置共享配额会限制共享上存储的文件的总大小。 如果共享上文件的总大小超过在共享上设置的配额，则客户端不能增加现有文件的大小。 客户端无法创建新文件，除非这些文件是空的。

下面的示例演示如何检查共享的当前使用情况，以及如何设置共享的配额。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>为文件或文件共享生成共享访问签名

从 Azure 存储客户端库的 5.x 版开始，可以为文件共享或单个文件生成共享访问签名 (SAS)。 你还可以在文件共享上创建存储访问策略，以管理共享访问签名。 我们建议创建一个存储访问策略，因为它会使你能够在其遭到入侵时撤销 SAS。

以下示例在一个共享上创建一个存储访问策略。 该示例使用该策略为共享中的文件提供 SAS 约束。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

有关创建和使用共享访问签名的详细信息，请参阅[共享访问签名的工作原理](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)。

## <a name="copy-files"></a>复制文件

从 Azure 存储空间客户端库的 5.x 版开始，可以将一个文件复制到另一个文件，将一个文件复制到一个 Blob，或将一个 Blob 复制到一个文件。 在接下来的部分中，我们将演示如何以编程方式执行这些复制操作。

还可以使用 AzCopy 将一个文件复制到另一个文件，或将一个 blob 复制到一个文件或其他方法。 请参阅 [AzCopy 入门](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

> [!NOTE]
> 如果将一个 Blob 复制到一个文件，或将一个文件复制到一个 Blob，必须使用共享访问签名 (SAS) 授予对源对象的访问权限，即使是在同一存储帐户内进行复制。
>

### <a name="copy-a-file-to-another-file"></a>将一个文件复制到另一个文件

以下示例将一个文件复制到同一共享中的另一个文件。 由于此复制操作在同一存储帐户中的文件之间进行复制，因此可以使用共享密钥身份验证来进行复制。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>将一个文件复制到一个 Blob

以下示例创建一个文件并将其复制到同一存储帐户中的某个 blob。 该示例为源文件创建一个 SAS，服务在复制操作期间使用该 SAS 授予对源文件的访问权限。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

可以用相同的方式将一个 Blob 复制到一个文件。 如果源对象是一个 Blob，则创建一个 SAS，以便在复制操作期间授予对该 Blob 的访问权限。

## <a name="share-snapshots"></a>共享快照

从 Azure 存储客户端库的 8.5 版开始，可以创建共享快照。 还可以列出或浏览共享快照，以及删除共享快照。 共享快照的状态为只读，因此不允许对共享快照执行写入操作。

### <a name="create-share-snapshots"></a>创建共享快照

下面的示例创建文件共享快照。

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>列出共享快照

下面的示例列出共享上的共享快照。

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>浏览共享快照中的文件和目录

下面的示例浏览共享快照中的文件和目录。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>列出共享快照并共享快照，并从共享快照还原文件共享或文件

拍摄文件共享的快照即可在将来恢复单个文件或整个文件共享。

查询文件共享的共享快照即可从文件共享快照还原文件。 然后，你可以检索属于特定共享快照的文件。 使用该版本直接读取和比较或还原。

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>删除共享快照

下面的示例删除文件共享快照。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## 使用指标对 Azure 文件进行故障排除<a name="troubleshooting-azure-files-using-metrics"></a>

Azure 存储分析现在支持用于 Azure 文件的指标。 使用指标数据，可以跟踪请求和诊断问题。

可以从[Azure 门户](https://portal.azure.com)为 Azure 文件启用指标。 你还可以通过使用存储客户端库中的 REST API 或它的某个模拟调用 "设置文件服务属性" 操作，以编程方式启用指标。

以下代码示例演示如何使用适用于 .NET 的存储客户端库启用 Azure 文件的指标。

首先，将以下 `using` 指令添加到 `Program.cs` 文件，并将其添加到前面添加的指令：

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

尽管 Azure Blob、Azure 表和 Azure 队列使用 `Microsoft.Azure.Storage.Shared.Protocol` 命名空间中的共享 `ServiceProperties` 类型，但 Azure 文件使用其自己的类型，即 `Microsoft.Azure.Storage.File.Protocol` 命名空间中的 `FileServiceProperties` 类型。 但必须从代码中引用这两个命名空间，才能编译下面的代码。

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

如果遇到任何问题，可以参阅[排查 Windows 中的 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)。

## <a name="next-steps"></a>后续步骤

有关 Azure 文件的详细信息，请参阅以下资源：

### <a name="conceptual-articles-and-videos"></a>概念性文章和视频

* [Azure 文件：适用于 Windows 和 Linux 的顺畅的云 SMB 文件系统](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [将 Azure 文件与 Linux 配合使用](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>文件存储的工具支持

* [AzCopy 入门](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [将 Azure CLI 用于 Azure 存储](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [在 Windows 中排查 Azure 文件问题](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>参考

* [适用于 .NET 的 Azure 存储 Api](/dotnet/api/overview/azure/storage)
* [文件服务 REST API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>博客文章

* [Azure 文件存储现已正式发布](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure 文件存储内部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 文件服务简介](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [将连接保存到 Microsoft Azure 文件中](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
