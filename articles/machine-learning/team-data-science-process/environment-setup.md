---
title: 在 Azure 中设置数据科学环境 | Microsoft Docs
description: 在 Azure 中设置在团队数据科学过程中使用的数据科学环境。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 996c7b4332326ddf1ef18ca732677a85b6df6046
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345974"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>设置在团队数据科学过程中使用的数据科学环境
团队数据科学过程使用各种数据科学环境来完成数据的存储、处理和分析。 它们包括 Azure Blob 存储、几种类型的 Azure 虚拟机、HDInsight (Hadoop) 群集和 Azure 机器学习工作区。 关于要使用的环境类型的决策具体取决于要建模的数据类型和数量以及云中数据的目标目的地。 

* 有关做出此决策时要考虑的问题的指南，请参阅[规划 Azure 机器学习数据科学环境](plan-your-environment.md)。 
* 有关执行高级分析时可能遇到的某些方案的目录，请参阅[团队数据科学过程的方案](plan-sample-scenarios.md)

以下文章介绍如何设置团队数据科学过程使用的各种数据科学环境。

* [Azure 存储帐户](../../storage/common/storage-quickstart-create-account.md)
* [HDInsight (Hadoop) 群集](customize-hadoop-cluster.md)
* [Azure 机器学习工作室工作区](../studio/create-workspace.md)

**Microsoft 数据科学虚拟机 (DSVM)** 同时也可用作 Azure 虚拟机 (VM) 映像。 此 VM 使用几种常用工具（通常用于数据分析和机器学习）进行预安装和配置。 Windows 和 Linux 上均可使用 DSVM。 有关详细信息，请参阅[基于云的 Data Science Virtual Machine for Linux and Windows 简介](../data-science-virtual-machine/overview.md)。

了解创建方式：

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
- [深度学习 VM](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
