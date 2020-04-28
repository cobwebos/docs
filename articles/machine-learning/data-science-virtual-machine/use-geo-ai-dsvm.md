---
title: 使用地理人工智能
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
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "70278421"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>使用地理人工智能数据科学虚拟机

使用地理 AI 数据科学 VM 提取数据进行分析、执行数据争论，以及为使用地理空间信息的 AI 应用程序构建模型。 在预配地理人工智能 Data Science VM 并通过 ArcGIS 帐户登录到 ArcGIS Pro 后，可以开始在线与 ArcGIS 桌面和 ArcGiS 进行交互。 还可以通过地理 Data Science VM 上预配置的 Python 接口和 R 语言桥来访问 ArcGIS。 若要构建丰富的 AI 应用程序，请将地理 Data Science VM 与在其上可用的机器学习和深度学习框架以及其他数据科学软件组合使用。  


## <a name="configuration-details"></a>配置详细信息

Python 库 [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)，用于与在数据科学 VM 的全局根 conda 环境（位于 ```c:\anaconda```）中安装的 ArcGIS 进行交互。

- 如果在命令提示符运行 Python，请运行 ```activate``` 来激活到 conda 根 Python 环境。
- 如果使用 IDE 或 Jupyter Notebook，可以选择环境或内核来确保处于正确的 conda 环境中。

ArcGIS 的 R 桥作为名为 [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 的 R 库安装在位于 ```C:\Program Files\Microsoft\ML Server\R_SERVER``` 的主 Microsoft Machine Learning Server 独立实例中。 Visual Studio、RStudio 和 Jupyter 已预配置为使用此 R 环境并将具有对 ```arcgisbinding``` R 库的访问权限。 


## <a name="geo-ai-data-science-vm-samples"></a>地理 AI 数据科学 VM 示例

除了基础 Data Science VM 提供的基于机器学习和深度学习框架的示例，地理人工智能 Data Science VM 还提供了一组地理空间示例。 这些示例可以帮助你使用地理空间数据和 ArcGIS 软件快速开始 AI 应用程序开发：


1. [开始使用 Python 地理空间分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)：一个介绍性示例，介绍了如何使用 [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) 库提供的到 ArcGIS 的 Python 接口来处理地理空间数据。 它还介绍了如何将传统的机器学习与地理空间数据组合使用，并在 ArcGIS 中的地图上直观呈现结果。

2. [使用 R 进行地理空间分析入门](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)：一个介绍性示例，介绍了如何使用 [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 库提供的到 ArcGIS 的 R 接口来处理地理空间数据。 

3. [像素级土地利用分类](https://github.com/Azure/pixel_level_land_classification)：该教程展示了如何创建深度神经网络模型，使该模型接受航拍图像作为输入并返回土地覆盖标签。 土地覆盖标签的示例包括“森林”和“水”   。 模型会为图像中的每个像素返回这样的标签。 


## <a name="next-steps"></a>后续步骤

使用数据科学 VM 的其他示例位于以下位置：

* [示例](dsvm-samples-and-walkthroughs.md)