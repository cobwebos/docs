---
title: 如何监视 Apache Spark 应用程序
description: 使用 Azure Synapse Studio 监视 Apache Spark 应用程序。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: e646f1bc1a25f58dd54437cbd77750479ac9ae99
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970887"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>使用 Azure Synapse Studio （预览版）监视 Apache Spark 应用程序

使用 Azure Synapse Analytics，可以使用 Spark 在工作区中的 Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 应用程序，使你能够关注最新的状态、问题和进度。

## <a name="accessing-the-list-of-apache-spark-applications"></a>访问 Apache Spark 应用程序的列表

若要查看工作区中 Apache Spark 应用程序的列表，请先[打开 Azure Synapse Studio](https://web.azuresynapse.net/) ，然后选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，请选择左侧的 "**监视**" 部分。

![选择监视中心](./media/common/left-nav.png)

选择**Apache Spark 应用程序**以查看 Apache Spark 应用程序的列表。

 ![选择 Spark 应用程序](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>筛选 Apache Spark 应用程序

你可以将 Apache Spark 应用程序的列表筛选为你感兴趣的应用程序的列表。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，你可以筛选视图以便仅查看包含名称 "sales" 的 Apache Spark 应用程序：

![“筛选器”按钮](./media/common/filter-button.png)

![示例筛选器](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>查看有关特定 Apache Spark 应用程序的详细信息

若要查看有关某个 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。 如果 Apache Spark 的应用程序仍在运行，则可以监视进度。 [了解详细信息](apache-spark-applications.md)。

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅[监视管道运行 Azure Synapse Studio](how-to-monitor-pipeline-runs.md)一文。 

有关 Apache Spark 应用程序进行调试的详细信息，请参阅[监视 Azure Synapse Studio 上的 Apache Spark 应用程序](apache-spark-applications.md)。