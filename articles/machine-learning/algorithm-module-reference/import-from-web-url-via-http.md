---
title: 通过 HTTP： Module Reference 从 Web URL 导入
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "从 Web URL 导入" 从公共网页读取数据以在机器学习管道中使用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e2521dabdab8e9365019f35514f2d8d235c9c014
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693118"
---
# <a name="import-from-web-url-via-http-module"></a>通过 HTTP 模块从 Web URL 导入

本文介绍了 Azure 机器学习服务的可视界面（预览）的模块。

使用此模块读取公共网页中的数据，以便在机器学习管道中使用。

以下限制适用于网页上发布的数据：

- 数据必须采用支持的格式之一： CSV、TSV、ARFF 或 SvmLight。 其他数据将导致错误。
- 不需要或不支持身份验证。 数据必须公开提供。 

可以通过两种方法获取数据：使用向导设置数据源，或手动配置数据源。

## <a name="use-the-data-import-wizard"></a>使用数据导入向导

1. 将**导入数据**模块添加到管道。 可以在接口的 "**数据输入和输出**" 类别中找到该模块。

2. 单击 "**启动导入数据向导**"，并选择 "通过 HTTP Web URL"。

3. 粘贴到 URL 中，并选择一种数据格式。

4. 配置完成时。

若要编辑现有的数据连接，请重新启动该向导。 向导会加载所有以前的配置详细信息，以便你不必从头开始重新开始

## <a name="manually-set-properties-in-the-import-data-module"></a>手动设置 "导入数据" 模块中的属性

以下步骤说明如何手动配置导入源。

1. 将[导入数据](import-data.md)模块添加到管道。 可以在接口的 "**数据输入和输出**" 类别中找到该模块。

2. 对于 "**数据源**"，请选择 "**通过 HTTP 的 Web URL**"。

3. 对于 " **URL**"，请键入或粘贴包含要加载的数据的页面的完整 URL。

    URL 应包含网站 URL，以及包含文件名称和扩展名的完整路径到包含要加载的数据的页面。

    例如，以下页面包含加利福尼亚大学的机器学习存储库中的 Iris 数据集，Irvine：

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. 对于 "**数据格式**"，请从列表中选择一个支持的数据格式。

    建议你始终事先检查数据以确定格式。 UC Irvine 页面使用 CSV 格式。 其他支持的数据格式为 TSV、ARFF 和 SvmLight。

5. 如果数据采用 CSV 或 TSV 格式，则使用**File 带有标题行**选项来指示源数据是否包含标题行。 标题行用于分配列名称。

6. 如果你不希望数据更改过多，或者想要避免每次运行管道时重新加载数据，请选择 "**使用缓存的结果**" 选项。

    选中此选项后，管道将在第一次运行模块时加载数据，并使用数据集的缓存版本。

    如果要在管道数据集的每次迭代时重新加载数据集，请取消选择 "**使用缓存的结果**" 选项。 如果对[导入数据](import-data.md)的参数进行了任何更改，也会重新加载结果。

7. 运行管道。

## <a name="results"></a>结果

完成后，单击输出数据集，并选择 "**可视化**" 以查看是否已成功导入数据。


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 