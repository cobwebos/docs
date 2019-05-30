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
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a1d2cc50b405df2c71d94e74973b3291a4e908cb
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393490"
---
# <a name="content-key-policies"></a>内容密钥策略

借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 

若要指定你的流上的加密选项，您需要创建[流式处理策略](streaming-policy-concept.md)并将其与你[流式处理定位符](streaming-locators-concept.md)。 您创建[内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)若要配置如何内容密钥 (提供对安全访问你[资产](assets-concept.md)) 传递来的最终客户端。 您需要设置的要求 （限制） 上必须满足密钥使用传递到客户端的指定配置内容密钥策略。 此内容的密钥策略不需要明确的流式处理或下载。 

通常情况下，关联您**内容密钥策略**与你**流式处理定位符**。 或者，可以指定在流式处理策略的内部内容的密钥策略 （在创建自定义的流式处理策略对于高级方案时）。 

建议让媒体服务自动生成内容密钥。 通常情况下，您将使用且生存期较长的密钥，并检查策略是否存在与**获取**。 若要获取密钥，需调用单独的操作方法来获取机密或凭据，请参阅下面的示例。

**内容密钥策略**是可更新的。 密钥传递缓存可能需要长达 15 分钟来进行更新并获取更新后的策略。 

> [!IMPORTANT]
> * **内容密钥策略**的 Datetime 类型的属性始终采用 UTC 格式。
> * 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。

## <a name="example"></a>示例

若要获取的键，使用**GetPolicyPropertiesWithSecretsAsync**，如下所示[从现有策略获取签名密钥](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)示例。

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

* [使用 AES-128 动态加密和密钥传递服务](protect-with-aes128.md)
* [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
