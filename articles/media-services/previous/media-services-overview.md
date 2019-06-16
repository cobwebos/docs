---
title: Azure 媒体服务概述 | Microsoft Docs
description: 本部分提供 Azure 媒体服务的概述
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 7f0a071012685ee192d4f866396a5953d0a4508c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937257"
---
# <a name="azure-media-services-overview"></a>Azure 媒体服务概述 

> [!div class="op_single_selector" title1="选择正在使用的媒体服务的版本："]
> * [第 3 版](../latest/media-services-overview.md)
> * [第 2 版](media-services-overview.md)

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，请参阅[从 v2 到 v3 迁移指南](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure 媒体服务 (AMS) 是一个可扩展的基于云的平台，使开发人员能够生成可缩放的媒体管理和传送应用程序。 媒体服务基于 REST API，你可以使用这些 API 安全地上传、存储、编码和打包视频或音频内容，以供点播以及以实时流形式传送到各种客户端（例如，电视、电脑和移动设备）。

可以完全使用媒体服务构建端到端工作流。 也可以选择使用第三方组件来构建工作流的某些组成部分。 例如，使用第三方编码器进行编码。 然后，使用媒体服务进行上传、保护、打包和传送。 可以选择实时流式播放你的内容，或者根据点播情况交付内容。 

## <a name="prerequisites"></a>必备组件

若要开始使用 Azure 媒体服务，应该具备以下条件：

* 一个 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com)。
* Azure 媒体服务帐户。 有关详细信息，请参阅[创建帐户](media-services-portal-create-account.md)。
* （可选）设置开发环境。 为开发环境选择“.NET”或“REST API”。 有关详细信息，请参阅 [设置环境](media-services-dotnet-how-to-use.md)。

    此外，请学习如何[以编程方式连接到 AMS API](media-services-use-aad-auth-to-access-ams-api.md)。
* 处于已启动状态的标准或高级流式处理终结点。  有关详细信息，请参阅[管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDK 和工具

若要构建媒体服务解决方案，可以使用：

* [媒体服务 REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* 可用的客户端 SDK 之一：
    * 适用于 .NET 的 Azure 媒体服务 SDK
    
        * [NuGet 包](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Github 源代码](https://github.com/Azure/azure-sdk-for-media-services)
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

在  “Azure 代码示例”库中查找多个代码示例：[Azure 媒体服务代码示例](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)。

## <a name="concepts"></a>概念

有关 Azure 媒体服务的概念，请参阅 [概念](media-services-concepts.md)。

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>支持的媒体服务功能跨数据中心方案和可用性

有关详细信息，请参阅 [AMS 功能和服务的跨数据中心方案和可用性](scenarios-and-availability.md)。

## <a name="service-level-agreement-sla"></a>服务级别协议 (SLA)

有关详细信息，请参阅 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)。

若要了解此功能在数据中心的可用性，请参阅[可用性](scenarios-and-availability.md#availability)部分。

## <a name="support"></a>支持

[Azure 支持](https://azure.microsoft.com/support/options/) 为 Azure（包括媒体服务）提供支持选项。

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
