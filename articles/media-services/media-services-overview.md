---
title: "Azure 媒体服务概述 | Microsoft Docs"
description: "本部分提供 Azure 媒体服务的概述"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 4cec6db8b05fa0023cc0166726159b1ec2de8edb
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="azure-media-services-overview"></a>Azure 媒体服务概述 

Microsoft Azure 媒体服务是一个可扩展的基于云的平台，使开发人员能够生成可缩放的媒体管理和传送应用程序。 媒体服务基于 REST API，你可以使用这些 API 安全地上传、存储、编码和打包视频或音频内容，以供点播以及以实时流形式传送到各种客户端（例如，电视、电脑和移动设备）。

可以完全使用媒体服务构建端到端工作流。 也可以选择使用第三方组件来构建工作流的某些组成部分。 例如，使用第三方编码器进行编码。 然后，使用媒体服务进行上传、保护、打包和传送。

可以选择实时流式播放你的内容，或者根据点播情况交付内容。 本主题还提供了其他相关主题的链接。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
可以在此处查看 AMS 学习路径：

* [AMS 实时流式处理工作流](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [AMS 按需流式处理工作流](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>先决条件

要开始使用 Azure 媒体服务，应该具备以下条件：

* 一个 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com)。
* Azure 媒体服务帐户。 有关详细信息，请参阅[创建帐户](media-services-portal-create-account.md)。
* （可选）设置开发环境。 为开发环境选择“.NET”或“REST API”。 有关详细信息，请参阅 [设置环境](media-services-dotnet-how-to-use.md)。

    此外，请学习如何[以编程方式连接到 AMS API](media-services-use-aad-auth-to-access-ams-api.md)。
* 处于已启动状态的标准或高级流式处理终结点。  有关详细信息，请参阅[管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDK 和工具

要构建媒体服务解决方案，可以使用：

* [媒体服务 REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* 可用的客户端 SDK 之一：
    * [适用于 .NET 的 Azure 媒体服务 SDK](https://github.com/Azure/azure-sdk-for-media-services)、
    * [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)，
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)，
    * [适用于 Node.js 的 Azure 媒体服务](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) （这是 Node.js SDK 的非 Microsoft 版本。 它由社区维护，当前未包括所有的 AMS API）。
* 现有工具：
    * [Azure 门户](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) （Azure 媒体服务资源管理器 (AMSE) 是适用于 Windows 的 Winforms/C# 应用程序）

> [!NOTE]
> 若要获取最新版本的 Java SDK 并开始使用 Java 进行开发，请参阅[媒体服务的 Java 客户端 SDK 入门](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)。 <br/>
> 若要下载最新的媒体服务 PHP SDK，请在 [Packagist 存储库](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)中查找 0.5.7 版 Microsoft/WindowAzure 包。  

## <a name="code-samples"></a>代码示例

在“Azure 代码示例”库中查找多个代码示例：[Azure 媒体服务代码示例](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)。

## <a name="concepts"></a>概念

有关 Azure 媒体服务的概念，请参阅 [概念](media-services-concepts.md)。

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>支持的媒体服务功能跨数据中心方案和可用性

有关详细信息，请参阅 [AMS 功能和服务的跨数据中心方案和可用性](scenarios-and-availability.md)。

## <a name="service-level-agreement-sla"></a>服务级别协议 (SLA)

* 对于媒体服务编码，我们保证 REST API 事务可实现 99.9% 的可用性。
* 如果客户购买了标准或高级流式处理终结点，则对于流式处理，我们以 99.9% 的可用性保证成功处理现有媒体内容的请求。
* 对于实时频道，我们保证运行中的频道在至少 99.9% 的时间都能建立外部连接。
* 对于内容保护，我们保证会在至少 99.9% 的时间成功满足密钥请求。
* 对于索引器，我们将使用编码保留单位在 99.9% 的时间成功处理索引器任务请求。

有关详细信息，请参阅 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)。

若要了解此功能在数据中心的可用性，请参阅[可用性](scenarios-and-availability.md#availability)部分。

## <a name="support"></a>支持

[Azure 支持](https://azure.microsoft.com/support/options/) 为 Azure（包括媒体服务）提供支持选项。

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
