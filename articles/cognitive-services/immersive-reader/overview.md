---
title: 什么是沉浸式阅读器 API？
titleSuffix: Azure Cognitive Services
description: 沉浸式阅读器 API 是一种工具，可用于使人们适应学习差异或为新读者和语言学习人员提供帮助。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 5acd6e4be178c7e645eb6855a79cd0f58303ba9a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846417"
---
# <a name="what-is-immersive-reader"></a>什么是沉浸式阅读器？

[沉浸式阅读器](https://www.onenote.com/learningtools)是一款经过广泛设计的工具，它实现了可靠的技术，以提高新出现的读者、语言学习者和有学习差异（如阅读障碍）的人的阅读理解能力。

通过使用沉浸式阅读器 SDK，可以在 Web 应用程序中使用沉浸式阅读器。

## <a name="what-does-immersive-reader-do"></a>沉浸式阅读器做什么用？

沉浸式阅读器旨在让每个人都能更方便地阅读。

* 以最小阅读视图显示内容

  ![沉浸式阅读器](./media/immersive-reader.png)

* 显示常用字词的图片

  ![图片字典](./media/picture-dictionary.png)

* 突出显示名词、动词、形容词和副词

  ![语音部件](./media/parts-of-speech.png)

* 向你大声朗读内容

  ![大声朗读](./media/read-aloud.png)

* 将内容翻译成其他语言

  ![翻译](./media/translation.png)

* 将单词分解为音节

  ![音节划分](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>沉浸式阅读器如何工作？

沉浸式阅读器是一款独立的 Web 应用，当使用沉浸式阅读器 JavaScript SDK 调用时，它会通过 `iframe` 显示在现有的 Web 应用之上。 通过调用 API 来启动沉浸式阅读器时，可以指定希望在沉浸式阅读器中显示的内容。 我们的 SDK 处理 `iframe` 的创建和样式以及与沉浸式阅读器后端服务的通信，该服务处理语音部件、文本转语音、翻译等内容。

## <a name="next-steps"></a>后续步骤

开始使用沉浸式阅读器：

* 跳转到[快速入门](./quickstart.md)
* 探索 [GitHub 上的沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)
* 阅读[沉浸式读取器 SDK 参考](./reference.md)