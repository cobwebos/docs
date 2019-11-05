---
title: 转换为 CSV：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "转换为 CSV" 模块将数据集转换为可下载、导出或与 R 或 Python 脚本模块共享的 CSV 格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 999f78ab08e1a2c9dd6b28d853e49fbb559fab83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493846"
---
# <a name="convert-to-csv-module"></a>转换为 CSV 模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块将数据集转换为 CSV 格式，以便可以下载、导出或与 R 或 Python 脚本模块共享。

### <a name="more-about-the-csv-format"></a>有关 CSV 格式的详细信息 

CSV 格式（代表 "逗号分隔值"）是许多外部机器学习工具使用的一种文件格式。 CSV 是使用开源语言（如 R 或 Python）时的常见交换格式。

即使您在 Azure 机器学习执行大部分工作，有时您可能会发现将数据集转换为 CSV 以便在外部工具中使用。 例如：

+ 下载 CSV 文件以使用 Excel 打开它，或将其导入到关系数据库中。  
+ 将 CSV 文件保存到云存储，并从 Power BI 连接到它以创建可视化效果。  
+ 使用 CSV 格式准备要在 R 和 Python 中使用的数据。 只需右键单击模块的输出即可生成直接从 Python 或 Jupyter 笔记本访问数据所需的代码。 

将数据集转换为 CSV 时，该文件将保存在 Azure ML 工作区中。 你可以使用 Azure 存储实用工具直接打开并使用该文件，也可以右键单击该模块输出并将其下载到你的计算机上，或在 R 或 Python 代码中使用它。  

## <a name="how-to-configure-convert-to-csv"></a>如何配置转换为 CSV


1.  将 "[转换为 CSV](./convert-to-csv.md) " 模块添加到管道。 可以在设计器中的 "**数据格式转换**" 组中找到此模块。 

2. 将其连接到输出数据集的任何模块。   
  
3.  运行管道。

### <a name="results"></a>结果
  

双击 "[转换为 CSV](./convert-to-csv.md)" 的输出，然后选择其中一个选项。  

 + **结果数据集-> 下载**：以 CSV 格式立即打开可保存到本地文件夹的数据的副本。 如果未指定文件夹，则将应用默认文件名，并将 CSV 文件保存在本地**下载**库中。


 + **结果数据集-> 另存为数据集**：将 CSV 文件作为单独的数据集保存回 Azure ML 工作区。

 + **生成数据访问代码**： Azure ML 会生成两组代码，以便你使用 Python 或使用 R 访问数据。若要访问数据，请将代码片段复制到应用程序中。 （*稍后将生成数据访问代码。* ）

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 