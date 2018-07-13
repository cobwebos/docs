---
title: Azure 内容审查器概述 | Microsoft Docs
description: 了解如何使用内容审查器跟踪、标记、评估和筛选用户生成的内容中不适合的内容。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366648"
---
# <a name="what-is-content-moderator"></a>什么是内容审查器？

内容审查是指对可能的冒犯性、不可取和危险内容进行监视的过程。 审查的内容可以是图像、文本和视频。

## <a name="where-it-is-used"></a>使用位置

以下列表显示了可以在其中使用内容审查器的几个示例方案：

- 联机市场审查产品目录和用户生成的内容
- 游戏公司审查用户生成的游戏项目和聊天室
- 社交通讯平台审查用户添加的图像、文本和视频
- 企业媒体公司为其内容实现的集中式内容审查
- K-12 教育解决方案提供商为学生和教师筛选错误和冒犯性内容

## <a name="what-it-includes"></a>组成部分

内容审查器包含多个 Web 服务 API 和内置人工干预评审工具，有助于审查图像、文本和视频。

![内容审查器块状图](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API

内容审查器包括以下 API：
  - [**文本审查 API**](text-moderation-api.md)：此 API 用于扫描文本中可能的不雅内容、明未、暗示、冒犯性和个人身份信息 (PII)。
  - [**自定义术语列表 API**](try-terms-list-api.md)：此 API 用于匹配除内置术语之外的自定义术语列表。 根据内容策略，使用这些列表阻止或允许内容。  
  - [图像审查 API****](image-moderation-api.md)：此 API 用于扫描成人图像和不雅内容，通过光学字符识别 (OCR) 功能检测图像中的文本，以及检测人脸。
  - [**自定义图像列表 API**](try-image-list-api.md)：此 API 用于匹配自定义图像列表和无需再次分类的预标识内容。
  - [**视频审查 API**](video-moderation-api.md)：此 API 用于扫描视频中潜在的成人和不雅内容。
  - [评审 API](try-review-api-job.md)：使用[作业](try-review-api-job.md)、[评审](try-review-api-review.md)和[工作流](try-review-api-workflow.md)操作在评审工具中创建和自动执行人工干预工作流。

## <a name="human-review-tool"></a>人工评审工具

内容审查器订阅包括内置[人工评审工具](Review-Tool-User-Guide/human-in-the-loop.md)。 使用前面所述的评审 API 来创建文本、图像和视频的评审，以便人工审查器可以制定最终决策。

![内容审查器评审工具](images/video-review-default-view.png)

## <a name="next-steps"></a>后续步骤

使用[快速入门](quick-start.md)以快速开始使用内容审查器。
