---
title: 媒体服务中的转换和作业
titleSuffix: Azure Media Services
description: 了解如何创建转换，以描述用于在 Azure 媒体服务中处理视频的规则。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571235"
---
# <a name="transforms-and-jobs-in-media-services"></a>媒体服务中的转换和作业

本主题提供有关[转换](https://docs.microsoft.com/rest/api/media/transforms)和[作业](https://docs.microsoft.com/rest/api/media/jobs)的详细信息，并解释这些实体之间的关系。

## <a name="overview"></a>概述

### <a name="transformsjobs-workflow"></a>转换/作业工作流

下图显示了转换/作业工作流：

![Azure 媒体服务中的转换和作业工作流](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>典型工作流

1. 创建转换。
2. 提交该转换下的作业。
3. 列出转换。
4. 如果你不打算在将来使用转换，请删除它。

#### <a name="example"></a>示例

假设你想要将所有视频的第一帧提取为缩略图–你需要执行的步骤如下：

1. 定义食谱或用于处理视频的规则： "使用视频的第一帧作为缩略图"。
2. 对于每个视频，你会告诉服务：
    1. 在哪里可以找到该视频。
    2. 在何处写入输出缩略图。

**转换**可帮助你一次性创建脚本（步骤 1），然后使用该脚本提交作业（步骤 2）。

> [!NOTE]
> **转换**和 Datetime 类型的**作业**的属性始终采用 UTC 格式。

## <a name="transforms"></a>转换

**转换**可用来配置对视频进行编码或分析的常见任务。 每个**转换**都说明了用于处理视频或音频文件的食谱或任务工作流。 单个转换可以应用多个规则。 例如，转换可以指定以给定的比特率将每个视频编码成 MP4 文件，并从该视频的第一帧生成缩略图。 针对要包含在转换中的每个规则，请添加一个 TransformOutput 条目。 使用预设来告知转换要如何处理输入媒体文件。

### <a name="viewing-schema"></a>查看架构

在媒体服务 v3 中，预设是 API 本身中的强类型化实体。 可以在[开放 API 规范（或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到这些对象的 "架构" 定义。 还可在[REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)、 [.net Sdk](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)或其他媒体服务 v3 SDK 参考文档中查看预设定义（如**StandardEncoderPreset**）。

### <a name="creating-transforms"></a>创建转换

可以使用 REST、CLI 或任意已发布的 Sdk 创建转换。 媒体服务 v3 API 由 Azure 资源管理器驱动，因此，也可以使用资源管理器模板在媒体服务帐户中创建和部署转换。 可以使用基于角色的访问控制来锁定对转换的访问。

### <a name="updating-transforms"></a>更新转换

如果需要更新[转换](https://docs.microsoft.com/rest/api/media/transforms)，请使用“更新”操作。 它用于对说明进行更改，或基础 TransformOutputs 的优先级。 建议在所有正在进行的作业都已完成时完成此类更新。 如果你想要重写脚本，则需要创建新的转换。

### <a name="transform-object-diagram"></a>转换对象关系图

下图显示了**转换**对象及其引用的对象，包括派生关系。 灰色箭头显示作业引用的类型，绿色箭头显示类派生关系。

选择要查看其完整大小的图像。  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>作业

**作业**是针对媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 **作业**指定输入视频位置和输出位置等信息。 你可以使用以下内容指定输入视频的位置： HTTPS Url、SAS Url 或[资产](https://docs.microsoft.com/rest/api/media/assets)。  

### <a name="job-input-from-https"></a>来自 HTTPS 的作业输入

如果内容已经可以通过 URL 进行访问，并且你不需要将源文件存储在 Azure 中（例如，从 S3 导入），请使用[来自 HTTPS 的作业输入](job-input-from-http-how-to.md)。 如果你的内容已存储在 Azure Blob 存储中，但不需要将文件存储在资产中，则此方法也适用。 目前，此方法仅支持使用单个文件作为输入。

### <a name="asset-as-job-input"></a>资产作为作业输入

如果输入内容已在资产中，或者内容已存储在本地文件中，请使用[资产作为作业输入](job-input-from-local-file-how-to.md)。 如果你计划发布输入资产以进行流式处理或下载（假设你想要将其发布以供下载，但又想要将语音转换为文本或面部检测），这也是一个不错的选择。 此方法支持多文件资产（例如，在本地编码的 MBR 流集）。

### <a name="checking-job-progress"></a>正在检查作业进度

使用事件网格监视事件可以获取作业的进度和状态。 有关详细信息，请参阅[使用事件网格监视事件](job-state-events-cli-how-to.md)。

### <a name="updating-jobs"></a>更新作业

[作业实体上](https://docs.microsoft.com/rest/api/media/jobs)的更新操作可用于在提交作业后修改*说明*和*优先级*属性。 仅当作业仍处于排队状态时，对优先级属性所做的更改才有效。 如果作业已开始处理或已完成，则更改优先级不起作用。

### <a name="job-object-diagram"></a>作业对象关系图

下图显示了**作业**对象及其引用的对象，包括派生关系。

单击图像可查看其完整大小。  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>配置媒体预留单位

对于媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议预配包含10个 S3 媒体保留单位（Mru）的帐户。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

有关详细信息，请参阅[使用 CLI 调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="see-also"></a>另请参阅

* [错误代码](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [媒体服务实体的筛选、排序和分页](entities-overview.md)

## <a name="next-steps"></a>后续步骤

- 在开始开发之前，请查看[使用媒体服务 v3 API 进行开发](media-services-apis-overview.md)（包括有关访问 API、命名约定等的信息）
- 查看以下教程：

    - [教程：根据 URL 编码远程文件并流式传输视频](stream-files-tutorial-with-rest.md)
    - [教程：上传、编码和流式处理视频](stream-files-tutorial-with-api.md)
    - [教程：通过媒体服务 v3 分析视频](analyze-videos-tutorial-with-api.md)
