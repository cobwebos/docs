---
title: 导出数据：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“导出数据”模块，将管道中的结果、中间数据和工作数据保存到 Azure 机器学习之外的云存储目标中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456295"
---
# <a name="export-data-module"></a>导出数据模块

本文介绍 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块将管道的结果、中间数据和工作数据保存到云存储目标。 

此模块支持将数据导出到以下云数据服务中：

- Azure Blob 容器
- Azure 文件共享
- Azure Data Lake
- Azure Data Lake Gen2

在导出数据之前，需要首先在 Azure 机器学习工作区中注册数据存储。 有关详细信息，请参阅访问[Azure 存储服务中的数据](../how-to-access-data.md)。

## <a name="how-to-configure-export-data"></a>如何配置“导出数据”

1. 在设计器中将“导出数据”模块添加到管道****。 可以在“输入和输出”类别中找到该模块****。

1. 将“导出数据”连接到包含要导出的数据的模块****。

1. 选择“导出数据”，打开“属性”窗格********。

1. 对于“数据存储”，请从下拉列表中选择现有数据存储****。 还可以创建新的数据存储。 通过访问[Azure 存储服务中的访问数据](../how-to-access-data.md)来检查如何。

1. 复选框"**重新生成输出**"将决定是否执行模块以在运行时重新生成输出。 

    默认情况下未选中，这意味着如果模块以前使用相同的参数执行，系统将重用上次运行的输出以缩短运行时间。 

    如果选中，系统将再次执行模块以重新生成输出。

1. 定义数据存储中数据的路径。 路径是相对路径。 不允许使用空路径或 URL 路径。


1. 对于“文件格式”，请选择数据的存储格式****。
 
1. 提交管道。

## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 
