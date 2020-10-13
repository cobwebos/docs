---
title: 教程：开始探索 Synapse 知识中心
description: 在本教程中，你将了解如何使用 Synapse 知识中心。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 47b8c45e22569cc758d00fb8534f409ecebf58ca
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299925"
---
# <a name="explore-the-synapse-knowledge-center"></a>探索 Synapse 知识中心

在本教程中，你将了解如何使用 Synapse Studio 知识中心。

## <a name="getting-to-the-knowledge-center"></a>访问知识中心

在 Synapse Studio 中，有两种方法可以找到知识中心：

  1. 在主页中心的“有用的链接”下，单击名为“知识中心”的第一个链接。
  2. 在顶部的菜单栏中，单击 **?** 然后单击“知识中心”。

选择任一方法并打开“知识中心”。

## <a name="overview"></a>概述

使用“知识中心”可以执行三项操作：
* **立即使用示例**。 此选项经过优化，你可以尽快看到正在进行的分析。 如果你需要一个快速示例来了解 Synapse 如何工作，请选择此选项。
* **浏览可用示例**。 此选项允许你链接示例数据集，并以 SQL 脚本、笔记本和管道的形式添加示例代码。
* **浏览 Synapse Studio**。 此选项将带你简单了解 Synapse Studio 的基本部分。 如果你以前从未使用过 Synapse Studio，则这很有用。

## <a name="exploring-blob-storage-with-sql-on-demand"></a>使用按需 SQL 探索 blob 存储

1. 在“知识中心”中，单击“立即使用示例”
1. 选择“使用 SQL 来查询数据” 
1. 单击“立即使用示例”
1. 它将创建一个新的 SQL 脚本。
1. 滚动到第一个查询（第 28 行到第 32 行），然后选择查询文本
1. 单击“运行”。 它将运行你选择的文本。

## <a name="loading-more-nyc-taxi-data"></a>加载更多 NYC 出租车数据
1. 在“知识中心”中，单击“浏览可用示例” 
1. 选择顶部的“SQL 脚本”选项卡
1. 选择“加载纽约出租车数据集”
1. 在“输入”下选择“选择现有池”，接着选择“SQLDB1”
1. 单击“打开脚本”
1. 此时会显示一个新的 SQL 脚本。
1. 单击“**运行**”
1. 这将为所有 NYC 出租车数据创建多个表，并使用 T-SQL COPY 命令加载它们。

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics 入门](get-started.md)
* [创建工作区](quickstart-create-workspace.md)
* [使用 SQL 按需版本](quickstart-sql-on-demand.md)
