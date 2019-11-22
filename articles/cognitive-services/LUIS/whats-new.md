---
title: 新增功能 - 语言理解 (LUIS)
titleSuffix: Azure Cognitive Services
description: 本文会定期更新，其中包含有关 Azure 认知服务语言理解 API 的新闻。
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: e03c7489268f3ad7076481de4498c5e202f654b6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280298"
---
# <a name="whats-new-in-language-understanding"></a>语言理解中的新增功能

了解服务中的新增功能。 这些项包括发行说明、视频、博客文章和其他类型的信息。 将此页加入书签，以随时了解服务的最新信息。  

## <a name="release-notes"></a>发行说明 

### <a name="november-4-2019---ignite"></a>2019年11月4日-Ignite

* 提高开发人员工作效率
    * [预测终结点 V3](luis-migration-api-v3.md)的公开上市。 
    * 能够导入和导出应用程序（[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)）格式。 这铺平了道路了一种有效的 CI/CD 处理方式。 
* 语言扩展
    * 公共预览版中的[阿拉伯语和印地语](luis-language-support.md)。
* 预生成的模型
    * [预](luis-reference-prebuilt-domains.md)生成的域现已正式发布（GA）
    * V3 中不支持日语[预建实体](luis-reference-prebuilt-entities.md#japanese-entity-support)-年龄、货币、数字和百分比。
    * 意大利语[预生成实体](luis-reference-prebuilt-entities.md#italian-entity-support)-时间、货币、维度、数字和百分比解析从 V2 更改。
* 增强了[preview.luis.ai 门户](https://preview.luis.ai)中的用户体验-改进标记体验，以启用生成和调试复杂模型。 尝试预览门户教程：
    * [仅限意向](tutorial-intents-only.md)
    * [可以分解机器学习的实体](tutorial-machine-learned-entity.md) 
* 高级语言理解功能-采用更少的精力[构建复杂的语言模型](luis-concept-entity-types.md)。 
* 在模型级别定义机器学习功能，并允许将模型用作其他模型的信号，例如，使用实体作为功能和其他实体。
* 新的扩展[限制](luis-boundaries.md)-词组列表和短语的最大上限，新模型作为功能限制
* 以深层层次结构的格式从文本中提取信息，使会话应用程序功能更强大。

    ![计算机学习的实体图像](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019年9月3日

* Azure 创作资源-[立即迁移](luis-migration-authoring.md)。
    * 500每个 Azure 资源的应用
    * 100每个应用的版本
* 预构建实体的土耳其语支持
* 意大利语对 datetimeV2 的支持

### <a name="july-23-2019"></a>2019 年 7 月 23 日

* 将 [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 更新为 1.2.3
    * 意大利语中的年龄、温度、维度和货币识别器。
    * 改进了英语中的假期识别，以正确计算基于感恩节的日期。
    * 改进了法语日期/时间，以减少非日期和非时间实体的误报。
    * 在英文的日期范围中支持日历/学校/会计年度和首字母缩写。
    * 改进了中文和日语的 PhoneNumber 识别。
    * 改进了对英语 NumberRange 的支持。
    * 性能改进。

### <a name="june-24-2019"></a>2019 年 6 月 24 日

* [序号 V2 预生成实体](luis-reference-prebuilt-ordinal-v2.md)，以支持排序，如下一个、上一个和最后一个。 仅限英语区域性。

### <a name="may-6-2019---build-conference"></a>2019 年 5 月 6 日 - //Build 会议

以下功能是在 Build 2019 大会上发布的：

* [V3 API 预览版迁移指南](luis-migration-api-v3.md)
* [改进的分析仪表板](luis-how-to-use-dashboard.md)
* [改进的预生成域](luis-reference-prebuilt-domains.md) 
* [动态列表实体](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [外部实体](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>博客

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>视频

### <a name="2019-build-videos"></a>2019 Build 视频

[如何使用 Azure 对话 AI 为下一代扩展业务](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>服务更新

[认知服务的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
