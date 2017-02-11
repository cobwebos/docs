---
title: "如何通过 iOS 使用 Azure Blob 存储 | Microsoft Docs"
description: "使用 Azure Blob 存储（对象存储）将非结构化数据存储在云中。"
services: storage
documentationcenter: ios
author: micurd
manager: jahogg
editor: tysonn
ms.assetid: df188021-86fc-4d31-a810-1b0e7bcd814b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: objective-c
ms.topic: article
ms.date: 10/18/2016
ms.author: micurd
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 11f6c46b03487e50ddd8a5808e41529660bf07ca


---
# <a name="how-to-use-blob-storage-from-ios"></a>如何通过 iOS 使用 Blob 存储
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概述
本文将演示如何使用 Microsoft Azure Blob 存储执行常见任务。 示例采用 Objective-C 编写，并使用了[适用于 iOS 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-ios)。 涉及的任务包括**上传**、**列出**、**下载**和**删除**Blob。 有关 Blob 的详细信息，请参阅[后续步骤](#next-steps)部分。 也可下载[示例应用](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample)，快速了解如何在 iOS 应用程序中使用 Azure 存储。

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>将 Azure 存储空间 iOS 库导入到应用程序中
可使用 [Azure 存储 CocoaPod](https://cocoapods.org/pods/AZSClient) 或导入 **Framework** 文件，将 Azure 存储 iOS 库导入到应用程序。

## <a name="cocoapod"></a>CocoaPod
1. 如果尚未这样做，请打开终端窗口并运行以下命令，在计算机上[安装 CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3)
   
        sudo gem install cocoapods
2. 接下来，在项目目录（包含 `.xcodeproj` 文件的目录）中创建名为 `Podfile` 的文件（无扩展名）。 将以下内容添加到 `Podfile` 并保存
   
        pod 'AZSClient'
3. 在终端窗口中，导航到项目目录并运行以下命令
   
        pod install
4. 如果 .`.xcodeproj` 已在 Xcode 中打开，请将它关闭。 在项目目录中打开新建的项目文件（扩展名为 `.xcworkspace`）。 这是从现在开始要使用的文件。

## <a name="framework"></a>框架
若要使用 Azure 存储空间 iOS 库，首先需要生成框架文件。

1. 首先，下载或克隆 [azure-storage-ios repo](https://github.com/azure/azure-storage-ios)。
2. 转到“azure-storage-ios” -> “Lib” -> “Azure 存储客户端库”，并在 Xcode 中打开 `AZSClient.xcodeproj`。
3. 在 Xcode 的左上方，将活动方案从“Azure Storage Client Library”更改为“Framework”。
4. 生成项目 (⌘+B)。 这将在桌面上创建 `AZSClient.framework` 文件。

可以通过执行以操作将框架文件导入到应用程序：

1. 在 Xcode 中创建一个新项目或打开现有项目。
2. 单击左侧导航栏中的项目，然后在项目编辑器顶部，单击“常规”选项卡。
3. 在“链接的框架和库”部分下，单击“添加”按钮 (+)。
4. 单击“添加其他...”。 导航到刚创建的 `AZSClient.framework` 文件并添加它。
5. 在“链接的框架和库”部分下，再次单击“添加”按钮 (+)。
6. 在已提供的库的列表中，搜索 `libxml2.2.dylib` 并将其添加到你的项目。
7. 在项目编辑器顶部，单击“生成设置”选项卡。
8. 在“搜索路径”部分中，双击“框架搜索路径”，并将该路径添加到 `AZSClient.framework` 文件。

## <a name="import-statement"></a>Import 语句
你需要在要调用 Azure 存储空间 API 的文件中添加以下 import 语句。

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[!INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>异步操作
> [!NOTE]
> 执行对服务的请求的所有方法都是异步操作。 在代码示例中，你会发现这些方法都有完成处理程序。 请求完成**后**，将运行完成处理程序内的代码。 正在发出请求**时**，将运行完成处理程序后的代码。
> 
> 

## <a name="create-a-container"></a>创建容器
Azure 存储空间中的每个 Blob 都必须驻留在一个容器中。 以下示例演示如何在存储帐户中创建一个名为 *newcontainer* 的容器（如果它尚不存在）。 在选择容器的名称时，请注意上面提到的命名规则。

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

可通过查看 [Microsoft Azure 存储资源管理器](http://storageexplorer.com)，并验证 *newcontainer* 是否存在于存储帐户的容器列表中来确认此操作是否有效。

## <a name="set-container-permissions"></a>设置容器权限
默认情况下，容器的权限配置为**专用**访问权限。 但是，容器提供了几个不同的容器访问权限选项：

* **专用**：仅帐户所有者可读取容器和 Blob 数据。
* **Blob**：可通过匿名请求读取此容器中的 Blob 数据，但容器数据不可用。 客户端无法通过匿名请求枚举容器中的 Blob。
* **容器**：可通过匿名请求读取容器和 Blob 数据。 客户端可以通过匿名请求枚举容器中的 Blob，但无法枚举存储帐户中的容器。

以下示例演示如何创建一个具有**容器**访问权限的容器，这将允许 Internet 上的所有用户对其进行公共只读访问：

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>将 Blob 上载到容器中
如 [Blob 服务概念](#blob-service-concepts)部分中所述，Blob 存储提供了三种不同类型的 blob：块 blob、追加 blob 和页 blob。 目前，Azure 存储空间 iOS 库只支持块 blob。 大多数情况下，推荐使用块 Blob。

以下示例演示如何从 NSString 上载块 blob。 如果此容器中已存在同名的 blob，则将覆盖该 blob 的内容。

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

可通过查看 [Microsoft Azure 存储资源管理器](http://storageexplorer.com)并验证容器 *containerpublic* 是否包含该 Blob *sampleblob* 来确认此操作是否正常工作。 在此示例中，我们使用了公共容器，因此你还可以通过转到 blob URI 来验证此操作是否正常工作：

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

除了从 NSString 上载块 blob 外，NSData、NSInputStream 或本地文件也存在类似的方法。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob
以下示例演示如何列出容器中的所有 blob。 执行此操作时，应注意以下参数：     

* **continuationToken** - 继续标记表示列出操作应开始的位置。 如果未提供标记，它将从开头列出 blob。 可以列出任意数目的 blob，从零到最大集。 即使此方法返回零个结果，如果 `results.continuationToken` 不为空，则服务中也可能存在更多 blob 未列出。
* **prefix** - 可以指定要用于 blob 列出的前缀。 将仅列出以该前缀开头的 blob。
* **useFlatBlobListing** - 如[命名和引用容器和 blob](#naming-and-referencing-containers-and-blobs) 部分中所述，虽然 Blob 服务是平面存储方案，但可通过命名具有路径信息的 blob 来创建虚拟层次结构。 但是，目前不支持非平面列表；该功能即将推出。 目前，此值应为 `YES`
* **blobListingDetails** - 可指定在列出 blob 时要包含哪些项
  * `AZSBlobListingDetailsNone`：仅列出已提交的 blob，并且不返回 blob 元数据。
  * `AZSBlobListingDetailsSnapshots`：列出已提交的 blob 和 blob 快照。
  * `AZSBlobListingDetailsMetadata`：检索列表中返回的每个 blob 的 blob 元数据。
  * `AZSBlobListingDetailsUncommittedBlobs`：列出已提交和未提交的 blob。
  * `AZSBlobListingDetailsCopy`：在列表中包含复制属性。
  * `AZSBlobListingDetailsAll`：列出所有可用的已提交 blob、未提交 blob 和快照，并返回这些 blob 的所有元数据和复制状态。
* **maxResults** - 此操作可返回的结果的最大数目。 使用 -1 以不设置限制。
* **completionHandler** - 要使用列表操作的结果执行的代码块。

在此示例中，帮助器方法用于在每次返回继续标记时递归调用列出 blob 方法。

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>下载 Blob
以下示例演示如何将 blob 下载到 NSString 对象中。

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>删除 Blob
以下示例说明如何删除 blob。

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>删除 Blob 容器
以下示例说明如何删除容器。

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>后续步骤
了解如何从 iOS 使用 Blob 存储后，请单击以下链接详细了解 iOS 库和存储服务。

* [适用于 iOS 的 Azure 存储客户端库](https://github.com/azure/azure-storage-ios)
* [Azure 存储 iOS 参考文档](http://azure.github.io/azure-storage-ios/)
* [Azure 存储空间服务 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage)

如果对此库有任何疑问，可随时将问题发布到我们的 [MSDN Azure 论坛](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata)或[堆栈溢出](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)。
如果有关于 Azure 存储的功能建议，请将建议发布到 [Azure 存储反馈](https://feedback.azure.com/forums/217298-storage/)。




<!--HONumber=Nov16_HO3-->


