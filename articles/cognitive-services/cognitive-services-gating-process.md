---
title: 认知服务的过程
titleSuffix: Azure Cognitive Services
description: 了解如何申请提前访问新的认知服务容器和 Api。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599509"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Azure 认知服务的过程

> [!NOTE]
> 服务产品完成封闭预览版后，将进入 "无选通" 公共预览，无需访问应用程序。 预览流程完成后，它将作为公开发布（GA）发布。

随着新的 Azure 认知服务产品的推出，他们会经历一个封闭预览版，客户必须通过应用程序请求访问。 此培训过程可帮助确定服务产品的改进机会，然后才能广泛使用。 

本文将指导你完成封闭认知服务产品的应用过程。

## <a name="eligibility-and-approval-process"></a>资格和审批流程

实施了该过程，有助于估量兴趣并更好地了解客户需求。 Microsoft 团队接受使用有效 Azure 订阅和有效业务方案的 Microsoft 商业客户提供的[应用程序](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)。 当以下情况时，客户可能会拒绝其应用程序：

 - 它们不与任何组织关联
 - 它们没有有效的 Azure 订阅
 - 应用程序通过个人电子邮件提交（ @hotmail.com 、 @gmail.com 、 @yahoo.com ）
 - 未提供适当的理由或业务方案

由于来自不同客户群的需求，我们正在努力加快批准过程。 但是，我们无法提交到时间线。 做出决定后，Microsoft 团队将与你和你的帐户管理团队联系，以了解后续步骤。 非常感谢您的理解和耐心。

如果已批准应用程序，Microsoft 团队将发送一封电子邮件，其中包含详细信息、文档和指南。 可[在此处](https://azure.microsoft.com/pricing/details/cognitive-services/)查看认知服务定价详细信息。


目前，以下服务是通过指导过程提供的：

## <a name="gated-web-apis"></a>封闭 web Api

|服务  |
|---------|
|异常探测器 v2     | 

## <a name="gated-online-containers"></a>封闭联机容器

| 服务                             | 容器                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [计算机视觉][cv-containers]    | 读取                                                                          |
| [人脸][fa-containers]               | 人脸                                                                          |
| [表单识别器][fr-containers]    | 表单识别器                                                               |
| [语音服务 API][sp-containers] | 语音到文本（自定义和标准）和文本到语音（自定义和标准） |
| [文本翻译][tt-containers]    | 文本翻译                                                               |

> [!IMPORTANT]
> 如果服务或容器化产品/服务未列出，则不是封闭服务或不可用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [注册封闭服务](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
