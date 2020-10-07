---
title: Azure Cosmos DB（预览版）中的内置 Jupyter Notebook 支持简介
description: 了解如何使用 Azure Cosmos DB 中的内置 Jupyter Notebook 支持以交互方式运行查询。
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 0299ab1e76e742adb8d2653b8b5d6923aff2f11f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88588149"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Azure Cosmos DB（预览版）中的内置 Jupyter Notebook 支持

Jupyter Notebook 是一个开源 Web 应用程序，可用于创建和共享包含实时代码、公式、可视化效果与叙述文本的文档。 

Azure Cosmos DB 内置 Jupyter Notebook 直接集成到 Azure 门户和 Azure Cosmos DB 帐户，所以使用起来非常便利。 开发人员、数据科学家、工程师和分析师可以使用熟悉的 Jupyter Notebook 体验进行数据浏览、数据清理、数据转换、数值模拟、统计建模、数据可视化和机器学习。

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Azure Cosmos DB 中的 Jupyter Notebook 可视化效果":::

Azure Cosmos DB 支持适用于所有 API 的 C# 和 Python 笔记本，包括 Core (SQL)、Cassandra、Gremlin、表和适用于 MongoDB 的 API。 通过在笔记本里利用内置命令和功能，可以轻松地创建 Azure Cosmos DB 资源、上传数据以及查询和可视化 Azure Cosmos DB 中的数据。 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Azure Cosmos DB 中的 Jupyter Notebook 可视化效果":::

## <a name="benefits-of-jupyter-notebooks"></a>Jupyter Notebook 的优势

Jupyter Notebook 一开始是为以 Python 和 R 编写的数据科学应用程序开发的。但是，可以通过各种方式将其用于不同类型的项目，包括：

**数据可视化：** 可以通过 Jupyter Notebook 将数据可视化为共享笔记本，该笔记本将数据集呈现为图形。 可以创建可视化效果，也允许对共享的代码和数据集进行交互式更改，并共享结果。

**代码共享：** 可以通过 GitHub 之类的服务来共享代码，但这些服务大部分是非交互式的。 使用 Jupyter Notebook，可以查看、执行代码，并直接在 Azure 门户中显示结果。

**与代码实时交互：** Jupyter Notebook 中的代码是动态的，可以对其进行编辑，并实时增量运行更新。 还可以嵌入用户控件（例如滑块或文本输入字段），用作代码、演示或概念证明 (POC) 的输入源。

**记录代码示例和数据浏览的结果：** 如果你有一个代码片段，需要逐行说明其工作原理，则可将其嵌入 Jupyter 笔记本中。 你可以同时添加交互性和文档内容。

**Azure Cosmos DB 的内置命令：** 通过使用 Azure Cosmos DB 的内置 magic 命令，可以轻松地与帐户进行交互。 你可以使用 %%upload 和 %%sql 等命令将数据上传到容器，并使用 [SQL API 语法](sql-query-getting-started.md)对其进行查询。 无需编写其他自定义代码。

**集中处理环境：** Jupyter Notebook 将代码、富文本、图像、视频、动画、数学公式、绘图、地图、交互式图形、小组件和图形用户界面组合到单个文档中。

## <a name="components-of-a-jupyter-notebook"></a>Jupyter Notebook 的组件

Jupyter Notebook 可能包括多种类型的组件，每一种都组织成不同的块或单元：

**文本和 HTML：** 纯文本或者以 Markdown 语法标注的用于生成 HTML 的文本可以插入到文档中的任何位置。 也可以内联方式包括 CSS 样式，或者将其添加到用于生成笔记本的模板。

**代码和输出：** Jupyter Notebook 支持 Python 和 C# 代码。 已执行代码的结果立即显示在代码块后面，代码块可以按你喜欢的任意顺序执行多次。

**可视化效果：** 可以使用 Matplotlib、Plotly 或 Bokeh 之类的模块通过代码生成图形和图表。 与输出类似，这些可视化效果以内联方式显示在生成它们的代码旁边。 与输出类似，这些可视化效果以内联方式显示在生成它们的代码旁边。

**多媒体：** Jupyter Notebook 基于 Web 技术，因此可以显示网页支持的所有类型的多媒体。 可以将它们作为 HTML 元素包括在笔记本中，也可以使用 `IPython.display` 模块以编程方式生成它们。

**数据：** 可以通过编程方式将 Azure Cosmos 容器中的数据和查询结果导入 Jupyter Notebook 中。 使用内置的 magic 命令上传或查询 Azure Cosmos DB 中的数据。 

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Cosmos DB 中的内置 Jupyter Notebook，请参阅以下文章：

* [在 Azure Cosmos 帐户中启用笔记本](enable-notebooks.md)
* [使用 Python 笔记本功能和命令](use-python-notebook-features-and-commands.md)
* [使用 C# 笔记本功能和命令](use-csharp-notebook-features-and-commands.md)
