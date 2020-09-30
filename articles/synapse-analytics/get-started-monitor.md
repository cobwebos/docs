---
title: 教程：Azure Synapse Analytics 入门 - 监视 Synapse 工作区
description: 在本教程中，你将了解如何监视 Synapse 工作区中的活动。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: f8be96fb008471d040839141a230c13b8f1657a5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708166"
---
# <a name="monitor-your-synapse-workspace"></a>监视 Synapse 工作区

在本教程中，你将了解如何监视 Synapse 工作区中的活动。 你可以监视 SQL、Apache Spark 和 Pipelines 的当前和历史 活动。 

## <a name="introduction-to-the-monitor-hub"></a>监视中心简介

打开 Synapse Studio 并导航到“监视”中心。 在此处，你可以查看工作区中所有活动的历史记录，以及目前处于有效状态的活动。 

* 在“业务流程”下，可监视管道、触发器和集成运行时
* 在“活动”下，可监视 Spark 和 SQL 活动。 

## <a name="orchestration"></a>业务流程

1. 导航到“业务流程”>“Pipeline”。 在此视图中，你可以查看每次管道在工作区中运行时的状况。 
1. 找到在先前步骤中运行的管道，然后单击其管道名称。
1. 现在，你可以查看该管道中各个活动的运行方式。
1. 单击 Synapse Studio 顶部附近的“痕迹导航栏”，再单击“所有管道运行”以返回到先前视图 。

## <a name="apache-spark-activities"></a>Apache Spark 活动

1. 导航到“业务流程”>“活动”>“Apache Spark 应用程序”。 现在，你可以查看正在工作区中运行或已经在工作区中运行的所有 Spark 应用程序。
1. 找到一个已不再运行的应用程序，然后单击其应用程序名称。 现在，你可以查看 Spark 应用程序的详细信息。
1. 如果你熟悉 Apache Spark，则可以通过单击 Spark 历史记录服务器来找到标准 Apache Spark 历史记录服务器 UI。

## <a name="sql-activities"></a>SQL 活动

1. 导航到“业务流程”>“活动”>“SQL 请求”。
1. 在此视图中，你可以查看 SQL 请求。
1. 选择要监视的池。 现在，你可以查看正在池的工作区中运行或已经在池的工作区中运行的所有 SQL 请求。
1. 查找特定的 SQL 请求，并将鼠标悬停在该项上。 悬停后，将看到“SQL 脚本”图标出现。
1. 单击“SQL 脚本”图标以查看 SQL 请求全文。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [探索知识中心](get-started-knowledge-center.md)
