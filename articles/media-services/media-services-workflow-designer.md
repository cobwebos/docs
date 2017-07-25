---
title: "使用工作流设计器创建高级编码工作流 | Microsoft Docs"
description: "了解如何使用工作流设计器创建高级编码工作流。"
services: media-services
documentationcenter: 
author: anilmur
manager: erikre
editor: 
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako;johndeu;anilmur
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 74916c9da0c1d71395a087a729513110b663738b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>使用工作流设计器创建高级编码工作流
## <a name="overview"></a>概述
**工作流设计器**是一个 Windows 桌面工具，可用于设计和构建自定义工作流，以使用**媒体编码器高级工作流**进行编码。
借助工作流设计器工具的强大功能，你可以设计和创建将在**媒体编码器高级**中运行的复杂工作流。  

根据输入源文件的属性，工作流可以包含客户决策逻辑和分支。 你可以创建具有可重写属性与动态值的工作流，让即使最复杂的编码任务也能在云中轻松重复及自定义。

你可以创建的工作流示例包括：

* 基于决策的工作流，该类工作流将检查要解析的源内容，并且仅编码所需的输出跟踪。  这有助于消除因无意中增加源内容而生成的多余跟踪。
* 可使用多个输入文件来支持标题、覆盖和拼结在一起的内容。 

还可以使用此工具修改任何[已发布工作流](media-services-workflow-designer.md#existing_workflows)。 

> [!NOTE]
> 若要获取工作流设计器工具的副本，请联系 mepd@microsoft.com。
> 
> 

创建工作流文件后，可将其作为资产上传，然后用于对媒体文件编码。 有关如何使用 **.NET** 通过**媒体编码器高级工作流**编码的信息，请参阅[使用媒体编码器高级工作流进行高级编码](media-services-encode-with-premium-workflow.md)。

## <a id="existing_workflows"></a>修改现有工作流
可以使用设计器工具修改默认的[已发布工作流](media-services-workflow-designer.md#existing_workflows)。 可以从[此处](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)获取默认工作流文件。 该文件夹还包含这些文件的相关说明。

以下视频演示如何使用设计器。

### <a name="day-1--getting-started"></a>第 1 天 — 入门
第 1 天视频涵盖：

* 设计器概述
* 基本工作流 —“Hello World”
* 创建多个用于 Azure 媒体服务流式处理的输出 MP4 文件

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>第 2 天
第 2 天视频涵盖：

* 不同的源文件应用场景 — 处理音频
* 具有高级逻辑的工作流
* 图形阶段

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>第 3 天
第 3 天视频涵盖：

* 工作流/蓝图中的脚本
* 当前编码器的限制
* 问答

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

如果你需要支持或对在工作流设计器工具中创建自定义工作流有疑问，请发送电子邮件到 mepd@microsoft.com。

## <a name="see-also"></a>另请参阅
[Azure 高级编码器工作流设计器培训视频](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)


