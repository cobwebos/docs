---
title: 监控阿帕奇火花应用程序
description: 使用 Azure 同步工作室监视 Apache Spark 应用程序。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430742"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>使用 Azure 同步工作室（预览）监视 Apache Spark 应用程序

使用 Azure 同步分析，可以使用 Spark 在工作区中的 Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Spark 应用程序，使您能够关注最新的状态、问题和进度。

## <a name="accessing-the-list-of-spark-applications"></a>访问 Spark 应用程序列表

要查看工作区中的 Spark 应用程序列表，请首先[打开 Azure 同步工作室](https://web.azuresynapse.net/)并选择工作区。

  > [!div class="mx-imgBorder"]
  > ![登录到工作区](./media/common/login-workspace.png)

打开工作区后，选择左侧的 **"监视器"** 部分。

  > [!div class="mx-imgBorder"]
  > ![选择监视器中心](./media/common/left-nav.png)

选择**Spark 应用程序**以查看 Spark 应用程序的列表。

  > [!div class="mx-imgBorder"]
  > ![选择 Spark 应用程序](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>筛选 Spark 应用程序

您可以将 Spark 应用程序列表筛选为您感兴趣的应用程序。 屏幕顶部的筛选器允许您指定要筛选的字段。

例如，您可以筛选视图以查看仅查看包含名称为"销售"的 Spark 应用程序：

  > [!div class="mx-imgBorder"]
  > ![“筛选器”按钮](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![样品过滤器](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>查看有关特定 Spark 应用程序的详细信息

要查看有关其中一个 Spark 应用程序的详细信息，请选择 Spark 应用程序并查看详细信息。 如果 Spark 应用程序仍在运行，则可以监视进度。

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅[监视器管道运行 Azure Synapse Studio](how-to-monitor-pipeline-runs.md)一文。  
