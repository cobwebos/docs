---
title: 从 Azure 媒体服务 v2 迁移到 v3
description: 本文介绍了 Azure 媒体服务 v3 中引入的更改，并说明了两个版本之间的差异。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure 媒体服务, 流, 广播, 实时, 脱机
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087830"
---
# <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3

本文介绍了 Azure 媒体服务 v3 中引入的更改，并说明了两个版本之间的差异。

## <a name="general-changes-from-v2"></a>版本2中的常规更改

* 对于通过 v3 创建的资产，媒体服务仅支持[Azure 存储服务器端存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。
    * 对于通过 v2 API 创建的，并采用媒体服务提供的[存储加密](../previous/media-services-rest-storage-encryption.md) (AES 256) 的资产，可以使用 v3 API。
    * 无法使用 v3 API 创建采用旧版 AES 256 [存储加密](../previous/media-services-rest-storage-encryption.md)的新资产。
* V3 中[资产](assets-concept.md)的属性不同于 v2，请参阅[属性的映射方式](#map-v3-asset-properties-to-v2)。
* v3 SDK 现在已与存储 SDK 分离，可让你更精细地控制所要使用的存储 SDK 版本，并避免版本控制问题。 
* 在 v3 API 中，所有编码比特率以“比特/秒”为单位。 这与 v2 Media Encoder Standard 预设不同。 例如，v2 中的比特率指定为 128 (kbps)，而在 v3 中，则指定 128000（比特/秒）。 
* v3 中不存在实体 AssetFile、AccessPolicy 和 Ingestmanifest。
* v3 中不存在 IAsset.ParentAssets 属性。
* 现在，Contentkey 不再是实体，而是流式处理定位符的一个属性。
* 事件网格支持替换 NotificationEndpoint。
* 以下实体已重命名
    * 作业输出取代了任务，现在是作业的一部分。
    * 流式处理定位符取代了定位符。
    * 直播活动取代了频道。<br/>直播活动计费基于实时频道计量器。 有关详细信息，请参阅[计费](live-event-states-billing.md)和[定价](https://azure.microsoft.com/pricing/details/media-services/)。
    * 实时输出取代了节目。
* 实时输出在创建时启动，在删除后停止。 v2 API 中的节目以不同的方式工作，它们必须在创建后启动。
* 若要获取有关作业的信息，需要知道在其下创建作业的转换名称。 
* 在 v2 中，XML[输入](../previous/media-services-input-metadata-schema.md)和[输出](../previous/media-services-output-metadata-schema.md)元数据文件作为编码作业的结果生成。 在 v3 中，元数据格式已从 XML 转换为 JSON。 
* 在 Media Services v2 中，可以指定初始化向量（IV）。 在媒体服务 v3 中，无法指定 FairPlay IV。 尽管这不会影响使用媒体服务进行打包和许可证传递的客户，但在使用第三方 DRM 系统提供 FairPlay 许可证（混合模式）时可能会遇到问题。 在这种情况下，请务必知道，FairPlay IV 派生自 cbc 密钥 ID，可以使用以下公式检索：

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    替换为

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    有关详细信息，请参阅[实时C#和 VOD 操作的混合模式下的媒体服务 v3 Azure Functions 代码](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)。
 
> [!NOTE]
> 查看适用于[媒体服务 v3 资源](media-services-apis-overview.md#naming-conventions)的命名约定。 还要查看[命名 blob](assets-concept.md#naming)。

## <a name="feature-gaps-with-respect-to-v2-apis"></a>与 v2 API 之间的功能差距

与 v2 API 相比，v3 API 存在以下功能差距。 我们正在弥补这些差距。

* [Premium Encoder](../previous/media-services-premium-workflow-encoder-formats.md) 和旧版[媒体分析处理器](../previous/media-services-analytics-overview.md)（Azure 媒体服务索引器 2 预览版、Face Redactor 等）不可通过 v3 访问。<br/>想要从媒体索引器 1 或 2 预览版迁移的客户可以立即使用 v3 API 中的 AudioAnalyzer 预设。  此新预设包含的功能比旧版媒体索引器 1 或 2 更多。 
* [V2 api 中 Media Encoder Standard 的许多高级功能](../previous/media-services-advanced-encoding-with-mes.md)当前在 v3 中不可用，例如：
  
    * 资产拼接
    * 叠加
    * 裁剪
    * 缩略图子画面
    * 输入没有音频时插入无提示音频轨
    * 当输入不包含视频时插入视频轨
* 包含转码的直播活动目前不支持静态图像插入中间流，以及通过 API 调用执行的广告标记插入。 
 
## <a name="asset-specific-changes"></a>资产特定更改

### <a name="map-v3-asset-properties-to-v2"></a>将 v3 资产属性映射到 v2

下表显示了 v3 中[资产](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)的属性映射到 V2 中资产的属性的方式。

|v3 属性|v2 属性|
|---|---|
|`id`-（唯一）完整的 Azure 资源管理器路径，请参阅[资产](https://docs.microsoft.com/rest/api/media/assets/createorupdate)中的示例||
|`name`-（唯一）请参阅[命名约定](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-（唯一）值以 `nb:cid:UUID:` 前缀开头。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` （创建选项）|
|`type`||

### <a name="storage-side-encryption"></a>存储端加密

若要保护静态资产，应通过存储端加密对资产进行加密。 下表显示了存储端加密在媒体服务中的工作方式：

|加密选项|说明|媒体服务 v2|媒体服务 v3|
|---|---|---|---|
|媒体服务存储加密|AES-256 加密，密钥由媒体服务管理。|支持<sup>(1)</sup>|不支持<sup>(2)</sup>|
|[静态数据的存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 存储提供的服务器端加密、由 Azure 或客户管理的密钥。|支持|支持|
|[存储客户端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 存储提供的客户端加密，Key Vault 中的客户管理的密钥。|不支持|不支持|

<sup>1</sup>虽然媒体服务支持以明文/不加密形式处理内容，但不建议这样做。

<sup>2</sup> 在媒体服务 v3 中，仅当资产是使用媒体服务 v2 创建的时才支持存储加密（AES-256 加密）以实现向后兼容性。 意思是，v3 适用于现有的存储加密资产，但不允许创建新的资产。

## <a name="code-differences"></a>代码差异

下表显示了常见方案中 v2 和 v3 的代码差异。

|场景|V2 API|V3 API|
|---|---|---|
|创建资产并上传文件 |[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|提交作业|[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>演示如何先创建转换，再提交作业。|
|发布使用 AES 加密的资产 |1. 创建 ContentKeyAuthorizationPolicyOption<br/>2. 创建 ContentKeyAuthorizationPolicy<br/>3. 创建 AssetDeliveryPolicy<br/>4. 创建资产并上传内容或提交作业并使用输出资产<br/>5. 将 AssetDeliveryPolicy 与资产关联<br/>6. 创建 ContentKey<br/>7. 将 ContentKey 附加到资产<br/>8. 创建 AccessPolicy<br/>9. 创建定位符<br/><br/>[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 创建内容密钥策略<br/>2. 创建资产<br/>3. 上传内容或使用资产作为 JobOutput<br/>4. 创建流式处理定位符<br/><br/>[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|获取作业详细信息和管理作业 |[用 v2 管理作业](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[用 v3 管理作业](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> 请将本文加入书签，并不时地查看最新信息。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](migrate-from-v2-to-v3.md)
