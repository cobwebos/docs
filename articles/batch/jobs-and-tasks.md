---
title: Azure Batch 中的作业和任务
description: 从开发的角度来了解作业和任务及其在 Azure Batch 工作流中的运用。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 5120b76f34e81c2ceeba88767a656b5ee0d40c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85955363"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Azure Batch 中的作业和任务

在 Azure Batch 中，“任务”表示计算单元。 “作业”是这些任务的集合。 下面详细介绍了作业和任务及其在 Azure Batch 工作流中的运用。

## <a name="jobs"></a>作业

作业是任务的集合。 作业控制其任务对池中计算节点执行计算的方式。

作业指定要在其上运行工作的[池](nodes-and-pools.md#pools)。 可以为每个作业创建新池，或将池用于多个作业。 可以针对与作业计划关联的每个作业创建池，或者针对与作业计划关联的所有作业创建池。

### <a name="job-priority"></a>作业优先级

可以向创建的作业分配可选的作业优先级。 Batch 服务使用作业的优先级值来确定帐户中的作业计划顺序（不要与 [计划的作业](#scheduled-jobs)相混淆）。 优先级值的范围为 -1000 到 1000，-1000 表示最低优先级，1000 表示最高优先级。 若要更新作业的优先级，请调用[更新作业的属性](/rest/api/batchservice/job/update)操作 (Batch REST) 或修改 [CloudJob.Priority](/dotnet/api/microsoft.azure.batch.cloudjob) 属性 (Batch .NET)。

在同一个帐户内，高优先级作业的计划优先顺序高于低优先级作业。 一个帐户中具有较高优先级值的作业，其计划优先级并不高于不同帐户中较低优先级值的另一个作业。 已经运行的低优先级作业中的任务不会预先清空。

不同池的作业计划是独立的。 在不同的池之间，即使作业的优先级较高，如果其关联的池缺少空闲的节点，则不保证此作业优先计划。 在同一个池中，相同优先级的作业有相同的计划机会。

### <a name="job-constraints"></a>作业约束

可以使用作业约束为作业指定特定的限制：

- 可以设置 **最大挂钟时间**，以便在作业的运行时间超过指定的最大挂钟时间时，终止该作业及其所有关联的任务。
- 可以将任务重试最大次数指定为约束，包括指定是要始终重试还是永不重试某个任务。 重试任务意味着，如果任务失败，它将重新排队以再次运行。

### <a name="job-manager-tasks-and-automatic-termination"></a>作业管理器任务和自动终止

客户端应用程序可将任务添加到作业，用户也可以指定 [作业管理器任务](#job-manager-task)。 作业管理器任务包含必要的信息用于为池中某个计算节点上运行的包含作业管理器任务的作业创建所需的任务。 作业管理器任务专门由 Batch 来处理；创建作业和重新启动失败的作业后，会立即将任务排队。 [作业计划](#scheduled-jobs)创建的作业需要作业管理器任务，因为它是在实例化作业之前定义任务的唯一方式。

默认情况下，当作业内的所有任务都完成时，作业仍保持活动状态。 可以更改此行为，使作业在其中的所有任务完成时自动终止。 将作业的 onAllTasksComplete 属性（在 Batch .NET 中为 [OnAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob)）设置为 terminatejob，可在作业的所有任务处于已完成状态时自动终止该作业。

Batch 服务将没有任务的作业视为其所有任务都已完成。 因此，此选项往往与 [作业管理器任务](#job-manager-task)配合使用。 如果想要使用自动作业终止而不通过作业管理器终止，首先应该将新作业的 **onAllTasksComplete** 属性设置为 *noaction*，然后只有在完成将任务添加到作业之后才将它设置为 *terminatejob*。

### <a name="scheduled-jobs"></a>计划的作业

使用[作业计划](/rest/api/batchservice/jobschedule)可在 Batch 服务中创建周期性作业。 作业计划指定何时要运行作业，并包含要运行的作业的规范。 可以指定计划的持续时间（计划的持续时间和生效时间），以及在计划的时间段创建作业的频率。

## <a name="tasks"></a>任务

任务是与作业关联的计算单位。 它在节点上运行。 任务将分配到节点以执行，或排入队列直到节点空闲。 简而言之，任务将在计算节点上运行一个或多个程序或脚本，以执行你需要完成的工作。

创建任务时，可以指定：

- 任务的**命令行**。 这是可在计算节点上运行应用程序或脚本的命令行。

    请务必注意，命令行不是在 shell 下运行的。 因此无法以本机方式利用 shell 功能，例如[环境变量](#environment-settings-for-tasks)扩展（包括 `PATH`）。 若要利用此类功能，必须在命令行中调用 shell，例如，在 Windows 节点上启动 `cmd.exe`，或者在 Linux 上启动 `/bin/sh`：

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    如果任务需要运行不在节点的 `PATH` 中的应用程序或脚本，或在引用环境变量，请在任务命令行中显式调用 shell。
- **资源文件** 。 在执行任务的命令行之前，这些文件将自动从 Azure 存储帐户中的 Blob 存储复制到节点。 有关详细信息，请参阅[启动任务](#start-task)与[文件和目录](files-and-directories.md)。
- 应用程序所需的 **环境变量** 。 有关详细信息，请参阅[任务的环境设置](#environment-settings-for-tasks)。
- 执行任务所依据的 **约束** 。 例如，约束包括允许运行任务的最长时间、重试失败任务的次数上限，以及文件保留在任务工作目录中的最长时间。
- **Application packages** 。 [应用程序包](batch-application-packages.md) 提供任务运行的应用程序的简化部署和版本控制。 在共享池的环境中，任务级应用程序包特别有用：不同的作业在一个池上运行，完成某个作业时不删除该池。 如果作业中的任务少于池中的节点，任务应用程序包可以减少数据传输，因为应用程序只部署到运行任务的节点。
- Docker 中心的**容器映像**引用，或者专用注册表和其他设置，用于创建 Docker 容器，其中的任务运行在节点上。 如果池使用容器配置进行设置，则仅指定此信息。

> [!NOTE]
> 最长任务生存期（从添加到作业时算起到任务完成时结束）为 180 天。 已完成的任务保存 7 天；最长生存期内未完成的任务的数据不可访问。

除了可以定义在节点上运行计算的任务以外，Batch 服务还提供几种特殊任务：

- [启动任务](#start-task)
- [作业管理器任务](#job-manager-task)
- [作业准备和释放任务](#job-preparation-and-release-tasks)
- [多实例任务](#multi-instance-task)
- [任务依赖项](#task-dependencies)

### <a name="start-task"></a>启动任务

通过将启动任务与池相关联，可以准备池节点的操作环境。 可以执行各种操作，例如，安装任务所要运行的应用程序或启动后台进程。 启动任务在节点每次启动时运行，且只要保留在池中就会持续运行。 这包括首次将节点添加到池时，以及重新启动节点或重置节点映像时。

启动任务的主要优点是可以包含全部所需的信息，使你能够配置计算节点，以及安装执行任务所需的应用程序。 因此，增加池中的节点数量与指定新的目标节点计数一样简单。 启动任务为 Batch 服务提供所需的信息以配置新节点并使其准备好接受任务。

与任何 Azure Batch 任务一样，除了指定要执行的命令行以外，还可以指定 [Azure 存储](../storage/index.yml)中的资源文件列表。 Batch 服务先将资源文件从 Azure 存储复制到节点，然后运行命令行。 对于池启动任务，文件列表通常包含任务应用程序及其依赖项。

但是，启动任务还可能包含计算节点上运行的所有任务使用的引用数据。 例如，启动任务的命令行可执行 `robocopy` 操作，将应用程序文件（已指定为资源文件并下载到节点）从启动任务的[工作目录](files-and-directories.md)复制到共享文件夹，然后运行 MSI 或 `setup.exe`。

通常，Batch 服务需要等待启动任务完成，然后认为节点已准备好分配任务，但你可以配置这种行为。

如果某个计算节点上的启动任务失败，则节点的状态将会更新以反映失败状态，同时，不会为该节点分配任何任务。 如果从存储中复制启动任务的资源文件时出现问题，或由其命令行执行的进程返回了非零退出代码，则启动任务可能会失败。

如果添加或更新现有池的启动任务，必须重启其计算节点，启动任务才应用到节点。

>[!NOTE]
> Batch 限制启动任务的总大小，其中包括资源文件和环境变量。 如需缩小启动任务，可使用下述两种方法中的一种：
>
> 1. 可以使用应用程序包，将应用程序或数据分发到 Batch 池中的每个节点。 有关应用程序包的详细信息，请参阅[使用 Batch 应用程序包将应用程序部署到计算节点](batch-application-packages.md)。
> 2. 可以手动创建压缩的存档，其中包含应用程序文件。 将压缩的存档作为 Blob 上传到 Azure 存储。 将压缩的存档指定为启动任务的资源文件。 为启动任务运行命令行之前，请在命令行中将存档解压缩。 
>
>    若要解压缩存档，可以使用所选归档工具。 需包括相关工具，以便为启动任务解压缩资源文件形式的存档。

### <a name="job-manager-task"></a>作业管理器任务

通常使用作业管理器任务来控制和/或监视作业的执行。 例如，作业管理器任务经常用于创建和提交作业的任务、确定其他要运行的任务，以及确定任务何时完成。

但是，作业管理器任务并不限定于这些活动。 它是功能齐备的任务，可执行作业所需的任何操作。 例如，作业管理器任务可以下载指定为参数的文件、分析该文件的内容，并根据这些内容提交其他任务。

作业管理员任务在所有其他任务之前启动。 它提供以下功能：

- 创建作业时由 Batch 服务自动提交为任务。
- 安排在作业中的其他任务之前执行。
- 缩小池时，关联的节点最后才从池中删除。
- 此终止可能完全取决于作业中的所有任务终止。
- 需要重新启动时，作业管理器任务有最高的优先级。 如果找不到空闲的节点，Batch 服务可以终止池中正在运行的其他某个任务，以便腾出空间供作业管理器任务运行。
- 一个作业中的作业管理器任务的优先级不高于其他作业的任务。 不同作业之间只遵循作业级别的优先级。

### <a name="job-preparation-and-release-tasks"></a>作业准备和释放任务

Batch 提供作业准备任务用于前期作业执行设置，还提供作业释放任务用于后期作业维护或清除。

在任何其他作业任务执行之前，作业准备任务在计划要运行任务的所有计算节点上运行。 例如，可以使用作业准备任务复制所有任务共享的、但对作业唯一的数据。

作业完成后，作业释放任务将在池中至少运行了一个任务的每个节点上运行。 例如，作业释放任务可以删除作业准备任务所复制的数据，也可以压缩并上传诊断日志数据。

作业准备和释放任务允许指定调用任务时要运行的命令行。 这些任务提供许多功能，例如文件下载、以提升权限方式执行、自定义环境变量、最大执行持续时间、重试计数和文件保留时间。

有关作业准备和释放任务的详细信息，请参阅 [在 Azure Batch 计算节点上运行作业准备和完成任务](batch-job-prep-release.md)。

### <a name="multi-instance-task"></a>多实例任务

[多实例任务](batch-mpi.md) 是经过配置后可以在多个计算节点上同时运行的任务。 通过多实例任务，可以启用高性能计算方案，此类方案需要将一组计算节点分配到一起来处理单个工作负荷，例如消息传递接口 (MPI)。

有关在 Batch 中使用 Batch .NET 库运行 MPI 作业的详细介绍，请参阅 [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md)（在 Azure Batch 中使用多实例任务来执行消息传递接口 (MPI) 应用程序）。

### <a name="task-dependencies"></a>任务依赖项

顾名思义，使用[任务依赖项](batch-task-dependencies.md)可以在执行某个任务之前，指定该任务与其他任务的依赖性。 此功能提供以下情况的支持：“下游”任务取用“上游”任务的输出，或当上游任务执行下游任务所需的某种初始化时。

若要使用此功能，必须先在 Batch 作业上[启用任务依赖性](batch-task-dependencies.md#enable-task-dependencies
)。 然后，针对每个依赖于另一个任务（或其他许多任务）的任务，指定该任务依赖的任务。

使用任务依赖性，可以配置如下所述的方案：

- *taskB* 依赖于 *taskA*（直到 *taskA* 完成，才开始执行 *taskB*）。
- *taskC* 同时依赖于 *taskA* 和 *taskB*。
- *taskD* 在执行前依赖于某个范围的任务，例如任务 *1* 到 *10*。

有关更多详细信息，请参阅 [Azure Batch 中的任务依赖关系](batch-task-dependencies.md)和 [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) GitHub 存储库中的 [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) 代码示例。

### <a name="environment-settings-for-tasks"></a>任务的环境设置

批处理服务执行的每个任务都可以访问在计算节点上设置的环境变量。 这包括 Batch 服务（[服务定义型](./batch-compute-node-environment-variables.md)）定义的环境变量，以及用户可以针对其任务定义的自定义环境变量。 任务执行的应用程序和脚本可以在执行期间访问这些环境变量。

可以通过填充这些实体的 *环境设置* 属性，在任务或作业级别设置自定义环境变量。 有关更多详细信息，请参阅[将任务添加到作业](/rest/api/batchservice/task/add?)] 操作 (Batch REST API)，或 Batch .NET 中的 [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) 和 [CloudJob.CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) 属性。

客户端应用程序或服务可使用[获取有关任务的信息](/rest/api/batchservice/task/get)操作 (Batch REST) 或通过访问 [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) 属性 (Batch .NET)，来获取任务的环境变量（服务定义型和自定义环境变量）。 在计算节点上执行的进程可以在节点上访问这些和其他环境变量，例如，通过使用熟悉的 `%VARIABLE_NAME%` (Windows) 或 `$VARIABLE_NAME` (Linux) 语法。

可以在[计算节点环境变量](batch-compute-node-environment-variables.md)中找到包含所有服务定义型环境变量的完整列表。

## <a name="next-steps"></a>后续步骤

- 了解[文件和目录](files-and-directories.md)。
