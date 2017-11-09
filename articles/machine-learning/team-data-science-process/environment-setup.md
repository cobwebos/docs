---
title: "在 Azure 中设置数据科学环境 | Microsoft Docs"
description: "在 Azure 中设置在团队数据科学过程中使用的数据科学环境。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: bradsev
ms.openlocfilehash: 00f767bffc86eceb16e3e69a205983af6ba4703b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>设置在团队数据科学过程中使用的数据科学环境
团队数据科学过程使用各种数据科学环境来完成数据的存储、处理和分析。 它们包括 Azure Blob 存储、几种类型的 Azure 虚拟机、HDInsight (Hadoop) 群集和 Azure 机器学习工作区。 关于要使用的环境类型的决策具体取决于要建模的数据类型和数量以及云中数据的目标目的地。 

* 有关做出此决策时要考虑的问题的指南，请参阅[规划 Azure 机器学习数据科学环境](plan-your-environment.md)。 
* 有关执行高级分析时可能遇到的某些方案的目录，请参阅[团队数据科学过程的方案](plan-sample-scenarios.md)

此菜单链接到介绍如何设置用于团队数据科学过程的各种数据科学环境的主题。

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

**Microsoft 数据科学虚拟机 (DSVM)** 同时也可用作 Azure 虚拟机 (VM) 映像。 此 VM 使用几种常用工具（通常用于数据分析和机器学习）进行预安装和配置。 Windows 和 Linux 上均可使用 DSVM。 有关详细信息，请参阅[适用于 Linux 和 Windows 的基于云的数据科学虚拟机简介](../data-science-virtual-machine/overview.md)。

