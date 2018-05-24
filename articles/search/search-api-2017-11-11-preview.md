---
title: Azure 搜索服务 REST API 版本 2017-11-11-Preview | Microsoft Docs
description: Azure 搜索服务 REST API 版本 2017-11-11-Preview 包括试验功能，如同义词和 moreLikeThis 搜索。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2018
ms.author: HeidiSteen
ms.openlocfilehash: 3a9ff6697357dec443691b261ae6fc0477603a9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786716"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure 搜索服务 REST API：版本 2017-11-11-Preview
本指南介绍了 `api-version=2017-11-11-Preview` 版本的 Azure 搜索服务 REST API，提供以下实验性功能：

+ [认知搜索](cognitive-search-concept-intro.md)是 Azure 搜索索引中的新扩充功能，用于查找非文本源和无差别文本中的潜在信息，将其转换为 Azure 搜索中的可全文搜索内容。

预览 API 等效于正式版 API，以下两个操作除外：

+ [创建技能集 (api-version=2017-11-11-Preview)](ref-create-skillset.md)
+ [创建索引器 (api-version=2017-11-11-Preview)](ref-create-indexer.md)

在评估预发布功能时务必以预览 API 版本 `api-version=2017-11-11-Preview` 为目标。 以下示例语法说明了对预览 API 版本的调用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

> [!NOTE]
> 预览功能可用于目的为收集反馈的测试和试验，可能随时更改。 **我们强烈建议不要在生产应用程序中使用预览版 API。**

Azure 搜索服务在多个版本内可用。 请参阅 [API 版本](search-api-versions.md)获取详细信息。
