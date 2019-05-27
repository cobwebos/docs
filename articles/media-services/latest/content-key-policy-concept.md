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
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155724"
---
# <a name="content-key-policies"></a>内容密钥策略

借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。

若要指定你的流上的加密选项，您需要创建[流式处理策略](streaming-policy-concept.md)并将其与你[流式处理定位符](streaming-locators-concept.md)。 您需要创建[内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)若要配置如何内容密钥 (提供对安全访问你[资产](assets-concept.md)) 传递来的最终客户端。 **内容密钥策略**还与关联您**流式处理定位符**。 您需要设置的要求 （限制） 上必须满足密钥使用传递到客户端的指定配置内容密钥策略。 

建议让媒体服务自动生成内容密钥。 通常情况下，您将使用且生存期较长的密钥，并检查策略是否存在与**获取**。 若要获取密钥，需调用单独的操作方法来获取机密或凭据，请参阅下面的示例。

**内容密钥策略**是可更新的。 例如，如果需要进行密钥轮换，则可能需要更新策略。 可以更新现有策略中的主验证密钥以及备用验证密钥的列表。 密钥传递缓存可能需要长达 15 分钟来进行更新并获取更新后的策略。 

> [!IMPORTANT]
> * **内容密钥策略**的 Datetime 类型的属性始终采用 UTC 格式。
> * 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。

## <a name="example"></a>示例

若要访问密钥，请使用 **GetPolicyPropertiesWithSecretsAsync**，如下例所示。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

* [使用 AES-128 动态加密和密钥传递服务](protect-with-aes128.md)
* [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
