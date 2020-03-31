---
title: 安排您的工作
description: 使用作业计划来管理任务。
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672428"
---
# <a name="schedule-jobs-for-efficiency"></a>计划作业以提高效率

通过计划 Batch 作业，您可以优先处理要首先运行的作业，同时考虑对其他任务具有依赖性的任务。 通过安排作业，可以确保使用最少的资源。 节点可以在不需要时停用，依赖于其他任务的任务只需及时优化工作流即可启动。 一次只运行一个作业。 在上一个完成之前，新的一个不会启动。 您可以将作业设置为自动完成。 

## <a name="benefit-of-job-scheduling"></a>作业日程安排的好处

计划作业的好处是您可以指定创建作业的计划。使用作业管理器任务计划的任务与作业相关联。 作业管理器任务将为作业创建任务。 为此，作业管理器任务需要使用 Batch 帐户进行身份验证。 使用AZ_BATCH_AUTHENTICATION_TOKEN访问令牌。 令牌将允许访问作业的其余部分。 

## <a name="use-the-portal-to-schedule-a-job"></a>使用门户安排作业

   1. 登录到[Azure 门户](https://portal.azure.com/)。

   2. 选择要在其中安排作业的批次帐户。

   3. 选择 **"添加"** 以创建新的作业计划并完成**基本窗体**。



![安排作业][1]

**作业计划 ID**：此作业计划的唯一标识符。

**显示名称**：作业的显示名称不必是唯一的，但最大长度为 1024 个字符。

**不要运行，直到**： 指定作业将运行的最早时间。 如果不设置此设置，计划将立即运行作业。

**不要在**： 之后运行 ： 此处设置的时间后不运行作业。 如果不指定时间，则创建定期作业计划，在显式终止之前保持活动状态。

**定期间隔**：您可以指定作业之间的时间量。 一次计划只能有一个作业，因此，如果是在作业计划下创建新作业，但以前的作业仍在运行，Batch 服务将不会创建新作业，直到上一个作业完成。  

**开始窗口**：在这里，您指定时间间隔，从计划指示应创建作业的时间开始，直到完成作业。 如果当前作业在其窗口期间未完成，则下一个作业将不会启动。

在基本窗体的底部，您将指定要在其上运行作业的池。 要查找池 ID 信息，请选择 **"更新**"。 

![指定池][2]


**池 ID**：标识要运行作业的池。

**作业配置任务**：选择 **"更新**"以命名作业管理器任务以及作业准备和发布任务（如果您正在使用）。

**优先级**：优先作业。

**最大挂钟时间**：设置作业可以运行的最大时间量。 如果无法在时间范围内完成，Batch 将终止作业。 如果不设置此设置，则作业没有时间限制。

**最大任务重试计数**：指定任务最多可以重试四次的次数。 这与 API 调用可能进行重试的次数不同。

**当所有任务完成**时 ：默认值为"无操作"。

**当任务失败时**：默认值为"无操作"。 如果重试计数已用尽或启动任务时出错，则任务将失败。 

选择 **"保存"** 后，如果转到左侧导航中的**作业计划**，则可以通过选择 **"执行信息"** 来跟踪作业的执行。


## <a name="for-more-information"></a>更多信息

要使用 Azure CLI 管理作业，请参阅[az 批处理作业计划](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest)。

## <a name="next-steps"></a>后续步骤

[创建任务依赖项以运行依赖于其他任务的任务](batch-task-dependencies.md)。





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


