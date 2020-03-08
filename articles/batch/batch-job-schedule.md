---
title: 计划作业
description: 使用作业计划管理任务。
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672428"
---
# <a name="schedule-jobs-for-efficiency"></a>计划作业以提高效率

通过计划批处理作业，你可以确定要首先运行的作业的优先级，同时考虑依赖于其他任务的任务。 通过计划作业，可以确保使用的资源量最少。 节点可以在不需要时取消配置，依赖于其他任务的任务会按时启动，同时优化工作流。 一次只能运行一个作业。 新的将不会启动，直到前一个完成。 你可以将作业设置为自动完成。 

## <a name="benefit-of-job-scheduling"></a>作业计划的优点

计划作业的好处是，您可以为作业创建指定计划。使用作业管理器任务计划的任务与作业相关联。 作业管理器任务将为作业创建任务。 为此，作业管理器任务需要与 Batch 帐户进行身份验证。 使用 AZ_BATCH_AUTHENTICATION_TOKEN 访问令牌。 该令牌将允许访问作业的其余部分。 

## <a name="use-the-portal-to-schedule-a-job"></a>使用门户计划作业

   1. 登录到 [Azure 门户](https://portal.azure.com/)。

   2. 选择要在其中计划作业的批处理帐户。

   3. 选择 "**添加**" 以创建新的作业计划并完成**基本窗体**。



![计划作业][1]

**作业计划 ID**：此作业计划的唯一标识符。

**显示名称**：作业的显示名称不必唯一，但最大长度为1024个字符。

**不要运行，直到**：指定运行作业的最早时间。 如果未设置此设置，则计划将立即开始运行作业。

**不要在此时间之后运行**：在此处设置的时间后没有作业运行。 如果未指定时间，则将创建定期作业计划，该计划将保持活动状态，直到你显式终止它。

**定期间隔**：可以指定作业之间的时间长度。 一次只能有一个作业在计划时，因此，如果在作业计划下创建新作业，但上一个作业仍在运行，则在上一个作业完成之前，批处理服务将不会创建新作业。  

**开始时段**：指定时间间隔，从计划指示应创建作业的时间开始，直到完成。 如果当前作业未在其窗口内完成，则无法启动下一个作业。

在基本窗体的底部，你将指定要在其上运行作业的池。 若要查找池 ID 信息，请选择 "**更新**"。 

![指定池][2]


**池 ID**：标识你将在其上运行作业的池。

**作业配置任务**：如果你正在使用作业管理器任务，请选择 "**更新**" 以命名该任务以及作业准备和释放任务。

**优先级**：为作业指定优先级。

**最大时钟时间时钟时间**：设置作业可运行的最长时间。 如果未在时间范围内完成，批处理将终止作业。 如果未设置此值，则作业不会有时间限制。

**最大任务重试次数**：指定任务最多可重试四次的次数。 这不同于 API 调用可能具有的重试次数。

**完成所有任务后**：默认值为 "无操作"。

**如果任务失败**：默认值为 "无操作"。 如果重试次数已用完或启动任务时出现错误，则任务失败。 

选择 "**保存**" 后，如果在左侧导航栏中转到 "**作业计划**"，则可以通过选择 "**执行信息**" 跟踪作业的执行。


## <a name="for-more-information"></a>更多信息

若要使用 Azure CLI 管理作业，请参阅[az batch job-schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest)。

## <a name="next-steps"></a>后续步骤

[创建任务依赖关系，以运行依赖于其他任务的任务](batch-task-dependencies.md)。





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


