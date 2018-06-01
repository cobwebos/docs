---
title: Azure 媒体服务中的转换和作业 | Microsoft Docs
description: 使用媒体服务时，需要创建一个转换来描述处理视频的的规则或规范。 本文概述了何为转换及其使用方法。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: b755e0573098d3dbed1bea18a40af634be609f76
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272074"
---
# <a name="transforms-and-jobs"></a>转换和作业

## <a name="overview"></a>概述 

Azure 媒体服务 REST API (v3) 的最新版本引入了用于对视频进行编码和/或分析的新模板化工作流资源，名为“转换”。 转换可用来配置对视频进行编码或分析的常见任务。 每个转换描述了处理视频或音频文件的任务的简单工作流。 

转换对象是脚本，作业是针对 Azure 媒体服务的实际请求，将该转换应用到给定的输入视频或音频内容。 作业指定输入视频位置和输出位置等信息。 可以使用以下方法指定视频位置：HTTP URL、SAS URL 或位于本地或 Azure Blob 存储中的文件的路径。 Azure 媒体服务帐户中最多可具有 100 个转换，并能在这些转换下提交作业。 然后可以使用通知订阅事件（如作业状态更改），其中通知直接与 Azure 事件网格通知系统集成。 

由于此 API 通过 Azure 资源管理器驱动，因此可以使用资源管理器模板来创建和部署媒体服务帐户中的转换。 还可以在此 API 中以资源级别设置基于角色的访问控制，从而锁定对特定资源（如转换）的访问。

## <a name="transform-definition"></a>转换定义

下表显示了转换的属性并给出了它们的定义。

|名称|Type|说明|
|---|---|---|
|ID|字符串|资源的完全限定的资源 ID。|
|名称|字符串|资源的名称。|
|properties.created |字符串|创建转换时的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.description |字符串|转换的可选详细说明。|
|properties.lastModified |字符串|上次更新转换的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.outputs |TransformOutput[]|转换应该生成的一个或多个 TransformOutput 的数组。|
|type|字符串|资源的类型。|

有关完整定义，请参阅[转换](https://docs.microsoft.com/rest/api/media/transforms)。

## <a name="job-definition"></a>作业定义

下表显示了作业的属性并给出了它们的定义。

|名称|Type|说明|
|---|---|---|
|ID|字符串|资源的完全限定的资源 ID。|
|名称|字符串|资源的名称。|
|properties.created |字符串|创建转换时的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.description |字符串|作业的可选详细说明。|
|properties.lastModified |字符串|上次更新转换的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.outputs |JobOutput[]:JobOutputAsset[] |作业的输出。|
|properties.priority |Priority |处理作业的优先级。 较高优先级的作业在较低优先级的作业之前处理。 如果未设置，则默认为正常。
|properties.state |JobState |作业的当前状态。
|type|字符串|资源的类型。|

有关完整定义，请参阅[作业](https://docs.microsoft.com/rest/api/media/jobs)。

## <a name="typical-workflow-and-example"></a>典型工作流和示例

1. 创建转换 
2. 在该转换下提交作业 
3. 列出转换 
4. 如果不打算在将来使用此“脚本”，请删除转换。 

假设希望提取所有视频的第一帧作为缩略图，应采取的步骤如下： 

1. 定义处理规则 - 例如，使用视频的第一帧作为缩略图 
2. 然后，对于每个作为服务输入的视频，需告知该服务： 
    1. 查找视频的位置，以及 
    2. 写入输出的位置 - 例如，缩略图 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [流式处理视频文件](stream-files-dotnet-quickstart.md)
