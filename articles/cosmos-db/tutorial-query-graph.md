---
title: 如何查询 Azure Cosmos DB 中的图数据？ | Microsoft Docs
description: 学习如何查询 Azure Cosmos DB 中的图数据
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: jhubbard
editor: ''
tags: ''
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: eb1da11c8b27a429ffcf9ea8fb50b6c7cee26ec0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>教程：使用 Gremlin 查询 Azure Cosmos DB 图形 API

Azure Cosmos DB [图形 API](graph-introduction.md) 支持 [Gremlin](https://github.com/tinkerpop/gremlin/wiki) 查询。 本文包含示例文档和查询，提供入门指导。 [Gremlin 支持](gremlin-support.md)一文提供了详细的 Gremlin 引用。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Gremlin 查询数据

## <a name="prerequisites"></a>先决条件

若要使这些查询生效，必须拥有 Azure Cosmos DB 帐户，且容器中必须包含图数据。 没有这些内容？ 请学习 [5 分钟快速入门](create-graph-dotnet.md)或[开发人员教程](tutorial-query-graph.md)，创建 帐户并填充数据库。 可使用 [Azure Cosmos DB .NET 图库](graph-sdk-dotnet.md)、[Gremlin 控制台](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console)或喜爱的 Gremlin 驱动程序运行以下查询。

## <a name="count-vertices-in-the-graph"></a>计算图中的顶点数量

以下代码段演示如何对图中的顶点进行计数：

```
g.V().count()
```

## <a name="filters"></a>筛选器

可通过 Gremlin 的 `has` 和 `hasLabel` 步骤执行筛选，并使用 `and``or` 和 `not` 将其组合，创建更复杂的筛选器。 Azure Cosmos DB 提供顶点和度数内所有属性的架构无关索引，以进行快速查询：

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>投影

可使用 `values` 步骤对查询结果中的某些属性进行投影：

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>查找相关边缘和顶点

目前为止，我们仅介绍了适用于任何数据库的查询运算符。 如需导航到相关边缘和顶点，可以使用图快速高效地进行遍历操作。 查找 Thomas 的所有朋友。 为此，可使用 Gremlin 的 `outE` 步骤从 Thomas 中查找所有外缘，并使用 Gremlin 的 `inV` 步骤从这些边缘遍历至内顶点：

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

下一查询通过调用 `outE` 和 `inV` 两次，执行两个跃点查找 Thomas 所有的“朋友的朋友”。 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

可使用 Gremlin 构建更加复杂的查询和实现功能强大图遍历逻辑，包括混合筛选表达式、使用 `loop` 步骤执行循环以及使用 `choose` 步骤实现条件导航。 深入了解通过 [Gremlin 支持](gremlin-support.md)可实现的操作！

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 已了解如何使用 Graph 进行查询 

现在可继续学习下一教程，了解如何全局发布数据。

> [!div class="nextstepaction"]
> [全局分发数据](tutorial-global-distribution-sql-api.md)
