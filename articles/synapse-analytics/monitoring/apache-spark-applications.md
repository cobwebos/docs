---
title: 使用 Synapse Studio 监视 Apache Spark 应用程序
description: 使用 Synapse Studio 监视 Apache Spark 应用程序。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ecc34b84e5be2d0470cc5a58a71a70e9ffcab1b6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052553"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>使用 Synapse Studio 监视 Apache Spark 应用程序

使用 Azure Synapse Analytics，可以使用 Apache Spark 在工作区中的 Apache Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 应用程序，使你能够关注最新的状态、问题和进度。

本教程涵盖以下任务：

* 运行 Apache Spark 应用程序的监视器
* 查看已完成的 Apache Spark 应用程序
* 查看已取消 Apache Spark 应用程序
* 调试失败的 Apache Spark 应用程序

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，请确保满足以下要求：

- Synapse Studio 工作区。 有关说明，请参阅[创建 Synapse Studio 工作区](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)。

- Apache Spark 池。

## <a name="monitor-running-apache-spark-application"></a>运行 Apache Spark 应用程序的监视器

打开 "**监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关正在运行的 Apache Spark 应用程序的详细信息，请选择 "提交 Apache Spark 应用程序并查看详细信息。 如果 Apache Spark 的应用程序仍在运行，则可以监视进度。

  ![选择正在运行的作业](./media/how-to-monitor-spark-applications/select-running-job.png)

1. 检查 "**已完成的任务**"、"**状态**" 和 "**总持续时间**"。

2. 取消 Apache Spark 应用程序。

3. 刷新日志查询。

4. 查看图形。

5. 检查**摘要**信息。

6. 检查**日志**。 日志信息在运行时为空。

    ![查看正在运行的作业](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>查看已完成的 Apache Spark 应用程序

打开 "**监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关已完成的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

  ![选择已完成的作业](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. 检查 "**已完成的任务**"、"**状态**" 和 "**总持续时间**"。

2. 刷新日志查询。

3. 单击 " **Spark history server**" 打开 Apache Spark history server 链接 "。

4. 通过单击关系图中的图标来检查**摘要**信息。

5. 检查**日志**。 你可以从下拉列表中选择不同类型的日志，你可以通过单击 "**下载日志**" 来下载日志信息。

6. 可以在生成的作业关系图中查看作业的概述。 默认情况下，该图显示所有作业。 可按**作业 ID**筛选此视图。

7. 默认情况下，**进度**显示处于选中状态。 可以通过在 "**显示**" 下拉列表中选择 "**读取**" 或 "**写入**" 来检查数据流。

8. 若要播放作业，请选择 "**播放**"。 你可以随时选择 "**停止**"。

9. 使用鼠标滚动在作业图上放大和缩小，或选择 "**缩放到合适大小**" 以使其适应屏幕大小。

10. 作业图形节点显示每个阶段的下列信息：

    * ID。

    * 名称或说明。

    * 总任务数。

    * 读取的数据：输入大小和随机读取大小之和。

    * 数据写入：输出大小和无序写入大小的总和。

    * 执行时间：从第一次尝试的开始时间到最后一次尝试的完成时间的时间和。

    * 行计数：输入记录、输出记录、随机读取记录和随机写入记录的总和。

    * 进度。

     ![查看已完成作业](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. 单击图形，将显示 "阶段的详细信息"。

   ![阶段的详细信息](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>查看已取消 Apache Spark 应用程序

打开 "**监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关已取消的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

 ![选择已取消作业](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. 检查 "**已完成的任务**"、"**状态**" 和 "**总持续时间**"。

2. 刷新日志查询。

3. 通过单击 " **Spark 历史记录服务器**" 打开 Apache history 服务器链接。

4. 查看图形。

5. 检查**摘要**信息。

6. 检查**日志**。 你可以从下拉列表中选择不同类型的日志，你可以通过单击 "**下载日志**" 来下载日志信息。

   ![查看已取消作业](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>调试失败的 Apache Spark 应用程序

打开 "**监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关失败的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

![选择失败的作业](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. 检查 "**已完成的任务**"、"**状态**" 和 "**总持续时间**"。

2. 刷新日志查询。

3. 单击 " **Spark history server**" 打开 Apache Spark history server 链接 "。

4. 查看图形。

5. 检查**摘要**信息。

6. 检查错误信息。

   ![失败的作业信息](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅[使用 Synapse Studio 运行监视管道](how-to-monitor-pipeline-runs.md)一文。  
