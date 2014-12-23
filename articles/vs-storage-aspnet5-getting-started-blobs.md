<properties title="Getting Started with Azure Storage" pageTitle="Azure 存储入门" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [入门](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [发生了什么情况](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Azure 存储入门（ASP.NET 5 项目）

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [队列](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [表](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Azure Blob 存储是一项可存储大量非结构化数据的服务，用户可在世界任何地方通过 HTTP 或 HTTPS 访问这些数据。单个 Blob 可以是任意大小。Blob 可以是图像、音频和视频文件、原始数据以及文档文件等。

若要开始使用，你需要创建一个 Azure 存储帐户，然后在存储中创建一个或多个容器。例如，您可以将存储命名为"Scrapbook"，然后在该存储中创建名为"图像"的容器以存储图片，然后创建名为"音频"的容器以存储音频文件。创建这些容器后，您可以向它们上载单独的 Blob 文件。有关以编程方式操纵 Blob 的详细信息，请参阅[如何通过 .NET 使用 Blob 存储](http://windowsazure.cn/zh-cn/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET")。

若要以编程方式访问 ASP.NET 5 项目中的 Blob，你需要添加以下项（如果尚未存在）。

1. 在您希望以编程方式访问 Azure 存储的任何 C# 文件中，将以下代码命名空间声明添加到文件的顶部。

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. 使用以下代码获取配置设置。

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####获取存储连接字符串
在你可以使用 Blob 执行任何操作之前，你需要先获取将存放 Blob 的存储帐户的连接字符串。你可以使用 **CloudStorageAccount** 类型来表示你的存储帐户信息。如果你使用的是 ASP.NET 5 项目，则可以调用 Configuration 对象的 get 方法从 Azure 服务配置获取存储连接字符串和存储帐户信息，如以下代码所示。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####创建容器
正如文件位于文件夹中一样，存储 Blob 位于容器中。你可以使用 **CloudBlobClient** 对象引用现有容器，也可以调用 CreateCloudBlobClient() 方法创建一个新容器。

以下代码将演示如何新建一个 Blob 存储容器。该代码将首先创建一个 **BlobClient** 对象，以便您可以访问该对象的功能，例如创建一个存储容器。然后，该代码将尝试引用名为"mycontainer"的存储容器。如果找不到该名称的容器，它将创建一个。

**注意：** 在 ASP.NET 5 中执行调出 Azure 存储空间的 API 是异步的。有关详细信息，请参阅[使用 Async 和 Await 进行异步编程](http://msdn.microsoft.com/library/hh191443.aspx)。下面的代码假定正在使用异步编程方法。

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();    

默认情况下，新容器是专用容器，因此您必须指定存储访问密钥才能从该容器下载 Blob。如果你要让容器中的文件可供所有人使用，则可以使用以下代码将容器设置为公共容器。

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**注意：** 在接下来的部分中，将在代码的前面使用全部此代码。

#####将 Blob 上载到容器中
若要将 Blob 文件上载到容器中，请获取容器引用，并使用它来获取 Blob 引用。获取 Blob 引用后，可以通过调用 **UploadFromStreamAsync()** 方法，将任何数据流上载到该 Blob。此操作将创建 Blob（如果该 Blob 不存在），或者覆盖它（如果该 Blob 存在）。下面的示例演示了如何将 Blob 上载到容器中，并假定已创建容器。

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

#####列出容器中的 Blob
若要列出容器中的 Blob，首先需要获取容器引用。然后，你可以调用容器的 **ListBlobsSegmentedAsync()** 方法来检索其中的 Blob 和/或目录。若要访问返回的 **IListBlobItem** 的丰富属性和方法，您必须将它转换到 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 对象。如果 Blob 类型未知，您可以使用类型检查来确定要将其转换为哪种类型。以下代码演示了如何检索和输出容器中每项的 URI。

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
            {
                if (item.GetType() == typeof(CloudBlockBlob))
                {
                    CloudBlockBlob blob = (CloudBlockBlob)item;
                    Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                }

                else if (item.GetType() == typeof(CloudPageBlob))
                {
                    CloudPageBlob pageBlob = (CloudPageBlob)item;

                    Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
                }

                else if (item.GetType() == typeof(CloudBlobDirectory))
                {
                    CloudBlobDirectory directory = (CloudBlobDirectory)item;

                    Console.WriteLine("Directory: {0}", directory.Uri);
                }
            }
        } while (token != null);

还有其他方法来列出 Blob 容器内容。有关详细信息，请参阅[如何通过 .NET 使用 Blob 存储](http://windowsazure.cn/zh-cn/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob)。

#####下载 Blob
若要下载 Blob，首先请获取对该 Blob 的引用，然后再调用 **DownloadToStreamAsync()** 方法。以下示例使用 **DownloadToStreamAsync()** 方法将 Blob 内容传输到稍后可以另存为本地文件的流对象。

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named "myfile".
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

还有其他方法可将 Blob 另存为文件。有关详细信息，请参阅[如何通过 .NET 使用 Blob 存储](http://windowsazure.cn/zh-cn/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs)。

#####删除 Blob
若要删除 Blob，首先请获取对该 Blob 的引用，然后再对其调用 **DeleteAsync()** 方法。

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[了解有关 Azure 存储空间的详细信息](http://azure.microsoft.com/documentation/services/storage/)
另请参阅[在服务器资源管理器中浏览存储资源](http://msdn.microsoft.com/zh-cn/library/azure/ff683677.aspx)以及 [ASP.NET 5](http://www.asp.net/vnext)。
