---
title: 什么是 Azure 认知服务？
titleSuffix: Azure Cognitive Services
description: Azure 认知服务是可以与 Microsoft Azure 一起用于生成智能应用程序的 API、SDK 和服务。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: article
ms.date: 04/19/2019
ms.author: nitinme
ms.openlocfilehash: dd93cd938539ae322e6344e0ce24cee6e34db27b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594752"
---
# <a name="what-are-azure-cognitive-services"></a>什么是 Azure 认知服务？

Azure 认知服务是 API、SDK 和服务，可帮助开发人员生成智能应用程序，而无需具备直接的 AI 或数据科学技能或知识。 Azure 认知服务使开发人员能够轻松地将认知功能添加到其应用程序中。 Azure 认知服务的目标是帮助开发人员创建可以看、听、说、理解甚至开始推理的应用程序。 Azure 认知服务中的服务目录可以分为五个主要的要点: 视觉、语音、语言、Web 搜索和决策。

## <a name="vision-apis"></a>视觉 API

|服务名称|服务说明|
|:-----------|:------------------|
|[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "计算机视觉")|使用计算机视觉服务，你可以访问用于处理图像并返回信息的高级算法。|
|[自定义影像服务](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "自定义影像服务")|通过自定义视觉服务可以生成自定义图像分类器。|
|[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/ "人脸 API")|使用人脸 API 可访问高级人脸算法，支持人脸属性检测和识别。|
|[窗体识别器](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "窗体识别器")效果|窗体识别器标识并提取窗体文档中的键/值对和表数据;然后输出结构化数据, 包括原始文件中的关系。|
|[手写识别器](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "手写识别器")效果|手写识别器允许识别和分析数字墨笔划数据、形状和手写内容, 并使用所有可识别的实体输出文档结构。|
|[视频索引器](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "视频索引器")|使用视频索引器从视频中提取见解。|

## <a name="speech-apis"></a>语音 API

|服务名称|服务说明|
|:-----------|:------------------|
|[语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "语音服务")|语音服务将语音支持功能添加到应用程序。|
|[说话人识别 API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "说话人识别 API")（预览版）|说话人识别 API 为说话人识别和验证提供算法。|
|[必应语音](https://docs.microsoft.com/azure/cognitive-services/speech/home "必应语音")停用|必应语音 API 为你提供了一种在应用程序中创建语音支持功能的简便方法。|
|[语音翻译](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "语音翻译")停用|语音翻译是一项机器翻译服务。|

## <a name="language-apis"></a>语言 API

|服务名称|服务说明|
|:-----------|:------------------|
|[语言理解 LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "语言理解")|使用语言理解服务 (LUIS)，应用程序可以理解用户以自己的语言表达的内容。|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|通过 QnA Maker，可以从半结构化内容生成问答服务。|
|[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "文本分析")|文本分析提供对原始文本的自然语言处理，用于情绪分析、关键短语提取和语言检测。|
|[文本翻译](https://docs.microsoft.com/azure/cognitive-services/translator/ "文本翻译")|文本翻译近乎实时地提供基于机器的文本翻译。|
|[视频索引器](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "视频索引器")|使用视频索引器从视频中提取见解。|

## <a name="search-apis"></a>搜索 API

|服务名称|服务说明|
|:-----------|:------------------|
|[必应新闻搜索](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "必应新闻搜索")|必应新闻搜索返回确定与用户查询相关的新闻文章列表。|
|[必应视频搜索](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "必应视频搜索")|必应视频搜索返回确定与用户查询相关的视频列表。|
|[必应 Web 搜索](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "必应 Web 搜索")|必应 Web 搜索返回确定与用户查询相关的搜索结果列表。|
|[必应自动建议](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "必应自动建议")|通过必应自动建议，可向必应发送部分搜索查询词，并取回建议的查询列表。|
|[必应自定义搜索](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "必应自定义搜索")|借助必应自定义搜索，可以为关注的主题创建定制的搜索体验。|
|[必应实体搜索](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "必应实体搜索")|必应实体搜索返回必应确定与用户查询相关的实体的相关信息。|
|[必应图像搜索](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "必应图像搜索")|必应图像搜索返回确定与用户查询相关的图像显示。|
|[必应视觉搜索](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "必应视觉搜索")|必应视觉搜索返回有关图像的见解，例如在视觉上相似的图像、在图像中找到的产品的购物来源以及相关的搜索。|
|[必应本地业务搜索](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "必应本地业务搜索")| 必应本地业务搜索 API 允许应用程序根据搜索查询查找有关本地企业的联系人和位置信息。|
|[必应拼写检查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "必应拼写检查")|使用必应拼写检查，可执行上下文语法和拼写检查。|

## <a name="decision-apis"></a>决策 Api

|服务名称|服务说明|
|:-----------|:------------------|
|[异常探测器](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "异常探测器")效果|异常探测器用于监视和检测时序数据中的异常。|
|[内容审查器](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "内容审查器")|内容审查器监视可能的冒犯性、不可取和危险内容。|
|[Personalizer](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer")效果|Personalizer 使你能够选择向用户显示的最佳体验, 并从其实时行为中学习。|

## <a name="use-free-trials"></a>使用免费试用版

[注册免费试用版](https://azure.microsoft.com/try/cognitive-services/ "注册帮助")只需要发送一封电子邮件并执行一些简单的步骤。 需要创建 Microsoft 帐户（如果还没有帐户）。 对于所请求的每个 API，你都会收到一对唯一的密钥。 第二个密钥只是备用密钥。 请不要与任何人分享密钥。 试用版具有每秒或每分钟事务数速率限制和每月使用上限。 事务就是 API 调用。 若要解锁这些限制，你可以升级到付费层。

## <a name="subscription-management"></a>订阅管理

使用 Microsoft 帐户登录后，你可以访问[我的订阅](https://www.microsoft.com/cognitive-services/subscriptions "我的订阅")，以显示你正在使用的产品、剩余配额以及能否向订阅添加其他产品。

## <a name="upgrade-to-unlock-limits"></a>通过升级来解锁限制

所有 Api 都具有一个具有使用和吞吐量限制的免费试用计划。  在 Azure 门户中部署服务时, 你可以通过使用付费产品和选择适当的定价层选项来增加这些限制。 [了解产品/服务和定价的详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/ "产品/服务和定价")。 你将需要使用信用卡和电话号码设置一个 Azure 订阅者帐户。 如果你有特殊要求或者只是想与销售人员交谈，请单击定价页顶部的“联系我们”按钮。

## <a name="regional-availability"></a>适用区域

认知服务中的 API 托管在不断扩大的 Microsoft 托管数据中心网络上。 你可以在 [Azure 区域列表](https://azure.microsoft.com/regions)中找到每个 API 的区域可用性。

正在寻找我们尚不支持的区域？ 请在我们的 [UserVoice 论坛](https://cognitive.uservoice.com/)上填写功能申请，告诉我们你的需求。

## <a name="supported-cultural-languages"></a>支持的区域性语言

 认知服务在服务级别支持各种区域性语言。 可以在[支持的语言列表](language-support.md)中找到每个 API 的语言可用性。

## <a name="container-support"></a>容器支持

 认知服务提供用于在 Azure 云或本地部署的容器。 详细了解[认知服务容器](cognitive-services-container-support.md)。

## <a name="certifications-and-compliance"></a>认证和符合性

认知服务已获得多种认证，例如 CSA STAR Certification、FedRAMP Moderate 和 HIPAA BAA。

可以[下载](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)认证自行进行审核和安全审查。

若要了解隐私和数据管理，请转到[信任中心](https://servicetrust.microsoft.com/)。

## <a name="support"></a>支持

* 有关支持和技术问题，请在 [Stack Overflow](https://stackoverflow.com/questions/tagged/microsoft-cognitive) 上发帖
* 有关反馈和功能申请，请转到 [UserVoice](https://cognitive.uservoice.com/)

## <a name="next-steps"></a>后续步骤

* [创建认知服务帐户](cognitive-services-apis-create-account.md)
