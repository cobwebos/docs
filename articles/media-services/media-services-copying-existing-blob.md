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
ms.sourcegitcommit: 197bf0f0d95422f5bd696a8e9b0abc799f2951fc
ms.openlocfilehash: f71adf1f12e2be0c63465eff7d61a35d3256d4f2


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>将现有 Blob 复制到媒体服务资产中
本主题说明如何使用 [Azure 媒体服务 .NET SDK 扩展](https://github.com/Azure/azure-sdk-for-media-services-extensions/)将 blob 从存储帐户复制到新的 Microsoft Azure 媒体服务资产中。

此扩展方法适用于：

- 常规资产。
- 实时存档资产（FragBlob 格式）。
- 属于不同媒体服务帐户的源和目标资产（甚至跨不同数据中心）。 但是，这样做可能会产生费用。 有关定价的详细信息，请参阅[数据传输](https://azure.microsoft.com/pricing/#header-11)。

> [!NOTE]
> 在不使用媒体服务 API 的情况下，你不应该尝试更改媒体服务生成的 BLOB 容器内容。
> 

## <a name="download-sample"></a>下载示例
可以执行本文中的步骤，也可以从[此处](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)下载包含本文中所述代码的示例。

## <a name="prerequisites"></a>先决条件
* 在新的或现有的 Azure 订阅中拥有两个媒体服务帐户。 请参阅主题[如何创建媒体服务帐户](media-services-portal-create-account.md)。
* 操作系统：Windows 10、Windows 7、Windows 2008 R2 或 Windows 8。
* .NET Framework 4.5。
* Visual Studio 2010 SP1（专业版、高级专业版、旗舰版或学习版）或更高版本。

## <a name="set-up-your-project"></a>设置项目
在本部分中，你将创建和设置一个 C# 控制台应用程序项目。

1. 使用 Visual Studio 创建包含 C# 控制台应用程序项目的新解决方案。 
2. 为“名称”输入 CopyExistingBlobsIntoAsset，然后单击“确定”。
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

## <a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>将 Blob 从存储帐户复制到媒体服务资产中

以下代码使用单个扩展通过扩展的 **IAsset.Copy** 方法将源资产中的所有文件复制到目标资产。 提供异步支持时存在额外的重载。

将 Program.cs 类中的代码替换为以下代码：

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


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO1-->


