---
title: 使用 Synapse Studio 运行监视管道
description: 使用 Synapse Studio 监视工作区管道运行。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7676f4aeeb9485ce5e3a702027884bc54d79a863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387313"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>使用 Synapse Studio 监视工作区管道运行

借助 Azure Synapse Analytics，可以创建复杂的管道，使其能够在解决方案中自动化和协调数据移动、数据转换和计算活动。 可以使用 Synapse Studio (preview) 创作和监视这些管道。

本文介绍如何监视管道运行，从而使你能够关注管道的最新状态、问题和进度。

## <a name="access-pipeline-runs-list"></a>访问管道运行列表

若要查看工作区中运行的管道列表，请先 [打开 Synapse Studio](https://web.azuresynapse.net/) ，然后选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，请选择左侧的 " **监视** " 部分。

![选择监视中心](./media/common/left-nav.png)

选择 " **管道运行** " 以查看管道运行的列表。

![选择管道运行](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>筛选管道运行

你可以将管道运行列表筛选为你感兴趣的运行列表。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，可以对视图进行筛选，以便仅查看名为 "假日" 的管道的管道运行：

![“筛选器”按钮](./media/common/filter-button.png)

![示例筛选器](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>查看有关特定管道运行的详细信息

若要查看有关管道运行的详细信息，请选择管道运行。 然后查看与管道运行关联的活动运行。 如果管道仍在运行，你可以监视进度。 
  
## <a name="next-steps"></a>后续步骤

若要了解有关监视应用程序的详细信息，请参阅 [监视 Apache Spark 应用程序](how-to-monitor-spark-applications.md) 。 
