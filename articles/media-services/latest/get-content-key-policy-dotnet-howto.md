---
title: 使用媒体服务 v3 .NET SDK 从现有策略中获取签名密钥 - Azure | Microsoft Docs
description: 本主题说明如何使用媒体服务 v3 .NET SDK 从现有策略中获取签名密钥。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471165"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>从现有策略获取签名密钥

V3 API 的主要设计原则之一是使 API 更安全。 v3 Api 不返回机密或凭据上**获取**或**列表**操作。 在响应中，密钥始终为 null、空值或进行了净化。 用户需要调用一个单独的操作方法来获取机密信息或凭据。 **读取器**角色不能调用操作，以便它不能调用 Asset.ListContainerSas，StreamingLocator.ListContentKeys，ContentKeyPolicies.GetPolicyPropertiesWithSecrets 等操作。 具有单独的操作，可根据需要自定义角色中设置更精细的 RBAC 安全权限。

有关详细信息，请参阅[RBAC 和媒体服务帐户](rbac-overview.md)

本文中的示例演示如何使用 .NET 从现有策略中获取签名密钥。 
 
## <a name="download"></a>下载 

使用以下命令将包含完整 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
带有机密的 ContentKeyPolicy 示例位于 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) 文件夹中。

## <a name="get-contentkeypolicy-with-secrets"></a>获取带有机密的 ContentKeyPolicy 

若要访问密钥，请使用 **GetPolicyPropertiesWithSecretsAsync**，如下例所示。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>后续步骤

[设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md) 
