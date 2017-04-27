---
title: "Azure 事件中心最佳错误处理实践 | Microsoft Docs"
description: "处理错误和重试的 Azure 事件中心"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>🔧 Azure 事件中心最佳错误处理和重试实践

> [!NOTE]
> 
> 此主题尚未编写！ 
>
> 欢迎输入内容，帮助撰写与此主题相关的内容。 可在 [GitHub 中就此问题](https://github.com/Azure/azure-event-hubs/issues/305)进行状态跟踪并提供输入。
> 
> 详细了解如何在 [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md) 上贡献内容。

本文将介绍：

- 有哪些不同类型的错误？
- 遇到错误时，会发生什么情况？
- 遇到哪些类型的错误时应重试？ 遇到哪些时不应重试？
- 自定义重试策略

