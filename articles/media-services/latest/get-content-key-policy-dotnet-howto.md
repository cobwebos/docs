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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322480"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>从现有策略获取签名密钥

V3 API 的主要设计原则之一是使 API 更安全。 v3 API 不在 **Get** 或 **List** 操作中返回机密或凭据。 在响应中，密钥始终为 null、空值或进行了净化。 你需要调用单独的操作方法来获取机密或凭据。 当某些 API 会检索/显示机密而另一些 API 不会这样做时，可以使用单独的操作设置不同的 RBAC 安全权限。 有关如何使用 RBAC 管理访问权限的信息，请参阅[使用 RBAC 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)。

此类示例包括 

* 不在 StreamingLocator 的 Get 中返回 ContentKey 值， 
* 不在 ContentKeyPolicy 的 Get 中返回限制密钥， 
* 不返回作业的 HTTP 输入 URL 的查询字符串部分（删除签名）。

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
