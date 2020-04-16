---
title: 监视器管道运行 Azure 同步工作室（预览版）
description: 使用 Azure 同步工作室监视工作区管道运行。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430781"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>使用 Azure 同步工作室监视工作区管道运行

借助 Azure Synapse 分析，您可以创建复杂的管道，以便自动协调解决方案中的数据移动、数据转换和计算活动。 您可以使用 Azure 同步工作室（预览）创作和监视这些管道。

本文介绍如何监视管道运行，这允许您关注管道的最新状态、问题和进度。

## <a name="access-the-list-of-pipeline-runs"></a>访问管道运行列表

要查看工作区中运行的管道列表，请首先打开[Azure 同步工作室](https://web.azuresynapse.net/)并选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，选择左侧的 **"监视器"** 部分。

![选择监视器中心](./media/common/left-nav.png)

选择 **"管道运行"** 以查看管道运行的列表。

![选择管道运行](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>筛选管道运行

您可以筛选管道运行到您感兴趣的管道列表。 屏幕顶部的筛选器允许您指定要筛选的字段。

例如，您可以筛选视图以查看仅运行名为"假日"的管道的管道：

![“筛选器”按钮](./media/common/filter-button.png)

![样品过滤器](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>查看有关特定管道运行的详细信息

要查看有关管道运行的详细信息，请选择管道运行。 然后查看与管道运行关联的活动运行。 如果管道仍在运行，您可以监视进度。 
  
## <a name="next-steps"></a>后续步骤

要了解有关监视应用程序的更多信息，请参阅监视器[Apache Spark 应用程序](how-to-monitor-spark-applications.md)一文。 
