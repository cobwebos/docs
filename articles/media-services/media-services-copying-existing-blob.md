---
title: "将 blob 从存储帐户复制到 Azure 媒体服务资产中 | Microsoft 文档"
description: "本主题说明如何将现有 blob 复制到媒体服务资产中。 本示例使用 Azure 媒体服务 .NET SDK 扩展。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: b74fbba254f73c1df388b2b0ff9134cc7b8a31e8
ms.openlocfilehash: acc4780ea16f5a5ef0b87788b72595f29f6dcd89
ms.lasthandoff: 02/14/2017


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>将现有 Blob 复制到媒体服务资产中
本主题说明如何使用 [Azure 媒体服务 .NET SDK 扩展](https://github.com/Azure/azure-sdk-for-media-services-extensions/)将 Blob 从存储帐户复制到新的 Azure 媒体服务 (AMS) 资产中。

扩展方法可以处理：

- 常规资产。
- 实时存档资产（FragBlob 格式）。
- 属于不同媒体服务帐户的源和目标资产（甚至跨不同数据中心）。 但是，这样做可能会产生费用。 有关定价的详细信息，请参阅[数据传输](https://azure.microsoft.com/pricing/#header-11)。

> [!NOTE]
> 在不使用媒体服务 API 的情况下，你不应该尝试更改媒体服务生成的 BLOB 容器内容。
> 

本主题演示两个代码示例：

1. 将一个 AMS 帐户的资产中的 Blob 复制到另一个 AMS 帐户的新资产中。
2. 将某个存储帐户中的 Blob 复制到某个 AMS 帐户的新资产中。

## <a name="copy-blobs-between-two-ams-accounts"></a>在两个 AMS 帐户之间复制 Blob  

### <a name="prerequisites"></a>先决条件

两个媒体服务帐户。 请参阅主题[如何创建媒体服务帐户](media-services-portal-create-account.md)。

### <a name="download-sample"></a>下载示例
可以执行本文中的步骤，也可以从[此处](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)下载包含本文中所述代码的示例。

### <a name="set-up-your-project"></a>设置项目

1. 使用 Visual Studio 创建包含 C# 控制台应用程序项目的新解决方案。 
3. 使用 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) 安装和添加 Azure 媒体服务 .NET SDK 扩展 (windowsazure.mediaservices.extensions)。 安装此包也会安装适用于 .NET 的媒体服务 SDK 并添加所有其他必需的依赖项。
4. 添加此项目所需的其他引用：System.Configuration。
6. 将 appSettings 节添加到 .config 文件，并根据媒体服务帐户、目标存储帐户和源资产 ID 更新值。 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>将一个 AMS 帐户的资产中的 Blob 复制到另一个 AMS 帐户的资产中

以下代码使用单个扩展通过扩展的 **IAsset.Copy** 方法将源资产中的所有文件复制到目标资产。 提供异步支持时存在额外的重载。

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>将存储帐户中的 Blob 复制到 AMS 帐户中 

### <a name="prerequisites"></a>先决条件

- 一个要从中复制 Blob 的存储帐户。
- 一个要将 Blob 复制到的 AMS 帐户。

### <a name="set-up-your-project"></a>设置项目

1. 使用 Visual Studio 创建包含 C# 控制台应用程序项目的新解决方案。 
3. 使用 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) 安装和添加 Azure 媒体服务 .NET SDK 扩展 (windowsazure.mediaservices.extensions)。 安装此包也会安装适用于 .NET 的媒体服务 SDK 并添加所有其他必需的依赖项。
4. 添加此项目所需的其他引用：System.Configuration。
6. 将 appSettings 节添加到 .config 文件，并根据源存储帐户和目标 AMS 帐户更新值。
   
          <appSettings>
            <add key="MediaServicesAccountName" value="name" />
            <add key="MediaServicesAccountKey" value="key" />
            <add key="MediaServicesStorageAccountName" value="name" />
            <add key="MediaServicesStorageAccountKey" value="key" />
            <add key="SourceStorageAccountName" value="name" />
            <add key="SourceStorageAccountKey" value="key" />
          </appSettings>

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-a-ams-account"></a>将某个存储帐户中的 Blob 复制到某个 AMS 帐户的新资产中

以下代码演示如何将存储帐户中的 Blob 复制到媒体服务资产中。 

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            private static readonly string _sourceStorageAccountName =
         ConfigurationManager.AppSettings["SourceStorageAccountName"];
            private static readonly string _sourceStorageAccountKey =
                ConfigurationManager.AppSettings["SourceStorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;
    
            static void Main(string[] args)
            {
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey));
    
                _sourceStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName, 
                        _sourceStorageAccountKey), true);
    
                _destinationStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_mediaServicesStorageAccountName, 
                        _mediaServicesStorageAccountKey), true);
                
                CloudBlobClient sourceCloudBlobClient = 
                    _sourceStorageAccount.CreateCloudBlobClient();
                CloudBlobContainer sourceContainer = 
                    sourceCloudBlobClient.GetContainerReference("NameOfBlobContainerYouWantToCopy");
    
                CreateAssetFromExistingBlobs(sourceContainer);
            }
            
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
            {
                CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
    
                // Create a new asset. 
                IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);
    
                IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                    TimeSpan.FromHours(24), AccessPermissions.Write);
    
                ILocator destinationLocator = 
                    _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    
                // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
                CloudBlobContainer destAssetContainer =
                    destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);
    
                if (destAssetContainer.CreateIfNotExists())
                {
                    destAssetContainer.SetPermissions(new BlobContainerPermissions
                    {
                        PublicAccess = BlobContainerPublicAccessType.Blob
                    });
                }
    
                var blobList = sourceBlobContainer.ListBlobs();
    
                foreach (var sourceBlob in blobList)
                {
                    var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
    
                    ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);
    
                    // Call the CopyBlobHelpers.CopyBlobAsync extension method to copy blobs.
                    using (Task task = 
                        CopyBlobHelpers.CopyBlobAsync((CloudBlockBlob)sourceBlob, 
                            (CloudBlockBlob)destinationBlob, 
                            new BlobRequestOptions(), 
                            CancellationToken.None))
                    {
                        task.Wait();
                    }
    
                    assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                    assetFile.Update();
                    Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
                }
    
                asset.Update();
    
                destinationLocator.Delete();
                writePolicy.Delete();
    
                // Set the primary asset file.
                // If, for example, we copied a set of Smooth Streaming files, 
                // set the .ism file to be the primary file. 
                // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
                var ismAssetFiles = asset.AssetFiles.ToList().
                    Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
    
                // The following code assigns the first .ism file as the primary file in the asset.
                // An asset should have one .ism file.  
                ismAssetFiles.First().IsPrimary = true;
                ismAssetFiles.First().Update();
    
                return asset;
            }
        }
    }
    
## <a name="next-steps"></a>后续步骤

现在可以对上载的资产进行编码。 有关详细信息，请参阅 [对资产进行编码](media-services-portal-encode.md)。

还可以使用 Azure Functions，基于传入到所配置容器中的文件触发编码作业。 有关详细信息，请参阅[此示例](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


