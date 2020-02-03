---
title: 在 Azure 中设置数据科学环境 - Team Data Science Process
description: 在 Azure 中设置在团队数据科学过程中使用的数据科学环境。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d11e1614307d5b6cb267ae72bae6f32adb4b244a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722214"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>设置在团队数据科学过程中使用的数据科学环境
团队数据科学过程使用各种数据科学环境来完成数据的存储、处理和分析。 它们包括 Azure Blob 存储、几种类型的 Azure 虚拟机、HDInsight (Hadoop) 群集和 Azure 机器学习工作区。 关于要使用的环境类型的决策具体取决于要建模的数据类型和数量以及云中数据的目标目的地。 

* 有关做出此决策时要考虑的问题的指南，请参阅[规划 Azure 机器学习数据科学环境](plan-your-environment.md)。 
* 有关执行高级分析时可能遇到的某些方案的目录，请参阅[团队数据科学过程的方案](plan-sample-scenarios.md)

以下文章介绍如何设置团队数据科学过程使用的各种数据科学环境。

* [Azure 存储帐户](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop) 群集](customize-hadoop-cluster.md)
* [Azure 机器学习 Studio （经典）工作区](../studio/create-workspace.md)

**Microsoft 数据科学虚拟机 (DSVM)** 同时也可用作 Azure 虚拟机 (VM) 映像。 此 VM 使用几种常用工具（通常用于数据分析和机器学习）进行预安装和配置。 Windows 和 Linux 上均可使用 DSVM。 有关详细信息，请参阅[基于云的 Data Science Virtual Machine for Linux and Windows 简介](../data-science-virtual-machine/overview.md)。

了解创建方式：

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
