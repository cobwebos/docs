---
title: Graph 搜索查询语法
titleSuffix: Azure Machine Learning
description: 了解如何使用搜索查询语法在管道图形中搜索节点。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 122da78206ef2055b4867727b174293e74133c05
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817324"
---
# <a name="graph-search-query-syntax"></a>Graph 搜索查询语法

在本文中，你将了解 Azure 机器学习中的图形搜索查询语法。 利用图形搜索功能，您可以按节点的名称和属性来搜索节点。 

 ![显示示例图形搜索体验的动画屏幕截图](media/search/graph-search.gif)

Graph 搜索支持在节点名称和注释上进行全文关键字搜索。 你还可以按节点属性（例如 runStatus、duration、computeTarget）进行筛选。 关键字搜索基于 Lucene 查询。 完整的搜索查询如下所示：  

**[lucene 查询 |[筛选查询]** 

您可以使用 Lucene 查询或筛选查询。 若要同时使用这两种方法，请使用 **|** 分隔符。 筛选器查询的语法比 Lucene 查询更严格。 因此，如果可以将客户输入分析为两者，将应用筛选器查询。

 

## <a name="lucene-query"></a>Lucene 查询

Graph 搜索使用 Lucene 简单查询作为节点 "名称" 和 "注释" 的全文搜索语法。 支持以下 Lucene 运算符：

 
- 和/或
- 通配符是否匹配 **？** 和 **\*** 运算符。

### <a name="examples"></a>示例

- 简单搜索： `JSON Data`

- AND/OR： `JSON AND Validation`

- 多个和/或： `(JSON AND Validation) OR (TSV AND Training)`

 
- 通配符匹配： 
    - `machi?e learning`
    -   `mach*ing`
 
>[!NOTE]
> 不能使用 "*" 字符启动 Lucene 查询。

##  <a name="filter-query"></a>筛选查询

 
筛选查询使用以下模式：
 
    **[key1] [operator1] [value1]; [key2] [operator1] [value2];**

 
你可以使用以下节点属性作为键：

- runStatus
- 计算
- duration
- 直接

并使用以下运算符：

- 大于或等于： `>=`
- 小于或等于： `<=`
- 强大 `>`
- 量 `<`
- = `==`
- 能 `=`
- NotEqual `!=`
- 中 `in`

 
 

### <a name="example"></a>示例

- 持续时间 > 100;
- 状态在 {Failed，NotStarted} 中
- {0} 中的计算{0} 中的 runStatus

## <a name="technical-notes"></a>技术说明

- 多个筛选器之间的关系是 "AND"
- 如果 `>=,  >,  <, or  <=` 选择了，则值将自动转换为数字类型。 否则，将使用字符串类型进行比较。
- 对于所有字符串类型值，不区分大小写。
- 运算符 "In" 需要集合作为值，集合语法为 `{name1, name2, name3}`
- 关键字之间将忽略空格