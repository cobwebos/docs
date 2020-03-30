---
title: 检查作业和任务错误 - Azure 批处理 |微软文档
description: 要检查的错误和作业和任务故障
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087009"
---
# <a name="job-and-task-error-checking"></a>作业和任务错误检查

添加作业和任务时可能会出现各种错误。 检测这些操作的故障非常简单，因为 API、CLI 或 UI 会立即返回任何故障。  但是，在计划并运行作业和任务时，以后可能会发生一些失败。

本文介绍提交作业和任务后可能发生的错误。 它列出并解释需要检查和处理的错误。

## <a name="jobs"></a>作业

作业是一个或多个任务的分组，任务实际上指定要运行的命令行。

添加作业时，可以指定以下参数，这些参数可能会影响作业失败的方式：

- [作业约束](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - 可以选择`maxWallClockTime`指定该属性以设置作业可以处于活动状态或运行的最大时间量。 如果超过，该作业将终止与在作业`terminateReason`的执行[信息](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob)中设置的属性。
- [工作准备任务](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - 如果指定，则首次为节点上的作业运行任务时运行作业准备任务。 作业准备任务可能会失败，这将导致任务未运行，作业未完成。
- [作业发布任务](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - 仅当配置了作业准备任务时，才能指定作业发布任务。 终止作业时，作业释放任务将在运行作业准备任务的每个池节点上运行。 作业释放任务可能会失败，但作业仍将移动到状态`completed`。

### <a name="job-properties"></a>作业属性

应检查以下作业属性是否存在错误：

- '[执行信息](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)'：
  - 属性`terminateReason`可以具有指示`maxWallClockTime`超过 作业约束中指定的 的值，因此作业已终止。 如果作业`onTaskFailure`属性设置得当，也可以将其设置为指示任务失败。
  - 如果出现计划错误，则设置[计划错误](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror)属性。
 
### <a name="job-preparation-tasks"></a>工作准备任务

如果为作业指定了作业准备任务，则该任务的实例将在首次在节点上运行该作业的任务时运行该任务。 作业上配置的作业准备任务可视为任务模板，运行多个作业准备任务实例，最多为池中的节点数。

应检查作业准备任务实例以确定是否存在错误：
- 运行作业准备任务时，触发作业准备任务的任务将移动到[state](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)`preparing`如果作业准备任务随后失败，触发任务将恢复为状态`active`，并且不会运行。  
- 可以使用[列表准备和发布任务状态](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)API 从作业获取已运行的作业准备任务的所有实例。 与任何任务一样，属性（[execution information](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)如`failureInfo`）`exitCode`和`result`.
- 如果作业准备任务失败，则触发作业任务将不会运行，作业将无法完成并卡住。 如果没有具有可计划任务的其他作业，池可能会未使用。

### <a name="job-release-tasks"></a>作业发布任务

如果为作业指定了作业释放任务，则当作业被终止时，将在运行作业准备任务的每个池节点上运行作业释放任务的实例。  应检查作业释放任务实例以确定是否存在错误：
- 可以使用 API[列表准备和发布任务状态](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)从作业获取正在运行的作业发布任务的所有实例。 与任何任务一样，属性（[execution information](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)如`failureInfo`）`exitCode`和`result`.
- 如果一个或多个作业释放任务失败，则作业仍将终止并移动到状态`completed`。

## <a name="tasks"></a>任务

作业任务可能由于多种原因失败：

- 任务命令行失败，返回时带有非零退出代码。
- 为`resourceFiles`任务指定了任务，但失败意味着一个或多个文件未下载。
- 为`outputFiles`任务指定了任务，但失败意味着一个或多个文件未上载。
- 超过任务`maxWallClockTime`[约束](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)中的属性指定的任务的已用时间。

在所有情况下，都必须检查以下属性是否存在错误和有关错误的信息：
- 任务[执行信息](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation)属性包含多个属性，这些属性提供有关错误的信息。 [结果](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult)指示任务是否由于任何原因失败，并`exitCode``failureInfo`提供有关故障的详细信息。
- 任务将始终移动到`completed`[状态](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)，而不管它是成功还是失败。

必须考虑任务失败对作业和任何任务依赖项的影响。  可以为任务指定[exit条件](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions)属性，以配置依赖项和作业的操作。
- 对于依赖项，[依赖项操作](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction)控制依赖于失败任务的任务是被阻止还是正在运行。
- 对于作业[，JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction)控制失败的任务是否导致作业被禁用、终止或保持不变。

### <a name="task-command-line-failures"></a>任务命令行失败

运行任务命令行时，输出将`stderr.txt`写入 和`stdout.txt`。 此外，应用程序可能会写入特定于应用程序的日志文件。

如果运行任务的池节点仍然存在，则可以获取和查看日志文件。 例如，Azure 门户列出并可以查看任务或池节点的日志文件。 多个 API 还允许列出和获取任务文件，例如[从任务获取](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask)。

由于池和池节点经常是短暂的，节点被不断添加和删除，因此建议保留日志文件。 [任务输出文件](https://docs.microsoft.com/azure/batch/batch-task-output-files)是将日志文件保存到 Azure 存储的便捷方法。

### <a name="output-file-failures"></a>输出文件失败
每次上传文件时，Batch 都会将以下两个日志文件写入计算节点：`fileuploadout.txt` 和 `fileuploaderr.txt`。 可以检查这些日志文件来详细了解具体的失败情况。 在从未尝试文件上载的情况下，例如，由于任务本身无法运行，则这些日志文件将不存在。  

## <a name="next-steps"></a>后续步骤

检查应用程序是否实现全面的错误检查;及时检测和诊断问题至关重要。
