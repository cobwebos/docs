---
title: "连接到 Power BI 工作区集合中的数据源 | Microsoft Docs"
description: "了解如何连接到 Power BI 工作区集合内的数据源。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 24600c4343e3bfebe14f25020c5a7ba02d15af64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-data-source"></a>连接到数据源

使用 Power BI 工作区集合，可以在自己的应用中嵌入报表。 在应用中嵌入 Power BI 报表时，可以通过**导入**数据的副本或通过使用 **DirectQuery****直接连接**到数据源将该报表连接到基础数据。

> [!IMPORTANT]
> Power BI 工作区集合已遭弃用，在 2018 年 6 月或合同上指明的时间前可用。 建议计划迁移到 Power BI Embedded，以免应用中断。 若要了解如何将数据迁移到 Power BI Embedded，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

以下是使用**导入**和 **DirectQuery** 之间的区别。

| 导入 | DirectQuery |
| --- | --- |
| 将表、列和数据导入或复制到报表的数据集。 若要查看对基础数据所做的更改，必须刷新，或重新导入完整的当前数据集。 |仅会将表和列导入或复制到报表的数据集中。 始终可以查看最新的数据。 |

通过 Power BI 工作区集合，可以将 DirectQuery 与云数据源结合使用，但暂不能与本地数据源结合使用。

> [!NOTE]
> Power BI 工作区集合暂不支持本地数据网关。 这意味着无法将 DirectQuery 与本地数据源结合使用。

## <a name="supported-data-sources"></a>支持的数据源

**DirectQuery**
* Azure SQL 数据库
* Azure SQL 数据仓库

**导入**

可以在 Power BI Desktop 中使用所有可用数据源进行导入。 不能在 Power BI 工作区集合中刷新此类数据。 必须将 PBIX 文件更改上传到 Power BI 工作区集合。 这是因为没有可用网关。 

## <a name="benefits-of-using-directquery"></a>使用 DirectQuery 的优点

使用 **DirectQuery** 具有两个主要优点：

* 使用 DirectQuery，可以根据大型数据集生成可视化效果，这就导致以其他方式先导入所有数据变得不可行。
* 基础数据更改可能需要刷新数据，对于一些报表，可能需要执行大型数据传输，才能显示当前数据，这就导致重新导入数据变得不可行。 与此相反，**DirectQuery** 报表始终使用当前数据。

## <a name="limitations-of-directquery"></a>DirectQuery 的限制

使用 **DirectQuery** 存在一些限制：

* 所有表必须都来自单一数据库。
* 如果查询过于复杂，则会出错。 若要修复此错误，则必须重构查询，降低其复杂程度。 如果查询必须很复杂，需要导入数据，而不使用 DirectQuery。
* 关系筛选仅限于单向，而不是双向。
* 不能更改某列的数据类型。
* 默认情况下，将对度量值中允许使用的 DAX 表达式加以限制。 请参阅 [DirectQuery 和度量值](#measures)。

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery 和度量值
为了确保发送到基础数据源的查询具有可接受的性能，针对度量值施加了一些限制。 使用 **Power BI Desktop** 时，高级用户可以通过选择“文件”>“选项和设置”>“选项”来选择绕过此限制。 在“选项”对话框中，选择“DirectQuery”，并选择“允许 DirectQuery 模式下的度量值不受限制”选项。 选中该选项后，可以使用对度量值有效的任何 DAX 表达式。 不过，用户必须知道，虽然一些表达式在导入数据时性能很好，但在 DirectQuery 模式下可能会导致对后端源的查询非常缓慢。 

## <a name="see-also"></a>另请参阅

* [Microsoft Power BI 工作区集合入门](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

有更多问题？ [尝试 Power BI 社区](http://community.powerbi.com/)

