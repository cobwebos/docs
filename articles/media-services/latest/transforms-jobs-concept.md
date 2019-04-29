---
title: Azure 媒体服务中的转换和作业 | Microsoft Docs
description: 使用媒体服务时，需要创建一个转换来描述处理视频的的规则或规范。 本文概述了何为转换及其使用方法。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 1c2ec576211741390ef91233101261a7881e4180
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466742"
---
# <a name="transforms-and-jobs"></a>转换和作业

本主题提供有关详细信息[转换](https://docs.microsoft.com/rest/api/media/transforms)并[作业](https://docs.microsoft.com/rest/api/media/jobs)，并解释了这些实体之间的关系。 下面的关系图展示了转换/作业工作流。

![转换](./media/encoding/transforms-jobs.png)

> [!NOTE]
> 属于日期/时间类型的转换和作业的属性始终采用 UTC 格式。

## <a name="transforms"></a>转换

**转换**可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。 单个转换可以应用多个规则。 例如，转换可以指定以给定的比特率将每个视频编码成 MP4 文件，并从该视频的第一帧生成缩略图。 针对要包含在转换中的每个规则，请添加一个 TransformOutput 条目。 可以使用媒体服务 v3 API 或者使用任何已发布的 SDK 在媒体服务帐户中创建转换。 媒体服务 v3 API 由 Azure 资源管理器驱动，因此，也可以使用资源管理器模板在媒体服务帐户中创建和部署转换。 可以使用基于角色的访问控制来锁定对转换的访问。

对[转换](https://docs.microsoft.com/rest/api/media/transforms)实体进行的更新操作旨在对基础 TransformOutputs 的说明或优先级进行更改。 建议在所有正在进行的作业都完成后执行这类更新。 如果旨在重写脚本，则需要创建新的转换。

## <a name="jobs"></a>作业

**作业**是针对 Azure 媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 **作业**指定输入视频位置和输出位置等信息。 可以使用以下各项指定输入视频的位置：HTTPS URL、SAS URL 或[资产](https://docs.microsoft.com/rest/api/media/assets)。  

使用[作业从 HTTPS 输入](job-input-from-http-how-to.md)如果你的内容已可通过 URL 访问，并且不需要在 Azure 中存储的源文件 （例如，S3 从导入）。 如果具有 Azure Blob 存储中的内容，但具有要资产中的文件不需要此方法也是合适的。 目前，此方法仅支持单个文件，用于输入。
 
使用[为作业输入资产](job-input-from-local-file-how-to.md)如果输入的内容已在资产或作为内容存储在本地文件。 它也是一个不错的选择，如果您计划发布的流式处理或下载 （假设您要将发布供下载的 mp4，但同时想要执行语音转文本或人脸检测） 的输入的资产。 此方法支持多文件资产 (例如，流式处理集本地编码的 MBR)。
 
使用事件网格监视事件可以获取作业的进度和状态。 有关详细信息，请参阅[使用事件网格监视事件](job-state-events-cli-how-to.md)。

对[作业](https://docs.microsoft.com/rest/api/media/jobs)实体进行的更新操作可以用于在作业提交之后修改说明或优先级。 仅当作业仍处于排队状态时，对优先级属性所做的更改才有效。 如果作业已开始处理或已完成，则更改优先级不起作用。

## <a name="typical-workflow"></a>典型工作流

1. 创建转换 
2. 在该转换下提交作业 
3. 列出转换 
4. 请删除将来不打算使用的转换。 

### <a name="example"></a>示例

假设希望提取所有视频的第一帧作为缩略图，应采取的步骤如下： 

1. 定义脚本或者用于处理视频的规则 -“使用视频的第一帧作为缩略图”。 
2. 对于每个视频，请告知服务： 
    1. 在何处查找该视频；  
    2. 在何处写入输出缩略图。 

**转换**可帮助你一次性创建脚本（步骤 1），然后使用该脚本提交作业（步骤 2）。

## <a name="job-error-codes"></a>作业错误代码

请参阅[错误代码](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)。

## <a name="paging"></a>Paging

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="configure-media-reserved-units"></a>配置媒体保留单位

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 个 S3 媒体保留单位 (MRU)。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

有关详细信息，请参阅[使用 CLI 调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

## <a name="next-steps"></a>后续步骤

- [教程：使用 .NET 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
- [教程：通过 .NET 使用媒体服务 v3 来分析视频](analyze-videos-tutorial-with-api.md)
