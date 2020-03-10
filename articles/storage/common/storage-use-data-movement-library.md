---
title: 通过用于 .NET 的数据移动库传输数据
titleSuffix: Azure Storage
description: 使用数据移动库将数据移动或复制到 blob 和文件内容。 从本地文件将数据复制到 Azure 存储，或者在存储帐户中或存储帐户之间复制数据。 轻松地将数据迁移到 Azure 存储。
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5b8654500fd697685b38e4f51ba1069e0cf6ccfc
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942903"
---
# <a name="transfer-data-with-the-data-movement-library"></a>通过数据移动库传输数据

Azure 存储数据移动库是一个跨平台的开源库，旨在高性能上载、下载和复制 blob 和文件。 数据移动库提供了在适用于 .NET 的 Azure 存储客户端库中不可用的便利方法。 这些方法可以设置并行操作的数量、跟踪传输进度、轻松恢复已取消的传输，等等。

此库还使用 .NET Core，这意味着，可以在构建适用于 Windows、Linux 和 macOS 的 .NET 应用时使用它。 若要了解有关 .NET Core 的详细信息，请参阅 [.NET Core 文档](https://dotnet.github.io/)。 此库也适用于面向 Windows 的传统 .NET Framework 应用。

本文档演示如何创建在 Windows、Linux 和 macOS 上运行的 .NET Core 控制台应用程序并执行以下方案：

- 将文件和目录上传到 Blob 存储。
- 定义传输数据时使用的并行操作数目。
- 跟踪数据传输进度。
- 恢复已取消的数据传输。
- 将文件从 URL 复制到 Blob 存储。
- 从 Blob 存储复制到 Blob 存储。

## <a name="prerequisites"></a>必备条件

- [Visual Studio Code](https://code.visualstudio.com/)
- 一个 [Azure 存储帐户](storage-account-create.md)

## <a name="setup"></a>设置

1. 访问 [.NET Core 安装指南](https://www.microsoft.com/net/core)安装 .NET Core。 选择环境时，请选择命令行选项。
2. 通过命令行创建项目的目录。 导航到此目录，并键入 `dotnet new console -o <sample-project-name>` 创建 C# 控制台项目。
3. 在 Visual Studio Code 中打开此目录。 通过在 Windows 中命令行上键入 `code .` 可快速完成此步骤。
4. 从 Visual Studio Code Marketplace 安装 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。 重新启动 Visual Studio Code。
5. 此时，应会出现两条提示。 其中一条提示指出要“添加所需的资产用于生成和调试。” 请单击“是”。 另一条提示指出要还原未解析的依赖项。 请单击“还原”。
6. 修改 `launch.json` 下的 `.vscode`，将外部终端用作控制台。 此设置应为 `"console": "externalTerminal"`
7. 可以使用 Visual Studio Code 调试 .NET Core 应用程序。 点击 `F5` 运行应用程序，并验证设置是否正常运行。 应会看到“Hello World!” 列显在控制台上。

## <a name="add-the-data-movement-library-to-your-project"></a>将数据移动库添加到项目

1. 将最新版本的数据移动库添加到 `<project-name>.csproj` 文件的 `dependencies` 部分。 在编写本文时，最新的版本是 `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. 此时应会显示一条提示，指出要还原项目。 请单击“还原”按钮。 也可以从命令行还原项目，在项目的根目录中键入 `dotnet restore` 命令即可。

修改 `<project-name>.csproj`：

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>设置应用程序的主干

第一项操作是设置应用程序的“主干”代码。 此代码提示我们输入存储帐户名和帐户密钥，并使用这些凭据创建 `CloudStorageAccount` 对象。 此对象用来与所有传输方案中的存储帐户交互。 该代码还会提示我们选择要执行的传输操作类型。

修改 `Program.cs`：

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>将本地文件上传到 blob

将 `GetSourcePath` 和 `GetBlob` 方法添加到 `Program.cs`：

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

修改 `TransferLocalFileToAzureBlob` 方法：

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

此代码提示我们输入本地文件的路径、新的或现有容器的名称，以及新 Blob 的名称。 `TransferManager.UploadAsync`  方法使用此信息执行上传。

点击 `F5` 运行应用程序。 可通过使用 [Microsoft Azure 存储资源管理器](https://storageexplorer.com/)查看存储帐户，来验证是否已发生上传。

## <a name="set-the-number-of-parallel-operations"></a>设置并行操作的数目

数据移动库提供的一项功能是设置并行操作数目，以提高数据传输吞吐量。 默认情况下，数据移动库将并行操作数设置为 8 * 计算机上的内核数。

请注意，在低带宽环境中，大量的并发操作可能会使网络连接变得紊乱，实质性地阻碍操作的全面完成。 需要试运行一下此设置，根据可用网络带宽确定哪种设置最合适。

让我们添加一些代码来设置并行操作数目。 此外，添加代码来计量完成传输所需的时间。

将 `SetNumberOfParallelOperations` 方法添加到 `Program.cs`：

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

将 `ExecuteChoice` 方法修改为使用 `SetNumberOfParallelOperations`：

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

将 `TransferLocalFileToAzureBlob` 方法修改为使用计时器：

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>跟踪传输进度

知道传输数据所需的时间非常有用。 但是，在传输操作*过程中*能够看到传输的进度就更好了。 若要实现此方案，需要创建一个 `TransferContext` 对象。 `TransferContext` 对象采用两种形式：`SingleTransferContext` 和 `DirectoryTransferContext`。 前者用于传输单个文件，后者用于传输文件的目录。

将 `GetSingleTransferContext` 和 `GetDirectoryTransferContext` 方法添加到 `Program.cs`：

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

将 `TransferLocalFileToAzureBlob` 方法修改为使用 `GetSingleTransferContext`：

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>恢复已取消的传输

数据移动库提供的另一个便利功能是恢复已取消的传输。 让我们添加一些代码，以便可以通过键入 `c` 暂时取消传输，在 3 秒后恢复传输。

修改 `TransferLocalFileToAzureBlob`：

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

到目前为止，`checkpoint` 值始终设置为 `null`。 现在，如果取消传输，则可以检索传输的最后一个检查点，并在传输上下文中使用这个新的检查点。

## <a name="transfer-a-local-directory-to-blob-storage"></a>将本地目录传输到 Blob 存储

如果数据移动库一次只能传输一个文件，则会让人失望。 幸运的是，它并不是这样。 数据移动库提供了传输文件目录及其所有子目录的能力。 让我们添加一些代码来执行此操作。

首先，将 `GetBlobDirectory` 方法添加到 `Program.cs`：

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

然后修改 `TransferLocalDirectoryToAzureBlobDirectory`：

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

此方法与用于上传单个文件的方法有几处区别。 现在，我们要使用 `TransferManager.UploadDirectoryAsync` 以及前面创建的 `getDirectoryTransferContext` 方法。 此外，要将 `options` 值提供给上传操作，指出我们要在上传内容中包含子目录。

## <a name="copy-a-file-from-url-to-a-blob"></a>将文件从 URL 复制到 blob

现在，让我们添加代码，将文件从 URL 复制到 Azure Blob。

修改 `TransferUrlToAzureBlob`：

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

此功能的一个重要用例是将数据从另一个云服务（例如 AWS）移到 Azure。 只要拥有一个可用于访问资源的 URL，就可以使用 `TransferManager.CopyAsync` 方法轻松将该资源移入 Azure Blob。 此方法还引入了一个新的布尔参数。 将此参数设置为 `true` 即表示我们想要执行异步服务器端复制。 将此参数设置为 `false` 即表示要执行同步复制 - 先将资源下载到本地计算机，然后将其上传到 Azure Blob。 但是，同步复制目前只可用于在两个不同的 Azure 存储资源之间复制。

## <a name="copy-a-blob"></a>复制 blob

数据移动库唯一提供的另一个功能是能够从一个 Azure 存储资源复制到另一个 Azure 存储资源。

修改 `TransferAzureBlobToAzureBlob`：

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

在本示例中，我们将 `TransferManager.CopyAsync` 中的布尔参数设置为 `false`，表示我们要执行同步复制。 这意味着，需要先将资源下载到本地计算机，然后将其上传到 Azure Blob。 同步复制选项能够很好地确保复制操作以一致的速度进行。 相比之下，异步服务器复制的速度取决于服务器上的可用网络带宽，而这种带宽可能会有波动。 不过，同步复制可能会产生额外的数据传出费用，而异步复制则不会。 在与源存储帐户所在的同一区域的 Azure VM 中，建议使用同步复制，避免产生数据传出费用。

数据移动应用程序现已完成。 [GitHub 上提供了完整的代码示例](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)。

## <a name="next-steps"></a>后续步骤

[Azure 存储数据移动库参考文档](https://azure.github.io/azure-storage-net-data-movement)。

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
