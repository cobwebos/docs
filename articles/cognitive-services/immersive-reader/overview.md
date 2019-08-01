---
title: 什么是沉浸式阅读器 API？
titleSuffix: Azure Cognitive Services
description: 了解沉浸式阅读器 API。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f84804fbad07357706e786fc449fce9e42a5ec4a
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688334"
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