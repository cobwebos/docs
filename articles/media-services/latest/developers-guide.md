---
title: Azure 媒体服务 v3 SDK - Azure
description: 本文概述了如何使用 SDK/工具通过媒体服务 v3 API 开始进行开发。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/20/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: cda6c9cd1f9c8b9305349f0904aeb744ba373711
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350243"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>使用 SDK/工具通过媒体服务 v3 API 开始进行开发

作为开发者，可以利用媒体服务 [REST API](https://aka.ms/ams-v3-rest-ref) 或客户端库，与 REST API 交互，以轻松创建、管理和维护自定义媒体工作流。 [媒体服务 v3](https://aka.ms/ams-v3-rest-sdk) API 基于 OpenAPI 规范（以前称为 Swagger）。

本主题提供 SDK、工具、文档的链接， 并提供一些适用于不同开发环境的信息。

## <a name="prerequisites"></a>先决条件

若要开始针对媒体服务进行开发，你需要满足以下条件：

- 一个有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [了解基本概念](concepts-overview.md)
- [创建媒体服务帐户](create-account-cli-how-to.md)

## <a name="start-developing"></a>开始开发

Azure 媒体服务支持以下 SDK/工具 

- [Azure CLI](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure 媒体服务浏览器

[Azure 媒体服务浏览器](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是可供希望了解媒体服务的 Windows 客户使用的工具。 AMSE 是一个 Winforms/C# 应用程序，用于通过媒体服务对 VOD 和实时内容进行上传、下载、编码和流式传输。 AMSE 工具适用于希望在不编写任何代码的情况下测试媒体服务的客户。 对于希望使用媒体服务进行开发的客户，可以为其提供 AMSE 代码作为资源。

AMSE 是一个开源项目，由社区提供支持（可以将问题报告给 https://github.com/Azure/Azure-Media-Services-Explorer/issues)）。 本项目采用 [Microsoft 开源行为准则](https://opensource.microsoft.com/codeofconduct/)。 有关详细信息，请参阅[行为准则常见问题解答](https://opensource.microsoft.com/codeofconduct/faq/)；若有其他任何问题或意见，请联系 opencode@microsoft.com。

## <a name="rest"></a>REST

若要开始使用媒体服务 REST API 进行开发，请安装 REST 客户端。 例如，**Postman**、带 REST 插件的 **Visual Studio Code** 或 **Telerik Fiddler**。 我们会将 [Postman](media-rest-apis-with-postman.md) 用于媒体服务 v3 示例。

浏览媒体服务 [REST API 参考](https://aka.ms/ams-v3-rest-ref)文档并查看以下示例：

- [教程：基于 URL 对远程文件进行编码并流式传输视频 - REST](stream-files-tutorial-with-rest.md)
- [将文件上传到媒体服务帐户 - REST](upload-files-rest-how-to.md)
- [使用媒体服务创建筛选器 - REST](filters-dynamic-manifest-rest-howto.md)
- [基于 Azure 资源管理器的 REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

浏览媒体服务 [Azure CLI 参考](https://aka.ms/ams-v3-cli-ref)文档并查看以下示例：

- [缩放媒体保留单位 - CLI](media-reserved-units-cli-how-to.md)
- [创建媒体服务帐户 - CLI](./scripts/cli-create-account.md) 
- [重置帐户凭据 - CLI](./scripts/cli-reset-account-credentials.md)
- [创建资产 - CLI](./scripts/cli-create-asset.md)
- [上传文件 - CLI](./scripts/cli-upload-file-asset.md)
- [创建转换 - CLI](./scripts/cli-create-transform.md)
- [创建作业 - CLI](./scripts/cli-create-jobs.md)
- [创建 EventGrid - CLI](./scripts/cli-create-event-grid.md)
- [发布资产 - CLI](./scripts/cli-publish-asset.md)
- [筛选器 - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

浏览媒体服务 [.NET 参考](https://aka.ms/ams-v3-dotnet-ref)文档并查看以下示例：

- [教程：对视频进行上传、编码和流式处理 - .NET](stream-files-tutorial-with-api.md) 
- [教程：通过媒体服务 v3 进行实时流式传输 - .NET](stream-live-tutorial-with-api.md)
- [教程：使用媒体服务 v3 来分析视频 - .NET](analyze-videos-tutorial-with-api.md)
- [从本地文件创建作业输入 - .NET](job-input-from-local-file-how-to.md)
- [从 HTTPS URL 创建作业输入 - .NET](job-input-from-http-how-to.md)
- [对自定义转换进行编码 - .NET](customize-encoder-presets-how-to.md)
- [使用 AES-128 动态加密和密钥传递服务 - .NET](protect-with-aes128.md)
- [使用 DRM 动态加密和许可证传送服务 - .NET](protect-with-drm.md)
- [从现有策略获取签名密钥 - .NET](get-content-key-policy-dotnet-howto.md)
- [使用媒体服务创建筛选器 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [使用媒体服务 v3 的 Azure Functions v2 的高级视频点播示例](https://aka.ms/ams3functions)

## <a name="java"></a>Java

查看媒体服务 [Java 参考](https://aka.ms/ams-v3-java-ref)文档。

## <a name="nodejs"></a>Node.js

浏览媒体服务 [Node.js 参考](https://aka.ms/ams-v3-nodejs-ref)文档并查看[示例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)，了解如何将媒体服务 API 与 Node.js 配合使用。

## <a name="python"></a>Python

查看媒体服务 [Python 参考](https://aka.ms/ams-v3-python-ref)文档。

## <a name="go"></a>Go

查看媒体服务 [Go 参考](https://aka.ms/ams-v3-go-ref)文档。

## <a name="next-steps"></a>后续步骤

- [创建帐户 - CLI](create-account-cli-how-to.md)
- [访问 API - CLI](access-api-cli-how-to.md)

