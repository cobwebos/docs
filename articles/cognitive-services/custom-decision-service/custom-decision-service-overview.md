---
title: 什么是自定义 AI 决策服务？ - Azure 认知服务 | Microsoft Docs
description: 本文简要介绍 Azure 自定义 AI 决策服务，它是一种基于云的 API，用于进行上下文决策，同时可增强体验。
services: cognitive-services
author: alekh
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 774467446513dcd7ade7255d998b11f41824cafe
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "35367002"
---
# <a name="what-is-custom-decision-service"></a>什么是自定义 AI 决策服务？

在典型的 Web 或移动应用程序中，通过首页可链接至一些文章或其他类型的内容。 加载首页时，它可能请求自定义 AI 决策服务对该首页包含的文章设置优先级。 因此，当用户单击选中文章时，下一个请求可能会被发送至自定义 AI 决策服务，自定义 AI 决策服务将记录用户的决定结果。

自定义 AI 决策服务易于使用，因为它只需要要添加到应用程序中的内容的 RSS 源和几行 JavaScript。

自定义 AI 决策服务可将内容转换为用于机器学习的功能。 系统使用这些功能来通过文本、图像、视频和整体情绪层面理解内容。 它使用几种其他的 [Microsoft 认知服务](https://www.microsoft.com/cognitive-services)，例如[实体链接](../entitylinking/home.md)、[文本分析](../text-analytics/overview.md)、[情感](../emotion/home.md)以及[计算机视觉](../computer-vision/home.md)。

以下是自定义 AI 决策服务的一些常见用例：

* 对新闻网站上的文章进行个性化设置
* 对媒体门户上的视频内容进行个性化设置
* 优化广告布局或广告指向的网页
* 对购物网站上的推荐产品设置优先级。

自定义 AI 决策服务目前提供免费公共预览版。 它可以对网站或应用上的大量文章进行个性化设置。 特征提取功能最适用于英语内容。 目前为其他语言提供[有限的功能](../text-analytics/overview.md)，例如西班牙语、法语、德语、葡萄牙语和日语。 本文档将在新功能可用时进行修订。

自定义 AI 决策服务可用于内容个性化域以外的应用程序。 这些应用程序也许非常适合自定义预览。 若要了解详细信息，请[联系我们](https://azure.microsoft.com/overview/sales-number/)。

## <a name="api-usage-modes"></a>API 使用模式

自定义 AI 决策服务可用于网页和移动应用。 可从浏览器或应用调用 API。 两种情况下的 API 用法很相似，但在某些细节上存在区别。

## <a name="glossary-of-terms"></a>术语词汇表

本文档中频繁出现了几个术语：

* **操作集**：自定义 AI 决策服务对其设置优先级的内容项集。 可将此集指定为 RSS 或 Atom 终结点。
* **设置优先级**：向自定义 AI 决策服务发送的每个请求都指定一个或多个操作集。 系统通过选取这些操作集中的所有内容选项做出响应，然后按优先级顺序将其返回。
* **回调函数**：此函数由你指定，可呈现 UI 中的内容。 该内容按自定义 AI 决策服务返回的优先级顺序进行排列。
* **回馈**：用户对呈现内容的反应情况的度量值。 自定义 AI 决策服务通过点击数来衡量用户的反应情况。 点击数是通过使用插入到应用程序中的自定义代码报告给系统的。

## <a name="next-steps"></a>后续步骤

* 使用自定义 AI 决策服务[注册应用程序](custom-decision-service-get-started-register.md)
* 开始优化[网页](custom-decision-service-get-started-browser.md)或[智能手机应用](custom-decision-service-get-started-app.md)。
* 请参阅 [API 参考](custom-decision-service-api-reference.md)详细了解提供的功能。