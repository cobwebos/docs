---
title: "Azure 媒体服务错误代码 | Microsoft Docs"
description: "本主题概述 Azure 媒体服务错误代码。"
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 39886a955124429302609dd9d5a7c20ae7f498d9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="azure-media-services-error-codes"></a>Azure 媒体服务错误代码
使用 Microsoft Azure 媒体服务时，可能会收到来自服务的 HTTP 错误代码，这取决于具体的问题，例如身份验证令牌对于媒体服务不支持的操作过期。 以下是媒体服务可能返回的 **HTTP 错误代码**及可能的错误原因的列表。  

## <a name="400-bad-request"></a>400 错误请求
请求包含无效信息，并因以下可能的原因之一被拒绝：

* 指定了不支持的 API 版本。 有关最新版本，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。
* 未指定媒体服务的 API 版本。 若要了解如何指定 API 版本，请参阅[媒体服务操作 REST API 参考](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)。
  
  > [!NOTE]
  > 如果使用 .NET 或 Java SDK 连接到媒体服务，每当尝试对媒体服务执行某种操作时，系统会自动指定 API 版本。
  > 
  > 
* 指定了未定义的属性。 错误消息中给出了该属性名。 仅能指定是给定实体的成员的属性。 有关实体及其属性的列表，请参阅 [Azure 媒体服务 REST API 参考](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)。
* 指定了一个无效的属性值。 错误消息中给出了该属性名。 有关有效属性类型及其值的信息，请访问之前的链接。
* 缺少或需要属性值。
* 指定的部分 URL 包含错误值。
* 曾尝试更新 WriteOnce 属性。
* 曾尝试创建一个作业，该作业的输入资产带有未指定或无法确定的 AssetFile。
* 曾尝试更新 SAS 定位符。 只能创建或删除 SAS 定位符。 可以更新流式处理定位符。 有关详细信息，请参阅[定位符](https://docs.microsoft.com/rest/api/media/operations/locator)。
* 提交了不支持的操作或查询。

## <a name="401-unauthorized"></a>401 未授权
由于以下任一原因，无法在授权请求之前对其进行身份验证：

* 缺少身份验证标头。
* 错误的身份验证标头值。
  * 令牌已过期。 
  * 令牌包含无效签名。

## <a name="403-forbidden"></a>403 禁止访问
出于以下原因之一，未允许该请求：

* 找不到或已删除媒体服务帐户。
* 已禁用媒体服务帐户，且请求类型不是 HTTP GET。 服务操作也将返回 403 响应。
* 身份验证令牌不包含用户的凭据信息：AccountName 和/或 SubscriptionId。 可以在 Azure 管理门户中媒体服务帐户的媒体服务 UI 扩展中找到此信息。
* 无法访问资源。
  
  * 曾尝试使用不可用于媒体服务帐户的 MediaProcessor。
  * 曾尝试更新由媒体服务定义的 JobTemplate。
  * 曾尝试覆盖某些其他媒体服务帐户的定位符。
  * 曾尝试覆盖某些其他媒体服务帐户的 ContentKey。
* 由于已达到媒体服务帐户的服务配额上限，因此无法创建资源。 有关服务配额的详细信息，请参阅[配额和限制](media-services-quotas-and-limitations.md)。

## <a name="404-not-found"></a>404 未找到
由于以下原因之一，不允许对某个资源使用该请求：

* 曾尝试更新不存在的实体。
* 曾尝试删除不存在的实体。
* 曾尝试创建一个实体，该实体链接到其他不存在的实体。
* 曾尝试获取不存在的实体。
* 曾尝试指定未与媒体服务帐户关联的存储帐户。  

## <a name="409-conflict"></a>409 冲突
出于以下原因之一，未允许该请求：

* 资产内的多个 AssetFile 具有指定名称。
* 曾尝试在资产中创建第二个主 AssetFile。
* 曾尝试使用已用过的指定 ID 创建 ContentKey。
* 曾尝试使用已用过的指定 ID 创建定位符。
* IngestManifest 内多个 IngestManifestFile 具有指定名称。
* 曾尝试将第二个存储加密 ContentKey 链接到加密了存储的资产。
* 曾尝试将同一 ContentKey 链接到资产。
* 曾尝试向存储容器缺失或不再与资产关联的资产创建定位符。
* 曾尝试向已使用 5 个定位符的资产创建定位符。 （Azure 存储强制执行对一个存储容器只能使用五个共享访问策略这一限制。）
* 将资产的存储帐户链接到 IngestManifestAsset 与父 IngestManifest 所使用的存储帐户的情况不同。  

## <a name="500-internal-server-error"></a>500 内部服务器错误
在处理请求期间，媒体服务遇到了某种错误，从而阻止继续处理请求。 这可能是以下原因之一造成的：

* 创建资产或作业失败，因为媒体服务帐户的服务配额信息暂不可用。
* 创建资产或 IngestManifest blob 存储容器失败，因为帐户的存储帐户信息暂不可用。
* 其他意外错误。

## <a name="503-service-unavailable"></a>503 服务不可用
服务器当前无法接收请求。 服务请求过多可能引发此错误。 媒体服务限制机制会限制那些发出过多服务请求的应用程序的资源使用情况。

> [!NOTE]
> 检查错误消息和错误代码字符串以获取有关收到 503 错误的原因的更多详细信息。 此错误并不始终意味着限制。
> 
> 

可能的状态说明是：

* “服务器正忙。 之前运行这种类型的请求所用时间超过 {0} 秒。”
* “服务器正忙。 每秒超过 {0} 个请求可能会受到限制。”
* “服务器正忙。 {1} 秒内超过 {0} 个请求可能会受到限制。”

若要处理此错误，建议使用指数退让重试逻辑。 这意味着需要在重试之间使用渐进式（越来越长）的等待时长，从而生成连续错误响应。  有关详细信息，请参阅[暂时性故障处理应用程序块](https://msdn.microsoft.com/library/hh680905.aspx)。

> [!NOTE]
> 如果使用[用于 .Net 的 Azure 媒体服务 SDK](https://github.com/Azure/azure-sdk-for-media-services/tree/master)，则该 SDK 已实现 503 错误的重试逻辑。  
> 
> 

## <a name="see-also"></a>另请参阅
[媒体服务管理错误代码](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

