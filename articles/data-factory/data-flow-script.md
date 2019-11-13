---
title: Azure 数据工厂映射数据流脚本
description: 数据工厂数据流脚本代码隐藏语言概述
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010158"
---
# <a name="data-flow-script-dfs"></a>数据流脚本（DFS）

数据流脚本（DFS）是基本元数据，类似于编码语言，用于执行映射数据流中包含的转换。 每个转换均由一系列属性表示，这些属性提供正确运行作业所需的信息。 通过单击浏览器 UI 顶部功能区上的 "脚本" 按钮，可从 ADF 查看并编辑该脚本。

![脚本按钮](media/data-flow/scriptbutton.png "脚本按钮")

例如，在源转换中 `allowSchemaDrift: true,` 时，会告知服务将源数据集中的所有列包含在数据流中，即使它们未包含在架构投影中也是如此。

## <a name="use-cases"></a>用例
DFS 由用户界面自动生成。 您可以单击 "脚本" 按钮，查看并自定义该脚本。 你还可以在 ADF UI 外生成脚本，然后将其传递到 PowerShell cmdlet。 调试复杂数据流时，您可能会发现扫描脚本代码隐藏而不是扫描流的 UI 图形表示形式会更容易。

下面是几个示例用例：
- 以编程方式生成非常相似的多个数据流，即 "标记出" 数据流。
- 难以在 UI 中管理或导致验证问题的复杂表达式。
- 调试和更好地了解在执行过程中返回的各种错误。

在生成要用于 PowerShell 或 API 的数据流脚本时，必须将格式化文本折叠为一行。 您可以保留制表符和换行符作为转义符。 但必须对文本进行格式化，才能容纳到 JSON 属性中。 下面的脚本编辑器 UI 上有一个按钮，该按钮将脚本的格式设置为一行。

![复制按钮](media/data-flow/copybutton.png "“复制”按钮")

## <a name="how-to-add-transforms"></a>如何添加转换
添加转换需要三个基本步骤：添加核心转换数据、重新路由输入流，然后重新路由输出流。 在示例中，这种方法很容易看出。
假设我们从一个简单的源来接收数据流，如下所示：

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

如果我们决定添加派生转换，首先需要创建核心转换文本，其中包含一个简单的表达式用于添加名为 `upperCaseTitle`的新大写列：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

接下来，我们获取现有 DFS 并添加转换：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

现在，我们通过标识要新转换之后的转换（在本例中为 `source1`）来重新路由传入的流，并将流的名称复制到新的转换：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

最后，我们确定要在此新转换后出现的转换，并将其输入流（在本例中为 `sink1`）替换为新转换的输出流名称：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS 基础知识
DFS 由一系列相互连接的转换组成，其中包括源、接收器，以及可添加新列、筛选数据和联接数据等的其他各种转换。 通常情况下，使用一个或多个源开始的脚本会执行许多转换，并以一个或多个接收器结束。

所有源都具有相同的基本结构：
```
source(
  source properties
) ~> source_name
```

例如，具有三个列（movieId、title、流派）的简单源将为：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

源以外的所有转换都具有相同的基本结构：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，一个简单的派生转换，它采用列（title）并使用大写的版本覆盖它，如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

无架构的接收器只是：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>后续步骤

从数据流[概述一文](concepts-data-flow-overview.md)开始探索数据流
