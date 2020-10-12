---
title: 媒体服务中的转换和作业
titleSuffix: Azure Media Services
description: 了解如何创建一个转换来描述在 Azure 媒体服务中处理视频的的规则。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: inhenkel
ms.openlocfilehash: a9e34c4aaeb6fc2c151233b05ed5584e0e624148
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297971"
---
# <a name="transforms-and-jobs-in-media-services"></a>媒体服务中的转换和作业

本主题提供有关[转换](/rest/api/media/transforms)和[作业](/rest/api/media/jobs)的详细信息，并解释这些实体之间的关系。

## <a name="overview"></a>概述

### <a name="transformsjobs-workflow"></a>转换/作业工作流

下面的关系图展示了转换/作业工作流：

![Azure 媒体服务中的转换和作业工作流](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>典型工作流

1. 创建转换。
2. 在该转换下提交作业。
3. 列出转换。
4. 请删除将来不打算使用的转换。

#### <a name="example"></a>示例

假设希望提取所有视频的第一帧作为缩略图，应采取的步骤如下：

1. 定义脚本或者用于处理视频的规则：“使用视频的第一帧作为缩略图”。
2. 对于每个视频，请告知服务：
    1. 在何处查找该视频。
    2. 在何处写入输出缩略图。

**转换**可帮助你一次性创建脚本（步骤 1），然后使用该脚本提交作业（步骤 2）。

> [!NOTE]
> 属于日期/时间类型的转换和作业的属性始终采用 UTC 格式。

## <a name="transforms"></a>转换

**转换**可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。 单个转换可以应用多个规则。 例如，转换可以指定以给定的比特率将每个视频编码成 MP4 文件，并从该视频的第一帧生成缩略图。 针对要包含在转换中的每个规则，请添加一个 TransformOutput 条目。 使用预设来告知转换要如何处理输入媒体文件。

### <a name="viewing-schema"></a>查看架构

在媒体服务 v3 中，预设是 API 本身中的强类型化实体。 可以在[开放 API 规范（或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到这些对象的“架构”定义。 也可以在 [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset)、[.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) 或其他媒体服务 v3 SDK 参考文档中查看预设定义（例如 **StandardEncoderPreset**）。

### <a name="creating-transforms"></a>创建转换

可以使用 REST、CLI 或任何已发布的 SDK 来创建转换。 媒体服务 v3 API 由 Azure 资源管理器驱动，因此，也可以使用资源管理器模板在媒体服务帐户中创建和部署转换。 可以使用基于角色的访问控制来锁定对转换的访问。

### <a name="updating-transforms"></a>更新转换

如果需要更新[转换](/rest/api/media/transforms)，请使用“更新”操作。 此操作旨在对底层 TransformOutputs 的说明或优先级进行更改。 建议在所有正在进行的作业都完成后执行这类更新。 如果你想要重写脚本，则需要创建新的转换。

### <a name="transform-object-diagram"></a>转换对象关系图

下图显示了**转换**对象及其引用的对象，包括派生关系。 灰色箭头显示作业引用的类型，绿色箭头显示类派生关系。

选择图像查看其完整大小。  

[![图显示了 Transform 对象及其引用的对象，包括对象之间的类派生关系。](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>作业

**作业**是针对媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 Job 指定输入视频位置和输出位置等信息。 可以使用以下各项指定输入视频的位置：HTTPS URL、SAS URL 或[资产](/rest/api/media/assets)。  

### <a name="job-input-from-https"></a>来自 HTTPS 的作业输入

如果内容已经可以通过 URL 进行访问，并且你不需要将源文件存储在 Azure 中（例如，从 S3 导入），请使用[来自 HTTPS 的作业输入](job-input-from-http-how-to.md)。 如果你的内容已存储在 Azure Blob 存储中，但不需要将文件存储在资产中，则此方法也适用。 目前，此方法仅支持使用单个文件作为输入。

### <a name="asset-as-job-input"></a>资产作为作业输入

如果输入内容已在资产中，或者内容已存储在本地文件中，请使用[资产作为作业输入](job-input-from-local-file-how-to.md)。 如果你打算发布输入资产以供流式传输或下载（例如，要发布 MP4 文件以供下载，同时想要执行语音转文本或人脸检测），这也是一个不错的选择。 此方法支持多文件资产（例如，在本地编码的 MBR 流集）。

### <a name="checking-job-progress"></a>检查作业进度

使用事件网格监视事件可以获取作业的进度和状态。 有关详细信息，请参阅[使用事件网格监视事件](job-state-events-cli-how-to.md)。

### <a name="updating-jobs"></a>更新作业

对[作业](/rest/api/media/jobs)实体进行的更新操作可以用于在作业提交之后修改说明或优先级。 仅当作业仍处于排队状态时，对优先级属性所做的更改才有效。 如果作业已开始处理或已完成，则更改优先级不起作用。

### <a name="job-object-diagram"></a>作业对象关系图

下图显示了**作业**对象及其引用的对象，包括派生关系。

单击图像查看其完整大小。  

[![图显示了 Job 对象及其引用的对象，包括对象之间的类派生关系。](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>配置媒体预留单位

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 个 S3 媒体预留单位 (MRU)。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

有关详细信息，请参阅[使用 CLI 调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="see-also"></a>另请参阅

* [错误代码](/rest/api/media/jobs/get#joberrorcode)
* [媒体服务实体的筛选、排序和分页](entities-overview.md)

## <a name="next-steps"></a>后续步骤

- 在开始开发之前，请查看[使用媒体服务 v3 API 进行开发](media-services-apis-overview.md)（包括有关访问 API、命名约定等的信息）
- 查看以下教程：

    - [教程：基于 URL 对远程文件进行编码并流式传输视频](stream-files-tutorial-with-rest.md)
    - [教程：上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
    - [教程：使用媒体服务 v3 来分析视频](analyze-videos-tutorial-with-api.md)
