---
title: 转换为 CSV：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "转换为 CSV" 模块将数据集转换为可下载、导出或与 R 或 Python 脚本模块共享的 CSV 格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 8206a88695c89d04eabe89e79a5aff8469cc6862
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152425"
---
# <a name="convert-to-csv-module"></a>转换为 CSV 模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块将数据集转换为 CSV 格式，以便可以下载、导出或与 R 或 Python 脚本模块共享。

### <a name="more-about-the-csv-format"></a>有关 CSV 格式的详细信息 

CSV 格式（代表 "逗号分隔值"）是许多外部机器学习工具使用的一种文件格式。 CSV 是使用开源语言（如 R 或 Python）时的常见交换格式。

即使您在 Azure 机器学习执行大部分工作，有时您可能会发现将数据集转换为 CSV 以便在外部工具中使用。 例如：

+ 下载 CSV 文件以使用 Excel 打开它，或将其导入到关系数据库中。  
+ 将 CSV 文件保存到云存储，并从 Power BI 连接到它以创建可视化效果。  
+ 使用 CSV 格式准备要在 R 和 Python 中使用的数据。 

将数据集转换为 CSV 时，csv 保存在 Azure ML 工作区中。 可以使用 Azure 存储实用工具直接打开并使用该文件。 还可以通过选择 "**转换为 CSV** " 模块来访问设计器中的 csv，然后在右侧面板中的 "**输出**" 选项卡下选择 "直方图" 图标以查看输出。 可以从 "结果" 文件夹将 CSV 下载到本地目录。  

## <a name="how-to-configure-convert-to-csv"></a>如何配置转换为 CSV


1.  将 "转换为 CSV" 模块添加到管道。 可以在设计器的 "**数据转换**" 组中找到此模块。 

2. 将其连接到输出数据集的任何模块。   
  
3.  运行管道。

### <a name="results"></a>结果
  

选择 "**转换为 CSV**" 的右面板中的 "**输出**" 选项卡，然后在**端口输出**下的其中一个图标上选择。  

+ **注册数据集**：选择图标，并将 CSV 文件作为单独的数据集保存回 Azure ML 工作区。 可以在 "**我的数据集**" 部分下的模块树中找到数据集作为模块。

 + **查看输出**：选择眼睛图标，然后按照说明浏览**Results_dataset**文件夹，然后下载数据 .csv 文件。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 