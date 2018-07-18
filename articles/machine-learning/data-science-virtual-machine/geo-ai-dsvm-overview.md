---
title: 地理人工智能数据科学虚拟机简介 - Azure | Microsoft Docs
description: 介绍地理 AI 数据科学 VM 和组件。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 2fb49212b8480fa550558e14e890cbf7395406da
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407446"
---
# <a name="introduction-to-the-geo-artificial-intelligence-data-science-virtual-machine"></a>地理人工智能数据科学虚拟机简介

Azure **地理 AI 数据科学 VM** (Geo-DSVM) 通过 Microsoft 的数据科学 VM 提供地理空间分析功能。 具体而言，此 VM 通过在[数据科学 VM](overview.md) 中添加 ESRI 的市场领先 [ArcGIS Pro](https://www.esri.com/arcgis/products/arcgis-pro/overview) 地理信息系统来扩展 AI 和数据科学工具包。

地理 AI 数据科学 VM 是 ESRI 和 Microsoft 之间的协作产生的产品服务。 其结果是一个功能强大的环境，可提供以下功能：

- 使用最常用 AI 和数据科学工具研究数据
- 对地理或空间方面的数据应用统计分析
- 使用机器学习和深度学习算法为预测分析训练模型
- 创建丰富的可视化效果

Windows 2016 数据科学虚拟机支持这种虚拟机。 另外，还在 Geo-DSVM 上预配置了 ArcGIS 的 Python 和 R 接口，以允许以编程方式访问 AI 应用程序中的地理空间分析。 还提供了 Jupyter Notebook 示例以帮助开始构建满载着地理空间智能的 AI 应用程序。


## <a name="why-geo-ai-data-science-vm"></a>为什么使用地理 AI 数据科学 VM？ 

将地理和位置信息与数据集成为了解世界越来越多地使用 IOT 设备带来了强大的新维度。 各种应用程序几乎存在于公民社会的每一个部分中，而无论是商业活动、政府活动、学术活动还是非营利性活动。 地理空间分析提供了可靠的工具来收集、管理和分析地理数据和基于位置的数据，并提供了功能强大的可视化效果，让用户以独特的视角了解此类数据的重要性。 

Microsoft 提供的[数据科学虚拟机 (DSVM)](overview.md) 是 Azure 上常用的试验和建模环境，并提供许多 AI、机器学习和数据科学工具。 这些平台和工具均已方便地进行预配置，以帮助你立即提高工作效率。 

通过将数据科学 VM 的预测功能与 Azure 云和 [ESRI](http://www.esri.com) 的地理空间分析软件的可伸缩性相结合，数据科学家和地理空间分析师现在有了一个高效平台，通过该平台可以从其数据中获取新智能和确定新机会。 


## <a name="next-steps"></a>后续步骤

按照以下主题中的指南预配地理 AI 数据科学 VM 和开始使用该 VM：

* [预配地理 AI 数据科学 VM](provision-geo-ai-dsvm.md)
* [使用地理 AI 数据科学 VM](use-geo-ai-dsvm.md)
