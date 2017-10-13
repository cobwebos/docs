---
title: "将数据从其他实验导入机器学习工作室 | Microsoft Docs"
description: "如何保存 Azure 机器学习工作室中的训练数据并将其用于其他实验。"
keywords: "导入数据, 数据, 数据源, 训练数据"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;bradsev
ms.openlocfilehash: cac35aa26fcdb7f2fb0db3b895d3c99d77f3b4ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>将数据从另一个试验导入 Azure 机器学习工作室
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

有时候想要从实验中提取直接结果，并将其用作其他实验的一部分。 为此，请将模块另存为数据集：

1. 单击想要另存为数据集的模块的输出。
2. 单击“另存为数据集”。
3. 出现提示时，请输入可轻松标识数据集的名称和描述。
4. 单击“确定”复选标记。

保存完成时，数据集将能在工作区的任何实验中使用。 可在模块面板的“保存的数据集”列表中找到它。

