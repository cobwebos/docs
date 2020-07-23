---
title: 检查作业和任务错误
description: 对作业和任务进行错误检查并排查故障
author: mscurrell
ms.topic: how-to
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: ece0f1473b3c453ca5506f06b7ef094533d146aa
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964322"
---
# <a name="job-and-task-error-checking"></a>作业和任务错误检查

添加作业和任务时可能会出现各种错误。 检测这些操作的错误的方式非常直接，因为任何错误都会由 API、CLI 或 UI 立即返回。  不过，有些错误可能要在计划和运行作业及任务后才会出现。

本文介绍在提交作业和任务后可能会发生的错误。 列出并解释了需要检查和处理的错误。

## <a name="jobs"></a>作业

作业是一个或多个任务的分组，这些任务实际上指定要运行的命令行。

添加作业时，可以指定以下影响作业失败方式的参数：

- [作业约束](/rest/api/batchservice/job/add#jobconstraints)
  - 可以选择指定 `maxWallClockTime` 属性，用于设置作业可处于活动或运行状态的最大时间量。 如果超过此限制，作业将终止，并在作业的 [executionInfo](/rest/api/batchservice/job/get#cloudjob) 中设置 `terminateReason` 属性。
- [作业准备任务](/rest/api/batchservice/job/add#jobpreparationtask)
  - 如果指定此参数，则在第一次为节点上的作业运行任务时运行作业准备任务。 作业准备任务可能会失败，这会导致任务未运行且作业未完成。
- [作业发布任务](/rest/api/batchservice/job/add#jobreleasetask)
  - 只有在配置了作业准备任务后，才能指定作业发布任务。 终止作业时，作业发布任务会在运行作业准备任务的每个池节点上运行。 作业发布任务可能会失败，但作业仍会变为 `completed` 状态。

### <a name="job-properties"></a>作业属性

应检查以下作业属性是否存在错误：

- “[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation)”：
  - `terminateReason` 属性的值可以指示已超过作业约束中指定的 `maxWallClockTime`，从而终止了作业。 如果正确设置了作业 `onTaskFailure` 属性，还可以将上述属性设置为指示任务失败。
  - 如果出现计划错误，则设置 [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) 属性。
 
### <a name="job-preparation-tasks"></a>作业准备任务

如果为作业指定作业准备任务，那么，当第一次在节点上运行作业的任务时，将运行该任务的实例。 对作业配置的作业准备任务可以看作是一个任务模板，可以运行多个作业准备任务实例，最多可运行一个池中的节点数。

应检查作业准备任务实例，确定是否存在错误：
- 运行作业准备任务时，触发作业准备任务的那个任务将变为 `preparing` [状态](/rest/api/batchservice/task/get#taskstate)；如果作业准备任务随后失败，触发任务将恢复为 `active` 状态，并且不会运行。  
- 可以使用[列出准备和发布任务状态](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API 从作业中获取所有已运行的作业准备任务实例。 与任何任务一样，[执行信息](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)可与 `failureInfo`、`exitCode` 和 `result` 等属性一起使用。
- 如果作业准备任务失败，将不会运行触发作业任务，作业将无法完成并处于停滞状态。 如果没有其他作业包含可计划的任务，池可能会闲置。

### <a name="job-release-tasks"></a>作业发布任务

如果为作业指定作业发布任务，当终止某个作业时，将在运行作业准备任务的每个池节点上运行作业发布任务的实例。  应检查作业发布任务实例，确定是否存在错误：
- 可以使用[列出准备和发布任务状态](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API 从作业中获取所有正在运行的作业发布任务实例。 与任何任务一样，[执行信息](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)可与 `failureInfo`、`exitCode` 和 `result` 等属性一起使用。
- 如果一个或多个作业发布任务失败，该作业仍会终止，并变为 `completed` 状态。

## <a name="tasks"></a>任务

以下几个原因可能导致作业任务失败：

- 任务命令行失败，返回非零退出代码。
- 为任务指定了 `resourceFiles`，但出现了一个错误，指示一个或多个文件未下载。
- 为任务指定了 `outputFiles`，但出现了一个错误，指示一个或多个文件未上传。
- 超出了任务[约束](/rest/api/batchservice/task/add#taskconstraints)中的 `maxWallClockTime` 属性指定的任务运行时间。

在所有情况下，都必须检查以下属性是否存在错误，并查看错误相关信息：
- 任务 [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) 属性包含可提供错误相关信息的多个属性。 [result](/rest/api/batchservice/task/get#taskexecutionresult) 指示任务是否因任何原因而失败，`exitCode` 和 `failureInfo` 提供有关失败的详细信息。
- 无论任务成功还是失败，任务将始终变为 `completed` [状态](/rest/api/batchservice/task/get#taskstate)。

必须考虑任务失败对作业和所有任务依赖项的影响。  可为任务指定 [exitConditions](/rest/api/batchservice/task/add#exitconditions) 属性，以便为依赖项和作业配置操作。
- 对于依赖项，[DependencyAction](/rest/api/batchservice/task/add#dependencyaction) 控制依赖于失败任务的那些任务是被阻止还是运行。
- 对于作业，[JobAction](/rest/api/batchservice/task/add#jobaction) 控制失败任务是导致作业被禁用、终止还是保持不变。

### <a name="task-command-line-failures"></a>任务命令行错误

运行任务命令行时，输出将被写入 `stderr.txt` 和 `stdout.txt`。 此外，应用程序还可以写入特定于应用程序的日志文件。

如果用于运行任务的池节点仍存在，则可以获取并查看日志文件。 例如，Azure 门户将列出并可以查看任务或池节点的日志文件。 通过多个 API 还可以列出和获取任务文件，如[从任务中获取](/rest/api/batchservice/file/getfromtask)。

由于池和池节点通常是临时性的，会不断地添加和删除节点，因此建议保留日志文件。 [任务输出文件](./batch-task-output-files.md)是将日志文件保存到 Azure 存储的一种简便方法。

### <a name="output-file-failures"></a>输出文件错误
每次上传文件时，Batch 都会将以下两个日志文件写入计算节点：`fileuploadout.txt` 和 `fileuploaderr.txt`。 可以检查这些日志文件来详细了解具体的失败情况。 如果从未尝试上传文件（例如，因为任务本身无法运行），则这些日志文件不会存在。  

## <a name="next-steps"></a>后续步骤

检查应用程序是否实现了全面的错误检查；及时检测和诊断问题非常重要。
