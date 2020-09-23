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
ms.date: 07/28/2020
ms.openlocfilehash: 5964ed9773d45b809fc79252eabc754b1b04af84
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907989"
---
# <a name="export-data-module"></a>导出数据模块

本文介绍 Azure 机器学习设计器中的模块。

使用此模块将管道中的结果、中间数据和工作数据保存到云存储目标中。 

此模块支持将数据导出到以下云数据服务中：

- Azure Blob 容器
- Azure 文件共享
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL 数据库

导出数据之前，首先需要在 Azure 机器学习工作区中注册数据存储。 有关详细信息，请参阅[访问 Azure 存储服务中的数据](../how-to-access-data.md)。

## <a name="how-to-configure-export-data"></a>如何配置“导出数据”

1. 在设计器中将“导出数据”模块添加到管道。 可以在“输入和输出”类别中找到该模块。

1. 将“导出数据”连接到包含要导出的数据的模块。

1. 选择“导出数据”，打开“属性”窗格 。

1. 对于“数据存储”，请从下拉列表中选择现有数据存储。 还可以创建新的数据存储。 通过访问[访问 Azure 存储服务中的数据](../how-to-access-data.md)来查看方法。

    > [!NOTE]
    > 不支持将某种数据类型的数据导出到指定为另一种数据类型的 SQL 数据库列。 目标表不需要先存在。

1. 复选框“重新生成输出”决定是否在运行时执行模块以重新生成输出。 

    默认情况下，它处于未选中状态，这意味着如果以前已经以相同的参数执行了模块，系统将重用上次运行的输出以减少运行时间。 

    如果选中了此复选框，系统将再次执行模块以再生成输出。

1. 在数据存储中定义数据所在的路径。 此路径是相对路径。 不允许使用空路径或 URL 路径。


1. 对于“文件格式”，请选择数据的存储格式。
 
1. 提交管道。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
