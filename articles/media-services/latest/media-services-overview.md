---
title: Azure 媒体服务 v3 概述
titleSuffix: Azure Media Services
description: Azure 媒体服务 v3 的概述，其中包含指向快速入门、教程和代码示例的链接。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure 媒体服务, 流, 广播, 实时, 脱机
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 7e4a242c1f81a504872dae6399683483c836daf2
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086900"
---
# <a name="azure-media-services-v3-overview"></a>Azure 媒体服务 v3 概述

Azure 媒体服务是一个基于云的平台，用于生成解决方案，以便实现广播质量的视频流、增强可访问性和分发、分析内容，等等。 无论你是应用开发人员、呼叫中心、政府机构还是娱乐公司，媒体服务都能帮助你创建应用，通过当今最热门的移动设备和浏览器向广大受众提供品质卓越的媒体体验。

媒体服务 v3 SDK 基于[媒体服务 v3 OpenAPI 规范 (Swagger)](https://aka.ms/ams-v3-rest-sdk)。

> [!NOTE]
> 目前，可以使用 [Azure 门户](https://portal.azure.com/)执行以下操作：管理媒体服务 v3 [直播活动](live-events-outputs-concept.md)、查看（而不是管理）v3 [资产](assets-concept.md)、[获取有关访问 API 的信息](access-api-portal.md)。 对于其他所有管理任务（例如，[转换和作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或某个受支持的 [SDK](media-services-apis-overview.md#sdks)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>符合性、隐私和安全性

需要重点提醒的是，在使用 Azure 媒体服务时，你必须遵守所有适用法律，不得以侵犯他人权利或可能对他人有害的方式使用媒体服务或任何 Azure 服务。

在将任何视频/图像上传到媒体服务之前，必须拥有该视频/图像的适当使用权限，包括根据法律的要求，获得视频/图像中的个人（如果有）授予的，在媒体服务和 Azure 中使用、处理和存储其数据的所有必要许可。 某些司法辖区可能会对收集、在线处理和存储某些类别的数据（例如生物识别数据）施加特殊的法律要求。 在根据特殊法律要求使用媒体服务和 Azure 处理与存储任何数据之前，必须确保符合可能适用于你的任何法律要求。

若要了解媒体服务中的合规性、隐私性和安全性，请访问 Microsoft [信任中心](https://www.microsoft.com/trust-center/?rtc=1)。 若要了解 Microsoft 的隐私义务、数据处理和保留惯例，包括如何删除数据，请查看 Microsoft 的[隐私声明](https://privacy.microsoft.com/PrivacyStatement)、[联机服务条款](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") 和[数据处理附录](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")。 使用媒体服务即表示你同意遵守 OST、DPA 和隐私声明。
 
## <a name="what-can-i-do-with-media-services"></a>媒体服务的功能是什么？

使用媒体服务，你可以在云中构建各种媒体工作流。 下面是媒体服务的功能的一些示例：

* 提供各种格式的视频，以便通过各种浏览器和设备播放视频。 为了将点播和实时传送视频流传输到各种客户端（移动设备、电视、电脑等），需要对视频和音频进行适当的编码和打包。 要了解如何交付和流式传输此类内容，请参阅[快速入门：编码和流式传输文件](stream-files-dotnet-quickstart.md)。
* 向大量在线观众流式传输实时体育赛事，例如足球、棒球、大学和高中体育运动等等。
* 广播公共会议和活动，例如市政厅、市议会和立法机构。
* 分析录制的视频或音频内容。 例如，为了实现更高的客户满意度，组织可以提取语音转文本并生成搜索索引和仪表板。 然后，他们可以提取围绕常见的投诉、投诉原因以及其他相关数据的情报。
* 当客户（例如电影工作室）需要限制对版权所有作品的访问和使用时，创建订阅视频服务并流式传输受 DRM 保护的内容。
* 提供脱机内容，以便在飞机、火车和汽车上播放。 如果客户希望断开网络连接，可能需要将内容下载到手机或平板电脑上播放。
* 使用 Azure 媒体服务和 [Azure 认知服务 API](https://docs.microsoft.com/azure/?pivot=products&panel=ai) 实现教育在线学习视频平台，提供语音转文本字幕、多种语言翻译等功能。
* 将 Azure 媒体服务与 [Azure 认知服务 API](https://docs.microsoft.com/azure/?pivot=products&panel=ai) 配合使用来为视频添加字幕和描述文字，满足更多受众的需求（例如，听力障碍人士或想用不同语言阅读的人）。
* 启用 Azure CDN 实现大幅度缩放，更好地处理即时高负载（例如在产品发布活动开始时）。

## <a name="how-can-i-get-started-with-v3"></a>如何开始使用 v3？ 

了解如何使用媒体服务 v3 来编码和打包内容、进行视频点播流式处理、实时广播和视频分析。 教程、API 参考和其他文档介绍了如何安全地向数百万用户传送可缩放的点播和直播视频或音频流。

> [!TIP]
> 在开始开发之前，请查看：<br/>* [基本概念](concepts-overview.md)（包括打包、编码、保护等重要概念）<br/>* [使用媒体服务 v3 API 进行开发](media-services-apis-overview.md)（包括有关访问 API、命名约定等的信息）

### <a name="sdks"></a>SDK

开始使用 [Azure 媒体服务 v3 客户端 SDK](media-services-apis-overview.md#sdks) 进行开发。

### <a name="quickstarts"></a>快速入门  

快速入门向新客户展示基本的第 1 天使用说明，以便快速试用媒体服务。

* [对视频文件进行流式处理 - .NET](stream-files-dotnet-quickstart.md)
* [对视频文件进行流式处理 - CLI](stream-files-cli-quickstart.md)
* [对视频文件进行流式处理 - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>教程

教程演示一些顶级媒体服务任务的基于方案的过程。

* [对远程文件和流视频进行编码 - REST](stream-files-tutorial-with-rest.md)
* [对上传的文件和流视频进行编码 - .NET](stream-files-tutorial-with-api.md)
* [实时流 - .NET](stream-live-tutorial-with-api.md)
* [分析视频 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 动态加密 - .NET](protect-with-aes128.md)

### <a name="samples"></a>示例

使用[此示例浏览器](https://docs.microsoft.com/samples/browse/?products=azure-media-services)浏览 Azure 媒体服务代码示例。

### <a name="how-to-guides"></a>操作方法指南

操作指南包含演示如何完成某项任务的代码示例。 在本部分中，你将看到许多示例。 下面是其中几个示例：

* [创建帐户 - CLI](create-account-cli-how-to.md)
* [访问 API - CLI](access-api-cli-how-to.md)
* [在将 HTTPS 作为作业输入的情况下进行编码 - .NET](job-input-from-http-how-to.md)  
* [监视事件 - 门户](monitor-events-portal-how-to.md)
* [通过多重 DRM 进行动态加密 - .NET](protect-with-drm.md) 
* [如何对自定义转换进行编码 - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[了解基本概念](concepts-overview.md)
