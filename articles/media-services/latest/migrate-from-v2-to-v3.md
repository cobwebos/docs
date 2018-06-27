---
title: 从 Azure 媒体服务 v2 迁移到 v3 | Microsoft Docs
description: 本文介绍了 Azure 媒体服务 v3 中引入的更改，并说明了两个版本之间的差异。
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure 媒体服务, 流, 广播, 实时, 脱机
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/12/2018
ms.author: juliako
ms.openlocfilehash: a382af644d30f9f0ebb586273c982ef1766f50b0
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292042"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>从媒体服务 v2 迁移到 v3

> [!NOTE]
> Azure 媒体服务的最新版本目前处于预览状态，可能称为 v3 版本。

本文介绍了 Azure 媒体服务 (AMS) v3 中引入的更改，并说明了两个版本之间的差异。

## <a name="why-should-you-move-to-v3"></a>为什么应迁移到 v3？

### <a name="api-is-more-approachable"></a>API 更便于访问

*  v3 基于一个统一的 API 接口，该接口公开了基于 Azure 资源管理器构建的管理和操作功能。 Azure 资源管理器模板可用于创建和部署转换、流式处理终结点、直播事件等等。
* 开放的 API（又称为 Swagger）规范文档。
* 可用于 .Net、.Net Core、Node.js、Python、Java、Ruby 的 SDK。
* Azure CLI 集成。

### <a name="new-features"></a>新增功能

* 编码现在支持 HTTPS 引入（基于 Url 的输入）。
* 转换是 v3 中的新增功能。 转换用于共享配置、创建 Azure 资源管理器模板以及隔离特定客户或租户的编码设置。 
* 一个资产可以有多个 StreamingLocator，其中每个 StreamingLocator 有不同的动态打包和动态加密设置。
* 内容保护支持多密钥的功能。 
* LiveEvent 预览版支持动态打包和动态加密。 这样，用户便可以使用预览版中的内容保护以及 DASH 和 HLS 打包。
* 与早期的 Program 实体相比，使用 LiveOuput 更简单。 
* 添加了对实体的 RBAC 支持。

## <a name="changes-from-v2"></a>自 v2 以来的更改

* 在媒体服务 v3 中，仅当资产是使用媒体服务 v2 创建的时才支持存储加密（AES-256 加密）以实现向后兼容性。 这意味着 v3 会处理现有的存储加密资产，但不会允许创建新资产。

    对于使用 v3 创建的资产，媒体服务支持 [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)服务器端存储加密。
    
* 媒体服务 SDK 已脱离存储 SDK，这使用户可以更好地控制所使用的存储 SDK 并避免版本控制问题。 
* 在 v3 中，所有编码比特率以位/秒为单位。 这不同于 REST v2 Media Encoder Standard 预设。 例如，v2 中的比特率被指定为 128，但在 v3 中，它是 128000。 
* AssetFile、AccessPolicy、Ingestmanifest 不存在于 v3 中。
* Contentkey 不再是实体（StreamingLocator 的属性）。
* 事件网格支持替换 NotificationEndpoint。
* 某些实体已重命名

  * JobOutput 替换了 Task，现在只是 Job 的一部分。
  * LiveEvent 替换了 Channel。
  * LiveOutput 替换了 Program。
  * StreamingLocator 替换了 Locator。

## <a name="code-changes"></a>代码更改

### <a name="create-an-asset-and-upload-a-file"></a>创建资产并上传文件 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>提交作业

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>发布使用 AES 加密的资产 

#### <a name="v2"></a>v2

1. 创建 ContentKeyAuthorizationPolicyOption
2. 创建 ContentKeyAuthorizationPolicy
3. 创建 AssetDeliveryPolicy
4. 创建资产并上传内容或提交作业并使用输出资产
5. 将 AssetDeliveryPolicy 与 Asset 关联
6. 创建 ContentKey
7. 将 ContentKey 附加到 Asset
8. 创建 AccessPolicy
9. 创建 Locator

#### <a name="v3"></a>v3

1. 创建内容密钥策略
2. 创建 Asset
3. 上传内容或将 Asset 用作 JobOutput
4. 创建 StreamingLocator

## <a name="next-steps"></a>后续步骤

若要了解如何轻松地开始编码和流式传输视频文件，请查看[流文件](stream-files-dotnet-quickstart.md)。 

