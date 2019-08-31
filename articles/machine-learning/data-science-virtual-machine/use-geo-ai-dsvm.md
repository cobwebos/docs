---
title: 使用地区 AI
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何使用地理 AI Data Science Virtual Machine 分析数据并根据地理空间数据构建模型。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 01238885e68a39ff713bd346a9206668e16e7ee4
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195285"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>使用地理人工智能数据科学虚拟机

使用地理 AI 数据科学 VM 提取数据进行分析、执行数据争论，以及为使用地理空间信息的 AI 应用程序构建模型。 预配异地 AI Data Science VM 并通过 ArcGIS 帐户登录到 ArcGIS Pro 后, 便可以开始与 ArcGIS desktop 和 ArcGIs online 交互。 你还可以从 Python 接口和在地理 Data Science VM 上预先配置的 R 语言桥访问 ArcGIS。 若要构建丰富的 AI 应用程序, 请将地理 Data Science VM 与计算机学习和深度学习框架以及它提供的其他数据科学软件组合在一起。  


## <a name="configuration-details"></a>配置详细信息

用于与 ArcGIS 进行交互的 Python 库 ( [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)) 安装在中找到```c:\anaconda```的 Data Science VM 的全局根 conda 环境中。

- 如果要在命令提示符处运行 Python, 请运行```activate```以激活到 conda 根 Python 环境。
- 如果使用的是 IDE 或 Jupyter 笔记本, 可以选择环境或内核, 以确保处于正确的 conda 环境中。

ArcGIS 的 R 桥将作为名为[arcgisbinding](https://github.com/R-ArcGIS/r-bridge)的 r 库安装在位于```C:\Program Files\Microsoft\ML Server\R_SERVER```的主 Microsoft Machine Learning Server 独立实例中。 Visual Studio、RStudio 和 Jupyter 已预先配置为使用此 r 环境, 并将具有对 r 库```arcgisbinding```的访问权限。 


## <a name="geo-ai-data-science-vm-samples"></a>地理 AI 数据科学 VM 示例

除了基本 Data Science VM 中的基于机器学习和深度学习框架的示例外, 还提供了一套地理 AI Data Science VM 的地理空间示例。 这些示例可帮助你快速开始使用地理空间数据和 ArcGIS 软件开发 AI 应用程序:


1. [用于 Python 的地理空间分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)入门:说明如何通过 Python interface to ArcGIS 使用地理空间数据的介绍性示例由[arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)库提供。 它还演示了如何将传统机器学习与地理空间数据组合在一起, 并在 ArcGIS 中将结果可视化到地图中。

2. [通过 R 进行地理空间分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)入门:本指南演示如何使用 ArcGIS [arcgisbinding](https://github.com/R-ArcGIS/r-bridge)库提供的 R 接口处理地理空间数据。 

3. [像素级土地利用分类](https://github.com/Azure/pixel_level_land_classification)：该教程展示了如何创建深度神经网络模型，使该模型接受航拍图像作为输入并返回土地覆盖标签。 土地封面标签的示例包括*森林*和*水*。 模型会为图像中的每个像素返回这样的标签。 


## <a name="next-steps"></a>后续步骤

使用数据科学 VM 的其他示例位于以下位置：

* [示例](dsvm-samples-and-walkthroughs.md)

