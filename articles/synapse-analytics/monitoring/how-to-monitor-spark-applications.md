---
title: 监视 Apache Spark 应用程序
description: 使用 Azure Synapse Studio 监视 Apache Spark 应用程序。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430742"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>使用 Azure Synapse Studio （预览版）监视 Apache Spark 应用程序

使用 Azure Synapse Analytics，可以使用 Spark 在工作区中的 Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Spark 应用程序，使你能够关注最新的状态、问题和进度。

## <a name="accessing-the-list-of-spark-applications"></a>访问 Spark 应用程序的列表

若要查看工作区中 Spark 应用程序的列表，请先[打开 Azure Synapse Studio](https://web.azuresynapse.net/) ，然后选择工作区。

  > [!div class="mx-imgBorder"]
  > ![登录到工作区](./media/common/login-workspace.png)

打开工作区后，请选择左侧的 "**监视**" 部分。

  > [!div class="mx-imgBorder"]
  > ![选择监视中心](./media/common/left-nav.png)

选择 " **spark 应用程序**" 以查看 spark 应用程序的列表。

  > [!div class="mx-imgBorder"]
  > ![选择 Spark 应用程序](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>筛选 Spark 应用程序

你可以将 Spark 应用程序的列表筛选为你感兴趣的应用程序。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，你可以筛选视图以便仅查看包含名称 "sales" 的 Spark 应用程序：

  > [!div class="mx-imgBorder"]
  > ![“筛选器”按钮](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![示例筛选器](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>查看有关特定 Spark 应用程序的详细信息

若要查看有关某个 Spark 应用程序的详细信息，请选择 Spark 应用程序并查看详细信息。 如果 Spark 应用程序仍在运行，你可以监视进度。

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅[监视管道运行 Azure Synapse Studio](how-to-monitor-pipeline-runs.md)一文。  
