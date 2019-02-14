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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: e84f74fe4678a65a33c9cc728f290e7c905b2261
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743729"
---
# <a name="transforms-and-jobs"></a>转换和作业
 
[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。 单个转换可以应用多个规则。 例如，转换可以指定以给定的比特率将每个视频编码成 MP4 文件，并从该视频的第一帧生成缩略图。 针对要包含在转换中的每个规则，请添加一个 TransformOutput 条目。 可以使用媒体服务 v3 API 或者使用任何已发布的 SDK 在媒体服务帐户中创建转换。 媒体服务 v3 API 由 Azure 资源管理器驱动，因此，也可以使用资源管理器模板在媒体服务帐户中创建和部署转换。 可以使用基于角色的访问控制来锁定对转换的访问。

对[转换](https://docs.microsoft.com/rest/api/media/transforms)实体进行的更新操作旨在对基础 TransformOutputs 的说明或优先级进行更改。 建议在所有正在进行的作业都完成后执行这类更新。 如果旨在重写脚本，则需要创建新的转换。

[作业](https://docs.microsoft.com/rest/api/media/jobs)是针对 Azure 媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 **作业**指定输入视频位置和输出位置等信息。 可以使用以下各项指定输入视频的位置：HTTPS URL、SAS URL 或[资产](https://docs.microsoft.com/rest/api/media/assets)。 使用事件网格监视事件可以获取作业的进度和状态。 有关详细信息，请参阅[使用事件网格监视事件](job-state-events-cli-how-to.md)。

对[作业](https://docs.microsoft.com/rest/api/media/jobs)实体进行的更新操作可以用于在作业提交之后修改说明或优先级。 仅当作业仍处于排队状态时，对优先级属性所做的更改才有效。 如果作业已开始处理或已完成，则更改优先级不起作用。

> [!NOTE]
> 属于日期/时间类型的转换和作业的属性始终采用 UTC 格式。

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

## <a name="paging"></a>分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

[对视频文件进行上传、编码和流式处理](stream-files-tutorial-with-api.md)
