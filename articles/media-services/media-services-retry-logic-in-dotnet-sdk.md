---
title: "适用于 .NET 的媒体服务 SDK 中的重试逻辑 | Microsoft Docs"
description: "本主题概述了适用于 .NET 的媒体服务 SDK 中的重试逻辑。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e22a16c0929b28c475aa4caa0465651603713112
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>适用于 .NET 的媒体服务 SDK 中的重试逻辑
使用 Microsoft Azure 服务时，可能发生暂时性故障。 如果发生暂时性故障，在大多数情况下，该操作将在几次重试后成功。 适用于 .NET 的媒体服务 SDK 执行重试逻辑以处理与异常和错误关联的暂时性故障，这些异常和错误由 Web 请求、执行查询、保存更改和存储操作导致。  默认情况下，适用于 .NET 的媒体服务 SDK 向应用程序重新引发异常前会执行四次重试。 然后应用程序中的代码必须正确处理该异常。  

 下面为 Web 请求、存储、查询和 SaveChanges 策略的简要指南：  

* 存储策略用于 Blob 存储操作（上传或下载资产文件）。  
* Web 请求策略用于泛型 Web 请求（例如，获取身份验证令牌和解析用户群集终结点）。  
* 查询策略用于查询 REST 中的实体（例如，mediaContext.Assets.Where(...)）。  
* SaveChanges 策略用于执行服务中更改数据的任何操作（如创建实体、更新实体以及为操作调用服务函数）。  
  
  本主题列出了由适用于 .NET 的媒体服务 SDK 的重试逻辑处理的异常类型和错误代码。  

## <a name="exception-types"></a>异常类型
下表说明了适用于 .NET 的媒体服务 SDK 为可能导致暂时性故障的某些操作处理或不处理的异常。  

| 异常 | Web 请求 | 存储 | 查询 | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>有关详细信息，请参阅 [WebException 状态代码](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus)部分。 |是 |是 |是 |是 |
| DataServiceClientException<br/> 有关详细信息，请参阅 [HTTP 错误和状态代码](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |否 |是 |是 |是 |
| DataServiceQueryException<br/> 有关详细信息，请参阅 [HTTP 错误和状态代码](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |否 |是 |是 |是 |
| DataServiceRequestException<br/> 有关详细信息，请参阅 [HTTP 错误和状态代码](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |否 |是 |是 |是 |
| DataServiceTransportException |否 |否 |是 |是 |
| TimeoutException |是 |是 |是 |否 |
| SocketException |是 |是 |是 |是 |
| StorageException |否 |是 |否 |否 |
| IOException |否 |是 |否 |否 |

### <a name="WebExceptionStatus"></a> WebException 状态代码
下表显示了对其执行重试逻辑的 WebException 错误代码。 [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) 枚举定义状态代码。  

| 状态 | Web 请求 | 存储 | 查询 | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |是 |是 |是 |是 |
| NameResolutionFailure |是 |是 |是 |是 |
| ProxyNameResolutionFailure |是 |是 |是 |是 |
| SendFailure |是 |是 |是 |是 |
| PipelineFailure |是 |是 |是 |否 |
| ConnectionClosed |是 |是 |是 |否 |
| KeepAliveFailure |是 |是 |是 |否 |
| UnknownError |是 |是 |是 |否 |
| ReceiveFailure |是 |是 |是 |否 |
| RequestCanceled |是 |是 |是 |否 |
| 超时 |是 |是 |是 |否 |
| ProtocolError <br/>ProtocolError 上的重试由 HTTP 状态代码处理控制。 有关详细信息，请参阅 [HTTP 错误和状态代码](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。 |是 |是 |是 |是 |

### <a name="HTTPStatusCode"></a> HTTP 错误状态代码
查询和 SaveChanges 操作引发 DataServiceClientException、DataServiceQueryException 或 DataServiceQueryException 时，HTTP 错误状态代码将返回在 StatusCode 属性中。  下表显示了对其执行重试逻辑的错误代码。  

| 状态 | Web 请求 | 存储 | 查询 | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |否 |是 |否 |否 |
| 403 |否 |是<br/>处理等待时间较长的重试。 |否 |否 |
| 408 |是 |是 |是 |是 |
| 429 |是 |是 |是 |是 |
| 500 |是 |是 |是 |否 |
| 502 |是 |是 |是 |否 |
| 503 |是 |是 |是 |是 |
| 504 |是 |是 |是 |否 |

若要查看适用于 .NET 的媒体服务 SDK 重试逻辑的实际实现，请参阅 [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


