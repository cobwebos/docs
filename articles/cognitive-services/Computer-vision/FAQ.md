---
title: 常见问题解答 - 计算机视觉
titlesuffix: Azure Cognitive Services
description: 获取 Azure 认知服务中计算机视觉 API 的常见问题解答。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 579a47e70f292222914723606d032737b98822bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498774"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>计算机视觉 API 常见问题解答

> [!TIP]
> 如果本 FAQ 无法解答你的问题，请尝试在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上的计算机视觉 API 服务社区中提问，或在 [UserVoice](https://cognitive.uservoice.com/) 上联系帮助和支持人员。

-----

**问**：我能否将计算机视觉 API 定型为使用自定义标记？例如，我要提供猫品种图片来“定型”AI，然后通过 AI 请求接收品种值。

**答**：此功能目前不可用。 不过，我们的工程师正在努力将此功能引入计算机视觉。

-----

**问**：*能否在未连接 Internet 的情况下本地使用计算机视觉？*

**答**：我们目前未提供本地解决方案。

-----

**问**：*计算机视觉能否用于读取牌照？*

**答**：视觉 API 可通过 OCR 有效检测文本，但暂不适合读取牌照。 我们一直在努力改进服务，并已将用于自动识别牌照的 OCR 添加到功能请求列表中。

-----

**问**：*手写识别支持什么类型的书写表面？*

**答**：此技术支持不同种类的表面，包括白板、白纸和黄色便笺。

-----

**问**：*手写识别操作需要多长时间才能完成？*

**答**：所需时间取决于文本长度。 对于较长文本，最长可能需要几秒钟。 因此，在“识别手写文本”操作完成后，可能需要先等待，然后才能使用“获取手写文本操作结果”操作检索结果。

-----

**问**：*手写识别技术如何处理使用插入点在一行中间插入的文本？*

**答**：手写识别操作将此类文本作为单独文本行返回。

-----

**问**：*手写识别技术如何处理划掉的字词或文本行？*

**答**：如果字词是用多条线划掉，导致其无法识别，那么手写识别操作不会拾取这些字词。 不过，如果字词是用一条线划掉，那么划线就会被视为干扰，并且手写识别操作仍会拾取这些字词。

-----

**问**：*手写识别技术支持哪些文本方向？*

**答**：手写识别操作最多可拾取方向介于约 30 度到 40 度角度范围内的文本。

-----
