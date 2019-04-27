---
title: Web API 接口 - 知识探索服务 API
titlesuffix: Azure Cognitive Services
description: 使用 Web API 接口，可以在知识探索服务 (KES) API 中创建丰富的语义搜索体验。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814435"
---
# <a name="web-api-interface"></a>Web API 接口

可以通过一组 Web API 托管和访问知识探索服务生成的模型文件。  可以使用 [`host_service`](CommandLine.md#host_service-command) 命令在本地计算机上托管 API，也可以使用 [`deploy_service`](CommandLine.md#deploy_service-command) 命令将 API 部署到 Azure 云服务。  这两种技术都公开以下 API 终结点：

* [interpret](interpretMethod.md) - 解释自然语言查询字符串。 返回批注版解释，以实现可预测用户输入内容的丰富搜索框自动完成体验。
* [evaluate](evaluateMethod.md) - 计算并返回结构化查询表达式的输出。
* [calchistogram](calchistogramMethod.md) - 计算结构化查询表达式返回的对象的属性值直方图。

综合使用这些 API 方法，可以创建丰富的语义搜索体验。  在自然语言查询字符串给定的情况下，interpret 方法根据基础语法和索引数据，提供包含结构化查询表达式的批注版输入查询。  evaluate 方法计算结构化查询表达式，并返回匹配索引对象以供显示。  calchistogram 方法计算属性值分布，以启用筛选和优化。

**示例**

在学术出版物领域中，如果用户键入字符串“latent s”，interpret 方法可以提供一组排名解释，同时提示用户可能正在搜索关键字“latent semantic analysis”（潜在语义分析）、标题“latent structure analysis”（潜在结构分析）或以“latent s”开头的其他表达式。  此信息可用于快速引导用户获得所需搜索结果。

对于此域，evaluate 方法可用于从学术索引中检索一组匹配出版物，calchistogram 方法可用于计算匹配出版物的属性值分布，这可用于进一步筛选和优化搜索结果。

请注意，REST API 调用包含未经 URL 编码的字符（如空格）是为了提高示例的可读性。 你的代码需要应用适当的 URL 编码。
