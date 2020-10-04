---
title: 使用 .NET 针对 Azure 文件进行开发 | Microsoft Docs
description: 了解如何开发使用 Azure 文件存储数据的 .NET 应用程序和服务。
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: fbfc347e1b514f9f59e2f238d2b1bfbaf59f2172
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710696"
---
# <a name="develop-for-azure-files-with-net"></a>使用 .NET 针对 Azure 文件进行开发

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

了解开发使用 [Azure 文件](storage-files-introduction.md) 存储数据的 .net 应用程序的基础知识。 本文介绍如何创建一个简单的控制台应用程序，以便通过 .NET 和 Azure 文件执行以下操作：

- 获取文件内容。
- 设置文件共享的最大大小或配额。
-  (SAS) 为文件创建共享访问签名。
- 将文件复制到同一存储帐户中的另一个文件。
- 将文件复制到同一存储帐户中的一个 Blob。
- 创建文件共享的快照。
- 从共享快照还原文件。
- 使用 Azure 存储度量值进行故障排除。

若要了解有关 Azure 文件存储的详细信息，请参阅[什么是 Azure 文件存储？](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>了解 .NET API

Azure 文件为客户端应用程序提供两个主要方法：服务器消息块 (SMB) 和 REST。 在 .NET 中，`System.IO` 和 `Azure.Storage.Files.Shares` API 将抽象化这些方法。

API | 何时使用 | 说明
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | 应用程序： <ul><li>需要使用 SMB 读取/写入文件</li><li>是否在可以通过端口 445 访问 Azure 文件帐户的设备上运行</li><li>不需要管理文件共享的任何管理设置</li></ul> | 一般情况下，通过 SMB 使用 Azure 文件存储实现的文件 I/O 与使用任何网络文件共享或本地存储设备实现的 I/O 相同。 有关 .NET 中的许多功能（包括文件 I/O）的简介，请参阅[控制台应用程序](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)教程。
[Azure。文件共享](/dotnet/api/azure.storage.files.shares) | 应用程序： <ul><li>由于防火墙或 ISP 约束，无法使用 SMB 在端口 445 上访问 Azure 文件存储</li><li>需要管理功能，例如能够设置文件共享的配额或创建共享访问签名</li></ul> | 本文演示如何通过 REST（而不是 SMB）将 `Azure.Storage.Files.Shares` 用于文件 I/O 以及如何管理文件共享。

## <a name="create-the-console-application-and-obtain-the-assembly"></a>创建控制台应用程序，并获取程序集

你可以在任何类型的 .NET 应用中使用 Azure 文件客户端库。 这些应用包括 Azure 云、web、桌面和移动应用。 在本指南中，我们将创建一个控制台应用程序来简化。

在 Visual Studio 中创建新的 Windows 控制台应用程序。 以下步骤演示了如何在 Visual Studio 2019 中创建控制台应用程序。 在其他版本的 Visual Studio 中，这些步骤是类似的。

1. 启动 Visual Studio 并选择“创建新项目”。 
1. 在“创建新项目”  中，选择用于 C# 的“控制台应用(.NET Framework)”  ，然后选择“下一步”  。
1. 在“配置新项目”中输入应用的名称，然后选择“创建”。  

将本文中的所有代码示例添加到 `Program` *Program.cs* 文件中的类。

## <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 安装所需包

请参阅项目中的这些包：

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [适用于 .net 的 Azure 核心库](https://www.nuget.org/packages/Azure.Core/)：此包是 azure 客户端管道的实现。
- [Azure 存储 Blob 适用于 .net 的客户端库](https://www.nuget.org/packages/Azure.Storage.Blobs/)：此包提供以编程方式访问存储帐户中的 Blob 资源的权限。
- [适用于 .net 的 Azure 存储空间客户端库](https://www.nuget.org/packages/Azure.Storage.Files.Shares/)：此包提供以编程方式访问存储帐户中的文件资源的权限。
- [适用于 .net 的系统 Configuration Manager 库](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)：此包提供了一个类，用于在配置文件中存储和检索值。

可以使用 NuGet 获取包。 执行以下步骤:

1. 在“解决方案资源管理器”中，右键单击你的项目并选择“管理 NuGet 包”   。
1. 在“NuGet 包管理器”中选择“浏览”。   然后搜索并选择 " **Azure Core**"，并选择 " **安装**"。

   此步骤将安装该包及其依赖项。

1. 搜索并安装以下包：

   - **Azure. Blob**
   - **Azure。文件共享**
   - **System.Configuration.ConfigurationManager**

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [适用于 .net 的 Microsoft Azure 存储通用库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)：此包提供对存储帐户中的常见资源的编程访问。
- [适用于 .net 的 Microsoft Azure 存储 blob 库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)：此包提供以编程方式访问存储帐户中的 blob 资源的权限。
- [Microsoft Azure 存储适用于 .net 的文件库](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)：此包提供以编程方式访问存储帐户中的文件资源的权限。
- [Microsoft Azure 适用于 .net Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)：此包提供用于在应用程序运行的任何位置分析配置文件中的连接字符串的类。

可以使用 NuGet 获取包。 执行以下步骤:

1. 在“解决方案资源管理器”中，右键单击你的项目并选择“管理 NuGet 包”   。
1. 在“NuGet 包管理器”中选择“浏览”。   搜索并选择“Microsoft.Azure.Storage.Blob”，然后选择“安装”。  

   此步骤将安装该包及其依赖项。
1. 搜索并安装以下包：

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>将存储帐户凭据保存到 App.config 文件

接下来，将你的凭据保存到项目的 *App.config* 文件中。 在“解决方案资源管理器”中，双击 `App.config` 并编辑该文件，使其类似于以下示例。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

请将 `myaccount` 替换为你的存储帐户名，将 `mykey` 替换为你的存储帐户密钥。

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

请将 `myaccount` 替换为你的存储帐户名，将 `StorageAccountKeyEndingIn==` 替换为你的存储帐户密钥。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Azurite 存储模拟器目前不支持 Azure 文件。 连接字符串必须针对云中的 Azure 存储帐户，才能使用 Azure 文件。

## <a name="add-using-directives"></a>添加 using 指令

在 **解决方案资源管理器**中，打开 *Program.cs* 文件，并将以下 using 指令添加到文件顶部。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>以编程方式访问文件共享

在 *Program.cs* 文件中，添加以下代码以编程方式访问文件共享。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

如果尚不存在，则以下方法将创建文件共享。 方法首先从连接字符串创建 [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) 对象。 然后，该示例会尝试下载我们先前创建的文件。 从调用此方法 `Main()` 。

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

接下来，将以下内容添加到 `Main()` 方法（在上面显示的代码后面）以检索连接字符串。 此代码会获取我们先前创建的文件的引用，并输出其内容。

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

---

## <a name="set-the-maximum-size-for-a-file-share"></a>设置文件共享的最大大小

从 Azure 文件客户端库的版本1.x 开始，可以设置文件共享的配额 (最大大小) 。 还可以查看共享当前存储了多少数据。

设置共享配额可以限制在该共享上存储的文件的总大小。 如果共享上文件的总大小超过配额，则客户端不能增加现有文件的大小。 客户端也无法创建新文件，除非这些文件是空的。

下面的示例演示如何检查共享的当前使用情况，以及如何设置共享的配额。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>为文件或文件共享生成共享访问签名

从 Azure 文件客户端库的版本1.x 开始，你可以为文件共享或单个文件生成共享访问签名 (SAS) 。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

下面的示例方法返回指定共享中文件的 SAS。

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

还可以在文件共享上创建一个存储访问策略以管理共享访问签名。 我们建议创建一个存储访问策略，因为它可以在 SAS 泄密时让你撤销 SAS。 以下示例在共享上创建一个存储访问策略。 该示例使用该策略为共享中的文件提供 SAS 约束。

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

---

若要详细了解如何创建和使用共享访问签名，请参阅[共享访问签名的工作原理](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)。

## <a name="copy-files"></a>复制文件

从 Azure 文件客户端库的版本1.x 开始，可以将一个文件复制到另一个文件，将一个文件复制到一个 blob，或将一个 blob 复制到一个文件中。

还可以使用 AzCopy 将一个文件复制到另一个文件或将一个 Blob 复制到一个文件，反之亦然。 请参阅 [AzCopy 入门](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

> [!NOTE]
> 如果将一个 Blob 复制到一个文件，或将一个文件复制到一个 Blob，必须使用共享访问签名 (SAS) 授予对源对象的访问权限，即使是在同一存储帐户内进行复制。

### <a name="copy-a-file-to-another-file"></a>将一个文件复制到另一个文件

以下示例将一个文件复制到同一共享中的另一个文件。 你可以使用 [共享密钥身份验证](/rest/api/storageservices/authorize-with-shared-key) 来进行复制，因为此操作会在同一存储帐户内复制文件。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

### <a name="copy-a-file-to-a-blob"></a>将文件复制到 Blob

以下示例创建一个文件并将其复制到同一存储帐户中的某个 blob。 该示例为源文件创建一个 SAS，服务在复制操作期间使用该 SAS 授予对源文件的访问权限。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

可以用相同的方式将一个 Blob 复制到一个文件。 如果源对象是一个 Blob，则创建一个 SAS，以便在复制操作期间授予对该 Blob 的访问权限。

## <a name="share-snapshots"></a>共享快照

从 Azure 文件客户端库版本8.5 开始，你可以创建共享快照。 还可以列出或浏览共享快照，以及删除共享快照。 创建后，共享快照是只读的。

### <a name="create-share-snapshots"></a>创建共享快照

下面的示例创建文件共享快照。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>列出共享快照

下面的示例列出了共享上的快照。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>列出共享快照中的文件和目录

以下示例浏览共享快照中的文件和目录。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>从共享快照还原文件共享或文件

拍摄文件共享的快照可恢复单个文件或整个文件共享。

查询文件共享的共享快照即可从文件共享快照还原文件。 然后，可以检索属于特定共享快照的文件。 使用该版本直接读取或还原文件。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

### <a name="delete-share-snapshots"></a>删除共享快照

下面的示例删除文件共享快照。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>使用指标排查 Azure 文件存储问题<a name="troubleshooting-azure-files-using-metrics"></a>

Azure 存储分析支持 Azure 文件的指标。 使用指标数据，可以跟踪请求和诊断问题。

可以从 [Azure 门户](https://portal.azure.com)为 Azure 文件启用指标。 你还可以通过 REST API 使用 "设置文件服务属性" 操作来通过调用 " [设置文件服务属性](/rest/api/storageservices/set-file-service-properties) " 操作来以编程方式启用指标。

下面的代码示例演示如何使用 .NET 客户端库启用 Azure 文件的指标。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

首先，将以下 `using` 指令添加到 *Program.cs* 文件，并将其添加到前面添加的指令：

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

尽管 Azure Blob、Azure 表和 Azure 队列使用 `Microsoft.Azure.Storage.Shared.Protocol` 命名空间中的共享 `ServiceProperties` 类型，但 Azure 文件存储使用其自身的类型，即 `Microsoft.Azure.Storage.File.Protocol` 命名空间中的 `FileServiceProperties` 类型。 但是，必须在代码中引用这两个命名空间才能编译后续代码。

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

---

如果遇到任何问题，可以参阅[在 Windows 中排查 Azure 文件存储问题](storage-troubleshoot-windows-file-connection-problems.md)。

## <a name="next-steps"></a>后续步骤

有关 Azure 文件存储的详细信息，请参阅以下资源：

### <a name="conceptual-articles-and-videos"></a>概念性文章和视频

- [Azure 文件：适用于 Windows 和 Linux 的顺畅的云 SMB 文件系统](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [在 Linux 中使用 Azure 文件存储](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>文件存储的工具支持

- [AzCopy 入门](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [在 Windows 中排查 Azure 文件问题](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>参考

- [适用于 .NET 的 Azure 存储 API](/dotnet/api/overview/azure/storage)
- [文件服务 REST API](/rest/api/storageservices/File-Service-REST-API)
