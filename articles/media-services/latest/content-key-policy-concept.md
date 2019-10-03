---
title: 媒体服务中的内容密钥策略 - Azure | Microsoft Docs
description: 本文介绍了何为内容密钥策略以及 Azure 媒体服务如何使用这些终结点。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8809bf25c3bcfb26fb0ad251a2b09dfdca2a3e04
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679187"
---
# <a name="content-key-policies"></a>内容密钥策略

借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 

若要在流上指定加密选项, 需要创建[流策略](streaming-policy-concept.md)并将其与[流式处理定位符](streaming-locators-concept.md)相关联。 你将创建[内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)来配置如何将内容密钥 (提供对[资产](assets-concept.md)的安全访问) 传递给最终客户端。 需要在内容密钥策略上设置要求 (限制), 才能将具有指定配置的密钥传递到客户端。 清除流式处理或下载不需要内容密钥策略。 

通常会将内容密钥策略与[流式处理定位符](streaming-locators-concept.md)相关联。 或者, 你可以在[流式处理策略](streaming-policy-concept.md)中指定内容密钥策略 (为高级方案创建自定义流式处理策略时)。 

> [!NOTE]
> `Datetime`类型的内容密钥策略的属性始终采用 UTC 格式。

## <a name="best-practices-and-considerations"></a>最佳做法和注意事项

> [!IMPORTANT]
> 请查看以下建议。

* 应为媒体服务帐户设计一组有限的策略, 并在需要相同的选项时将它们重新用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。
* 内容密钥策略是可更新的。 密钥传送缓存最多可能需要15分钟来更新和选取更新的策略。 

   更新策略后, 你将覆盖现有的 CDN 缓存, 这可能会导致使用缓存内容的客户出现播放问题。  
* 建议你不要为每个资产创建新的内容密钥策略。 在需要相同策略选项的资产之间共享相同内容密钥策略的主要优势包括:
   
   * 管理少量策略更容易。
   * 如果需要更新内容密钥策略, 所做的更改将立即对所有新的许可证请求生效。
* 如果确实需要创建新策略, 则必须为资产创建新的流式处理定位符。
* 建议媒体服务自动生成内容密钥。 

   通常, 你会使用长期的密钥, 并使用[Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get)检查内容密钥策略是否存在。 若要获取密钥，需调用单独的操作方法来获取机密或凭据，请参阅下面的示例。

## <a name="example"></a>示例

若要转到密钥, 请`GetPolicyPropertiesWithSecretsAsync`使用, 如[从现有策略获取签名密钥](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)示例中所示。

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

* [使用 AES-128 动态加密和密钥传递服务](protect-with-aes128.md)
* [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
