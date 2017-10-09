---
title: "对 Azure Blob 容器、SQL Server 和 Hive 表中的数据采样 | Microsoft Docs"
description: "如何浏览存储在各个 Azure 环境中的数据。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1b99ce11709376294f6a2385b9f4ff3388b99ab7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="heading"></a>对 Azure Blob 容器、SQL Server 和 Hive 表中的数据采样
本文档所链接到的主题介绍如何对存储在三个不同 Azure 位置之一的数据采样：

* **Azure Blob 容器数据**的采样方法是先以编程方式下载该数据，并使用样本 Python 代码对其采样。
* **SQL Server 数据**通过使用 SQL 和 Python 编程语言采样。 
* **Hive 表数据**通过使用 Hive 查询采样。

以下**菜单**所链接到的主题介绍如何从这些 Azure 存储环境之一采样数据。 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

此采样任务是[团队数据科学流程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

**为什么对数据采样？**

如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 这有利于数据了解、探索和功能设计。 它在 Cortana Analytics 进程中的作用是能够快速建立数据处理函数和机器学习模型的快速原型。


