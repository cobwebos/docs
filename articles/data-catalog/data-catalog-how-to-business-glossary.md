---
title: "在 Azure 数据目录中为管控标记设置业务术语表 | Microsoft Docs"
description: "操作指南文章重点说明了 Azure 数据目录中的业务术语表，用于定义和使用通用业务词汇来标记已注册的数据资产。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1c0233aa398b975cef5bb346d7c6822e4a47812e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017


---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>为管控标记设置业务术语表
## <a name="introduction"></a>介绍
Azure 数据目录可用于发现数据源，使用户能够轻松发现和理解执行分析和作出决策所需的数据源。 当用户能够找到并了解最广泛的可用数据源时，这些功能可以产生最大的影响。

标记是一种能促进更好地了解资产数据的数据目录功能。 通过使用标记，可以将关键字与资产或列关联，从而可以更轻松地通过搜索和浏览发现资产。 标记还有助于更容易地理解上下文和资产的意图。

然而，标记有时会导致其自身的问题。 一些标记可能会造成的问题的示例如下：

* 在一些资产上使用缩写而在其他资产上使用展开的文本。 虽然目的是要使用相同的标记来标记资产，但这种不一致会阻碍这些资产的发现。
* 含义可能会发生变化，具体变化因上下文而异。 例如，客户数据集上名为“收入”的标记可能指客户的收入，但季度销售数据集上相同的标记可能意味着公司的季度收入。  

为了帮助解决这些问题以及其他类似的难题，数据目录包含了一个业务术语表。

通过使用数据目录业务术语表，组织可记录关键业务术语及其定义，以便创建通用业务词汇。 这种管控可使整个组织的数据使用保持一致。 在业务术语表中定义术语后，可将它分配给目录中的某个数据资产。 这种方法即“管控标记”，和标记方法相同。

## <a name="glossary-availability-and-privileges"></a>术语表可用性和特权
业务术语表仅在 Azure 数据目录标准版中可用。 数据目录免费版不包含术语表，也不提供管控标记功能。

可通过数据目录门户导航菜单中的“术语表”选项访问业务术语表。  

![访问业务术语表](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

数据目录管理员和作为术语表管理员角色的成员可以在业务术语表中创建、编辑和删除术语表术语。 所有数据目录用户都可以查看术语定义，并可以使用术语表术语对资产进行标记。

![添加新的术语表术语](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>创建术语表术语
数据目录管理员和术语表管理员可通过单击“新建术语”按钮创建术语表术语。 每个术语表术语均包含以下字段：

* 术语的业务定义
* 捕获了资产或列的预期用途或业务规则的描述
* 最了解术语的利益干系人的名单
* 定义了组织术语的层次结构的父术语

## <a name="glossary-term-hierarchies"></a>术语表术语层次结构
通过使用数据目录业务术语表，组织可将其业务词汇表现为术语层次结构，也可以创建能够更好地表现业务分类的术语类别。

在给出的层次结构级别里，术语必须是唯一的。 不允许重复的名称。 层次结构中的级别数没有限制，但是三个或更少级别的层次结构通常更易于理解。

可以选择在业务术语表中使用层次结构。 将术语表术语的父术语字段留空，这会在术语表中创建一个简单（而非分层）术语列表。  

## <a name="tagging-assets-with-glossary-terms"></a>使用术语表术语标记资产
在目录中定义了术语表术语后，优化了标记资产的体验，以在用户键入其标记时搜索术语表。 数据目录门户显示一个匹配的术语表术语列表以供选择。 如果用户从列表中选择一个术语表术语，则会将其作为标记添加到资产中（也称为术语标记）。 用户还可以选择通过键入不在术语表中的术语来创建新标记（也称为用户标记）。

![标记有一个用户标记和两个术语表标记的数据资产](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> 用户标记是数据目录免费版中唯一支持的标记类型。
>
>

### <a name="hover-behavior-on-tags"></a>标记上的悬停行为
在数据目录门户中，这两种类型的标记看起来有所不同，具有不同的悬停行为。 将鼠标悬停在用户标记上时，可以看到标记文本和添加了该标记的一名或多名用户。 将鼠标悬停在术语表标记上时，还可以看到术语表术语的定义，以及用于打开业务术语表以查看该术语的完整定义的链接。

### <a name="search-filters-for-tags"></a>标记的搜索筛选器
术语表标记和用户标记均可搜索，可以在搜索中应用为筛选器。

## <a name="summary"></a>摘要
使用 Azure 数据目录中的业务术语表及其启用的管控标记，可以以一致的方式识别、管理和发现数据资产。 业务术语表可促进组织成员学习业务词汇。 术语表也支持捕获有意义的元数据，这可以简化资产的发现和理解。

## <a name="next-steps"></a>后续步骤
* [用于业务术语表操作的 REST API 文档](https://msdn.microsoft.com/library/mt708855.aspx)

