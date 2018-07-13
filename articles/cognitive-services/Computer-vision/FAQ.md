---
title: 计算机视觉 API FAQ | Microsoft Docs
description: 获取 Microsoft 认知服务中计算机视觉 API 的常见问题解答。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c862dd2fb26a005f4e785673a4e9358ecf9286f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365448"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>计算机视觉 API 常见问题解答
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>如果本 FAQ 无法解答你的问题，请尝试在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上的计算机视觉 API 服务社区中提问，或在 [UserVoice](https://cognitive.uservoice.com/) 上联系帮助和支持人员。

-----

**问**：我能否将计算机视觉 API 定型为使用自定义标记？例如，我要提供猫品种图片来“定型”AI，然后通过 AI 请求接收品种值。

**答**：此功能暂不可用。 不过，我们的工程师正在努力将此功能引入计算机视觉。

-----

**问**：能否在未连接 Internet 的情况下本地使用计算机视觉？

**答**：我们暂不提供本地解决方案。

-----

**问**：计算机视觉支持哪些语言？

**答**：支持的语言包括：

| | | 支持的语言 | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| 丹麦语 (da-DK)  | 荷兰语 (nl-NL)     | 英语           | 芬兰语 (fi-FI)            |法语 (fr-FR)
| 德语 (de-DE)  | 希腊语 (el-GR)     | 匈牙利语 (hu-HU) | 意大利语 (it-IT)            | 日语 (ja-JP)
| 韩语 (ko-KR)  | 挪威语 (nb-NO) | 波兰语 (pl-PL)    | 葡萄牙语 (pt-BR) (pt-PT) | 俄语 (ru-RU)
| 西班牙语 (es-ES)   | 瑞典语 (sv-SV)     | 土耳其语 (tr-TU)   |                            |

-----

**问**：计算机视觉能否用于读取牌照？

**答**：视觉 API 可通过 OCR 有效检测文本，但暂不适合读取牌照。 我们一直在努力改进服务，并已将用于自动识别牌照的 OCR 添加到功能请求列表中。

-----

**问**：手写识别支持哪些语言？

**答**：暂仅支持英语。

-----

**问**：手写识别支持什么类型的书写表面？

**答**：此技术支持不同种类的表面，包括白板、白纸和黄色便笺。

-----

**问**：手写识别操作需要多长时间才能完成？

**答**：所需时间取决于文本长度。 对于较长文本，最长可能需要几秒钟。 因此，在“识别手写文本”操作完成后，可能需要先等待，然后才能使用“获取手写文本操作结果”操作检索结果。

-----

**问**：手写识别技术如何处理使用插入点在一行中间插入的文本？

**答**：手写识别操作将此类文本作为单独文本行返回。

-----

**问**：手写识别技术如何处理划掉的字词或文本行？

**答**：如果字词是用多条线划掉，导致其无法识别，那么手写识别操作不会拾取这些字词。 不过，如果字词是用一条线划掉，那么划线就会被视为干扰，并且手写识别操作仍会拾取这些字词。

-----

**问**：手写识别技术支持哪些文本方向？

**答**：手写识别操作最多可拾取方向介于约 30 度到 40 度角度范围内的文本。

-----
