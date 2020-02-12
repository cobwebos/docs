---
title: 新增功能-语言理解（LUIS）
description: 本文会定期更新，其中包含有关 Azure 认知服务语言理解 API 的新闻。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 716860b54e7d8e75984c0365cac61d14153c09ff
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137808"
---
# <a name="whats-new-in-language-understanding"></a>语言理解中的新增功能

了解服务中的新增功能。 这些项包括发行说明、视频、博客文章和其他类型的信息。 将此页加入书签，以随时了解服务的最新信息。

## <a name="release-notes"></a>发行说明

### <a name="november-4-2019---ignite"></a>2019年11月4日-Ignite

* 视频-[使用 LUIS 和 Azure 认知服务的高级自然语言理解（NLU）模型 |BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

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

### <a name="july-23-2019"></a>2019年7月23日

* 将[识别器文本](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3)更新为1.2。3
    * 意大利语的年龄、温度、维度和币种识别器。
    * 为了正确计算基于感恩节的日期，可以改进英语的假日识别。
    * 法语日期时间的改进，可减少非日期和非时间实体的误报。
    * 支持在英文 DateRange 中提供日历/学校/财政年度和首字母缩写词。
    * 改进了中文和日语的 PhoneNumber 识别。
    * 改进了对英语 NumberRange 的支持。
    * 性能改进。

### <a name="june-24-2019"></a>2019年6月24日

* [OrdinalV2 预先](luis-reference-prebuilt-ordinal-v2.md)生成的实体，以支持排序，如下一个、上一个和最后一个。 仅限英语区域性。

### <a name="may-6-2019---build-conference"></a>5月6日，2019大会 build

以下功能已在生成2019会议上发布：

* [V3 API 迁移指南预览](luis-migration-api-v3.md)
* [改进的分析仪表板](luis-how-to-use-dashboard.md)
* [改进的预构建域](luis-reference-prebuilt-domains.md)
* [动态列表实体](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [外部实体](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>博客

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>视频

### <a name="2019-ignite-videos"></a>2019 Ignite 视频

[使用 LUIS 和 Azure 认知服务的高级自然语言理解（NLU）模型 |BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019生成视频

[如何使用 Azure 对话 AI 为下一代扩展业务](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>服务更新

[认知服务的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
