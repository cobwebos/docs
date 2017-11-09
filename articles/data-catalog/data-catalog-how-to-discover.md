---
title: "如何发现 Azure 数据目录中的数据源 | Microsoft Docs"
description: "本文重点说明如何发现已向 Azure 数据目录注册的数据资产，包括对 Azure 数据目录门户中的搜索和筛选以及使用命中词突出显示等功能的介绍。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 7c2a1d61bc13ba1cde404e2b732933d2d314e3e3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>如何发现 Azure 数据目录中的数据源
## <a name="introduction"></a>介绍
Azure 数据目录是一个完全托管的云服务，充当企业数据源的注册和发现系统。 换而言之，数据目录可帮助人们发现、了解和使用数据源，并帮助组织从其现有数据中获取更多价值。 数据源注册到数据目录后，其元数据将由此服务编制索引，从而用户可以轻松进行搜索，发现所需数据。

## <a name="searching-and-filtering"></a>搜索和筛选
数据目录中的发现使用两个主要机制：搜索和筛选。

搜索机制直截了当且功能强大。 默认情况下，会根据目录中的任何属性（包括用户提供的批注）来匹配搜索词。

筛选是对搜索的补充。 用户可选择具体特征，例如专家、数据源类型、对象类型和标签。 仅可查看匹配的数据资产，将搜索结果限于匹配的资产。

将搜索与筛选结合使用，可以快速导航已向数据目录注册的数据源，从而发现所需数据源。

## <a name="search-syntax"></a>搜索语法
虽然默认的自定义文本搜索简单且直观，但用户还可以使用数据目录搜索语法来更好地控制搜索结果。 数据目录搜索支持以下技术：

| 技术 | 使用 | 示例 |
| --- | --- | --- |
| 基本搜索 |使用一个或多个搜索词的基本搜索。 结果是与一个或多个指定搜索词的属性匹配的所有资产。 |`sales data` |
| 属性范围 |仅返回搜索词匹配指定属性的数据源。 |`name:finance` |
| 布尔运算符 |使用布尔运算扩大或缩小搜索范围。 |`finance NOT corporate` |
| 使用括号分组 |使用括号将部分查询分组以实现逻辑隔离，尤其是与布尔运算符结合使用时。 |`name:finance AND (tags:Q1 OR tags:Q2)` |
| 比较运算符 |针对具有数字和日期数据类型的属性使用比较运算而非相等运算。 |`modifiedTime > "11/05/2014"` |

有关数据目录搜索的详细信息，请参阅文章[ Azure 数据目录](https://msdn.microsoft.com/library/azure/mt267594.aspx)。

## <a name="hit-highlighting"></a>突出显示
查看搜索结果时，将突出显示任何与指定搜索词（如数据资产名称、说明和标记）匹配的属性，从而更轻松地确定给定搜索返回给定数据资产的原因。

> [!NOTE]
> 若要关闭命中词突出显示功能，请使用数据目录门户中的“突出显示”开关。
>
>

查看搜索结果时，即使启用了命中词突出显示，包括某个数据资产的原因也并非总是那么明确。 由于默认会搜索所有属性，可能会由于列级属性上的匹配而返回某数据资产。 而且由于多个用户可以使用自己的标记和说明对已注册的数据资产进行批注，因此并非所有元数据都会显示在搜索结果列表中。

在默认的磁贴视图中，显示在搜索结果中的每个磁贴都包括“查看搜索词匹配项”图标，因此可以快速查看匹配项数目及其位置，并根据需要跳转到它们。

 ![在 Azure 数据目录门户中命中突出显示并搜索匹配项](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>摘要
因为使用数据目录注册数据源会将结构性元数据和描述性元数据从数据源复制到目录服务，所以发现和理解数据源更简单。 注册数据源后，可通过使用数据目录门户中的筛选和搜索功能发现数据源。

## <a name="next-steps"></a>后续步骤
* 了解有关如何发现数据源的分步详细说明，请参阅 [Azure 数据目录入门](data-catalog-get-started.md)。
