---
title: "如何为管控标记设置业务术语表 | Microsoft Docs"
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
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8a528f8bccaeb55851ad550aee1da93bf4876730
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>如何为管控标记设置业务术语表
## <a name="introduction"></a>介绍
Azure 数据目录提供了数据源发现的功能，使用户能够轻松发现和理解他们执行分析和做出决策所需的数据源。 当用户能够找到并了解最广泛的可用数据源时，这些发现功能可以产生最大的影响。

标记是一种能促进更好地了解资产数据的数据目录功能。 标记允许用户将关键字与资产或列相关联，这使通过搜索或浏览来发现资产变得更容易，并且让用户能更容易地理解资产的上下文和目的。

然而，标记有时会导致其自身的问题。 一些标记可能会造成的问题的示例如下：

1. 用户进行标记时，在一些资产上使用了缩写，而在其他资产上使用了展开的文本。 虽然目的是要使用相同的标签来标记资产，但这种不一致会阻碍这些资产的发现。
2. 在不同上下文中有不同含义的标签。 例如，客户数据集上名为“收入”的标记可能指客户的收入，但季度销售数据集上相同的标记可能意味着公司的季度收入。  

为了帮助解决这些问题以及其他类似的难题，数据目录包含了一个业务术语表。

数据目录业务术语表允许组织记录关键的业务术语及其定义，创建通用业务词汇。 这种管控可使整个组织的数据使用保持一致。 在业务术语表中定义术语后，可以将它们分配到目录中的数据资产，标记时使用相同的方法，从而启用“管控标记”。

> [!NOTE]
> 本文中描述的功能仅在 Azure 数据目录标准版中可用。 免费版不提供管控标记或业务术语表功能。
>
>

## <a name="glossary-availability-and-privileges"></a>术语表可用性和特权
业务术语表在 Azure 数据目录标准版中可用。*数据目录免费版不包括术语表。*

可通过数据目录门户导航菜单中的“术语表”选项访问业务术语表。  

![访问业务术语表](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

数据目录管理员和术语表管理员角色的成员可以在业务术语表中创建、编辑和删除术语表术语。 所有数据目录用户都可以查看术语定义，并可以使用术语表术语对资产进行标记。

![添加新的术语表术语](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>创建术语表术语
通过单击“新建术语”按钮，数据目录管理员和术语表管理员可以创建新的术语表术语，以创建具有以下字段的新术语表术语：

* 术语的业务定义
* 捕获了资产/列的预期目的或业务规则的描述
* 最了解术语的利益干系人的名单
* 定义了组织术语的层次结构的父术语

## <a name="glossary-term-hierarchies"></a>术语表术语层次结构
数据目录业务术语表提供将业务词汇描述为术语层次结构的能力。 这使组织能够创建可更好地表示其业务分类的术语分类。

术语的名称在给定的层次结构级别中必须唯一 - 不允许重复的名称。 层次结构中的级别数没有限制，但是三个或更少级别的层次结构通常更易于理解。

可以选择在业务术语表中使用层次结构。 将术语表术语的父术语字段留空，这会在术语表中创建一个平面（而非分层）术语列表。  

## <a name="tagging-assets-with-glossary-terms"></a>使用术语表术语标记资产
在目录中定义了术语表术语后，优化了标记资产的体验，以在用户键入其标记时搜索术语表。 数据目录门户会显示一个匹配术语表术语列表供用户进行选择。 如果用户从列表中选择一个术语表术语，则会将其作为标记（也称为 术语表标记）添加到资产。 用户还可以选择通过键入不在术语表中的术语来创建新标记 （也称为用户标记）。

![标记有一个用户标记和两个术语表标记的数据资产](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> 用户标记是数据目录免费版中唯一支持的标记类型。
>
>

### <a name="hover-behavior-on-tags"></a>标记上的悬停行为
在数据目录门户中，这两种类型的标记看起来有所不同，并具有不同的悬停行为。 用户将鼠标悬停在用户标记上时，可以看到标记文本和添加了该标记的用户。 用户将鼠标悬停在术语表标记上时，还可以看到术语表术语的定义，以及能打开业务术语表以查看该术语的完整定义的链接。

### <a name="search-filters-for-tags"></a>标记的搜索筛选器
术语表标记和用户标记均可进行搜索，并且可以在搜索中应用为筛选器。

## <a name="summary"></a>摘要
Azure 数据目录中的业务术语表及其启用的管控标记允许以一致的方式识别、管理和发现数据资产。 业务术语表可以促进组织中用户对业务词汇的了解，并且支持捕获有意义的元数据，使资产发现和理解变得轻而易举。

## <a name="see-also"></a>另请参阅
* [用于业务术语表操作的 REST API 文档](https://msdn.microsoft.com/library/mt708855.aspx)

