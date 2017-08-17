---
title: "将 Azure 数据科学虚拟机设置为 IPython Notebook 服务器 | Microsoft Docs"
description: "将数据科学虚拟机设置为具有支持工具的 IPython Notebook 服务器。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 95e1fa87-794a-4d03-80a4-af4f3f3ac31e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: db1ffb2a226a087ecea2ea6f560c6b803e33d8c7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/22/2017

---
# <a name="provision-azure-data-science-virtual-machines-as-ipython-notebook-servers"></a>将 Azure 数据科学虚拟机设置为 IPython Notebook 服务器
此处介绍如何设置 Azure VM 和以 SQL 服务作为 IPython Notebook 服务器的 Azure VM。 使用支持工具配置 Windows 虚拟机，如 IPython Notebook、Azure 存储资源管理器和 AzCopy 以及其他可用于对数据科学项目的实用程序。 例如，Azure 存储资源管理器和 AzCopy 提供了从本地计算机将数据上传到 Azure 存储或从存储下载到本地计算机的便捷方法。 

此菜单链接到介绍如何设置用于[团队数据科学过程 (TDSP)](data-science-process-overview.md)的各种数据科学环境的主题。

[!INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

可以预配和配置多种类型的 Azure 虚拟机，以用作基于云的数据科学环境的一部分。 关于要使用的虚拟机风格的决策具体取决于要使用机器学习进行建模的数据类型和数量以及云中数据的目标目的地。 

* 有关做出此决策时要考虑的问题的指南，请参阅[规划 Azure 机器学习数据科学环境](machine-learning-data-science-plan-your-environment.md)。 
* 有关执行高级分析时可能遇到的某些方案的目录，请参阅 [Azure 机器学习中高级分析过程和技术的方案](machine-learning-data-science-plan-sample-scenarios.md)

提供了两套说明：

* [将 Azure 虚拟机设置为用于高级分析的 IPython Notebook 服务器](machine-learning-data-science-setup-virtual-machine.md)显示了如何使用 IPython Notebook 和其他用于执行数据科学的工具来配置 Azure 虚拟机，以便可以使用除 SQL 之外的 Azure 存储形式存储数据。
* [将 Azure SQL Server 虚拟机设置为用于高级分析的 IPython Notebook 服务器](machine-learning-data-science-setup-sql-server-virtual-machine.md)显示了如何使用 IPython Notebook 和其他用于执行数据科学的工具来配置 Azure SQL Server 虚拟机，以便可以使用 SQL 数据库存储数据。

预配并配置后，这些虚拟机就可以作为 IPython Notebook 服务器用于数据的浏览和处理，并连接 Azure 机器学习和团队数据科学过程 (TDSP) 所需的其他任务。 数据科学过程中的后续步骤映射在[TDSP 学习路径](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中，并且可能包括将数据移到 SQL Server 或 HDInsight 的步骤，并对该步骤进行采样、处理，以便使用 Azure 机器学习来学习数据。

> [!NOTE]
> Azure 虚拟机定价为**只为自己使用的东西付费**。 为了确保不会在不使用虚拟机时付费，它必须位于 [Azure 经典门户](http://manage.windowsazure.com/)的“已停止(已解除分配)”状态。 有关分步说明或如何解除分配虚拟机的信息，请参阅[关闭并在不使用虚拟机时取消分配](machine-learning-data-science-setup-virtual-machine.md#shutdown)
> 
> 


