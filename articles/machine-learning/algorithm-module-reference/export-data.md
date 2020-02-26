---
title: 导出数据：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "导出数据" 模块，将管道中的结果、中间数据和工作数据保存到 Azure 机器学习之外的云存储目标。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 40d8e298237b6110fee04aefbb7b79c5f3bac6f0
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598606"
---
# <a name="export-data-module"></a>导出数据模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可将管道中的结果、中间数据和工作数据保存到云存储目标。 

此模块支持将数据导出到以下 cloud data services：

- Azure Blob 容器
- Azure 文件共享
- Azure Data Lake
- Azure Data Lake Gen2

导出数据之前，需要先在 Azure 机器学习工作区中注册数据存储。 有关详细信息，请参阅[访问 Azure 存储服务中的数据](../how-to-access-data.md)。

## <a name="how-to-configure-export-data"></a>如何配置导出数据

1. 在设计器中将**导出数据**模块添加到管道。 您可以在 "**输入和输出**" 类别中找到此模块。

1. 将**导出数据**连接到包含要导出的数据的模块。

1. 选择 "**导出数据**" 以打开 "**属性**" 窗格。

1. 对于**数据存储**，请从下拉列表中选择现有的数据存储。 你还可以创建新的数据存储。 查看[访问 Azure 存储服务中的数据](../how-to-access-data.md)的方式。

1. 复选框 "**重新生成输出**" 决定是否执行模块以在运行时重新生成输出。 

    默认情况下，它处于未选中状态，这意味着，如果已使用相同的参数执行了模块，则系统将重用上次运行的输出以缩短运行时间。 

    如果选择此选项，系统将再次执行模块以重新生成输出。

1. 定义数据存储中数据的路径。 路径是相对路径。 不允许使用空路径或 URL 路径。


1. 对于 "**文件格式**"，请选择数据的存储格式。
 
1. 运行管道。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
