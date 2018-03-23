---
title: 深度学习虚拟机简介 -Azure |Microsoft Docs
description: 深度学习虚拟机的关键分析方案和组件。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: f74b5c68c2c04eebc1d6f1e59fac8cbb11fef46a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>深度学习虚拟机简介

## <a name="why-deep-learning-virtual-machine"></a>为什么选择深度学习虚拟机？ 

深度学习算法/深度神经网络在机器学习项目中的采用越来越普遍。 这些方法通过高级深层神经网络体系结构和访问大型数据集来训练模型，尤其善于处理图像、文本、音频/视频理解等机器认知任务，在某些领域常可接近人类认知水平。 深度学习需要强大的计算能力来使用大数据集训练模型。 由于可使用云和图形处理单元 (GPU)，现在逐渐可以构建复杂的深层神经体系结构，并基于云上强大计算基础结构中的大数据集训练它们。  [数据科学虚拟机](overview.md)可为数据准备、机器学习和深度学习提供各种工具和示例。 但用户所面临的挑战之一，是轻松找到适合深度学习等特定情景的工具和示例，以及更轻松地预配基于 GPU 的 VM 实例。 此深度学习虚拟机 (DLVM) 可以应对这些挑战。 

## <a name="what-is-deep-learning-virtual-machine"></a>什么是深度学习虚拟机？ 
深度学习虚拟机是[数据科学虚拟机](overview.md) (DSVM) 的特别配置变体，使其能够更直接地使用基于 GPU 的 VM 实例训练深度学习模型。 Windows 2016 和 Ubuntu 数据科学虚拟机支持这种虚拟机。  它具有和 DSVM 一样的核心 VM 映像（因此也具有相同的丰富工具集），但其配置更适合深度学习。 我们还为图像和文本理解提供端到端示例，广泛适用于许多现实生活 AI 方案。 深度学习虚拟机还会显示虚拟机上工具和示例的目录，让用户更容易发现 DSVM 上丰富的工具和示例。 在工具方面，深度学习虚拟机提供几种常用的深度学习框架，可用作获取和预处理图像、文本数据的工具。 如需全面的工具列表，可以参考[数据科学虚拟机概述页面](overview.md#whats-included-in-the-data-science-vm)。 

## <a name="next-steps"></a>后续步骤

按照以下步骤开始使用深度学习虚拟机：

* [预配深度学习虚拟机](provision-deep-learning-dsvm.md)
* [使用深度学习虚拟机](use-deep-learning-dsvm.md)
* [工具参考](dsvm-deep-learning-ai-frameworks.md)
* [示例](dsvm-samples-and-walkthroughs.md)
