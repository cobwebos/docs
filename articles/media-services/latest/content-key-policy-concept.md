---
title: 媒体服务中的内容密钥策略 - Azure | Microsoft Docs
description: 本文介绍了何为内容密钥策略以及 Azure 媒体服务如何使用这些终结点。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: dd19d0730b25685b008af16d1c1eac6537750612
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297262"
---
# <a name="content-key-policies"></a>内容密钥策略

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 

若要针对流指定加密选项，需要创建[流策略](streaming-policy-concept.md)并将其与[流定位符](streaming-locators-concept.md)相关联。 创建[内容密钥策略](/rest/api/media/contentkeypolicies)，用于配置如何将内容密钥（提供对[资产](assets-concept.md)的安全访问）传送到终端客户端。 需要针对内容密钥策略设置要求（限制），只有满足这些要求，才能将使用指定配置的密钥传送到客户端。 明文格式的流式传输或下载不需要内容密钥策略。 

通常，你会将内容密钥策略关联到[流定位符](streaming-locators-concept.md)。 或者，可以在[流策略](streaming-policy-concept.md)中指定内容密钥策略（为高级方案创建自定义的流策略时）。 

## <a name="best-practices-and-considerations"></a>最佳做法和注意事项

> [!IMPORTANT]
> 请查看以下建议。

* 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。
* 内容密钥策略可更新。 密钥传送缓存可能需要长达 15 分钟来更新策略和拾取更新后的策略。 

   更新策略后，你将覆盖现有的 CDN 缓存，这可能会导致使用缓存内容的客户出现播放问题。  
* 建议不要为每个资产创建新的内容密钥策略。 在需要使用相同策略选项的资产之间共享同一内容密钥策略的主要好处包括：
   
   * 可以更轻松地管理少量的策略。
   * 如果需要更新内容密钥策略，所做的更改几乎可以立即对所有新的许可证请求生效。
* 如果确实需要创建新策略，则必须为资产创建新的流定位符。
* 建议让媒体服务自动生成内容密钥。 

   通常，你会使用生存期较长的密钥，并使用 [Get](/rest/api/media/contentkeypolicies/get) 检查内容密钥策略是否存在。 若要获取密钥，需调用单独的操作方法来获取机密或凭据，请参阅下面的示例。

## <a name="example"></a>示例

若要获取密钥，请使用 `GetPolicyPropertiesWithSecretsAsync`，如[从现有策略获取签名密钥](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)示例中所示。

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="additional-notes"></a>附加说明

* 内容密钥策略的 `Datetime` 类型的属性始终采用 UTC 格式。
* Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="next-steps"></a>后续步骤

* [使用 AES-128 动态加密和密钥传递服务](protect-with-aes128.md)
* [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
