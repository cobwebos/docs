---
title: 检查作业和任务错误
description: 用于检查作业和任务并对其进行故障排除的错误
author: mscurrell
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 0c58bdf50f3e69b2b7d18f750f94fecdb512af85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116020"
---
# <a name="job-and-task-error-checking"></a>作业和任务错误检查

添加作业和任务时可能会出现各种错误。 检测这些操作的失败非常简单，因为 API、CLI 或 UI 会立即返回任何故障。  但是，在计划和运行作业和任务后，可能会发生失败。

本文介绍了在提交作业和任务后可能会发生的错误。 它列出并解释了需要检查和处理的错误。

## <a name="jobs"></a>作业

作业是一个或多个任务的分组，这些任务实际上指定要运行的命令行。

添加作业时，可以指定以下参数，这些参数会影响作业的失败方式：

- [作业约束](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - 可以`maxWallClockTime`根据需要指定属性，以设置作业可以处于活动状态或正在运行的最长时间。 如果超过此限制，将终止该作业， `terminateReason`并在[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob)中为该作业设置属性。
- [作业准备任务](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - 如果已指定，则在第一次为节点上的作业运行任务时运行作业准备任务。 作业准备任务可能会失败，这会导致任务未运行且作业未完成。
- [作业释放任务](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - 如果配置了作业准备任务，则只能指定作业释放任务。 终止作业时，作业释放任务会在运行作业准备任务的每个池节点上运行。 作业释放任务可能会失败，但该作业仍将进入`completed`状态。

### <a name="job-properties"></a>作业属性

应检查以下作业属性是否有错误：

- "[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)"：
  - `terminateReason`属性可以包含值，以指示已超过`maxWallClockTime`作业约束中指定的，因此作业已终止。 如果正确设置了作业`onTaskFailure`属性，还可以将其设置为指示任务失败。
  - 如果存在计划错误，则设置[schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror)属性。
 
### <a name="job-preparation-tasks"></a>作业准备任务

如果为作业指定了作业准备任务，则将在第一次在节点上运行作业的任务时运行该任务的实例。 在作业上配置的作业准备任务可以被视为任务模板，运行多个作业准备任务实例，直至池中的节点数。

应检查作业准备任务实例，以确定是否存在错误：
- 运行作业准备任务时，触发作业准备任务的任务将转到的`preparing`[状态](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate);如果作业准备任务失败，则触发任务将恢复为`active`状态，并且将不会运行。  
- 可以使用[列表准备和释放任务状态](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)API 从作业中获取已运行的作业准备任务的所有实例。 与任何任务一样，有可用于`failureInfo`、 `exitCode`和`result`等属性的[执行信息](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)。
- 如果作业准备任务失败，则不会运行触发作业任务，作业将无法完成并且会停滞。 如果没有其他作业包含可计划的任务，则该池可能会未利用。

### <a name="job-release-tasks"></a>作业释放任务

如果为作业指定了作业释放任务，则当终止某个作业时，将在运行作业准备任务的每个池节点上运行作业释放任务的实例。  应检查作业释放任务实例，以确定是否存在错误：
- 可以使用 API[列表准备和释放任务状态](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)从作业中获取正在运行的作业释放任务的所有实例。 与任何任务一样，有可用于`failureInfo`、 `exitCode`和`result`等属性的[执行信息](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)。
- 如果一个或多个作业释放任务失败，则该作业仍将终止并移至`completed`状态。

## <a name="tasks"></a>任务

作业任务可能因多种原因而失败：

- 任务命令行失败，返回非零退出代码。
- 已为`resourceFiles`任务指定，但出现了一个错误，即一个或多个文件未下载。
- 已为`outputFiles`任务指定，但出现了一个错误，即一个或多个文件未上传。
- 超出了任务`maxWallClockTime` [约束](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)中的属性指定的该任务所经过的时间。

在所有情况下，都必须检查以下属性的错误和有关错误的信息：
- Tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation)属性包含提供有关错误的信息的多个属性。 [结果](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult)指示任务是否因某种原因而失败， `exitCode`并`failureInfo`提供有关失败的详细信息。
- 该任务将始终移动到`completed` [状态](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)，而不管它是成功还是失败。

任务失败对作业和所有任务依赖项的影响必须考虑在内。  可为任务指定[exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions)属性以配置依赖项的操作和作业。
- 对于依赖项， [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction)控制依赖于失败的任务的任务是被阻止还是运行。
- 对于作业， [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction)控制失败的任务是导致作业被禁用、终止还是保持不变。

### <a name="task-command-line-failures"></a>任务命令行失败

运行任务命令行时，输出将写入到`stderr.txt`和。 `stdout.txt` 此外，应用程序还可以写入特定于应用程序的日志文件。

如果运行任务的池节点仍存在，则可以获取和查看日志文件。 例如，Azure 门户列出并可以查看任务或池节点的日志文件。 多个 Api 还允许列出和获取任务文件，如 "[从任务获取](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask)"。

由于池和池节点经常是暂时的，因此，如果节点持续添加和删除，则建议保留日志文件。 [任务输出文件](https://docs.microsoft.com/azure/batch/batch-task-output-files)是将日志文件保存到 Azure 存储的一种简便方法。

### <a name="output-file-failures"></a>输出文件失败
每次上传文件时，Batch 都会将以下两个日志文件写入计算节点：`fileuploadout.txt` 和 `fileuploaderr.txt`。 可以检查这些日志文件来详细了解具体的失败情况。 如果从未尝试过文件上传，例如因为任务本身无法运行，则这些日志文件将不存在。  

## <a name="next-steps"></a>后续步骤

检查您的应用程序是否实现了全面的错误检查;及时检测和诊断问题可能至关重要。
