---
title: 提交大量任务
description: 如何有效地将巨量的任务提交到单个 Azure Batch 作业中
ms.topic: how-to
ms.date: 08/24/2018
ms.openlocfilehash: 46ab5e8879167a1808c51d4c4cd5c7071cb67cff
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778955"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>将大量的任务提交到 Batch 作业

运行大规模的 Azure Batch 工作负荷时，你可能需要将数万、数十万甚至更多的任务提交到单个作业。 

本文提供有关如何以大幅提升的吞吐量将大量任务提交到单个 Batch 作业的指导，并提供了一些代码示例。 提交任务后，它们将会进入 Batch 队列，等待在为作业指定的池中进行处理。

## <a name="use-task-collections"></a>使用任务集合

Batch API 提供所需的方法用于高效地将任务作为集合添加到作业，或者每次添加一个任务。 添加大量的任务时，应使用适当的方法或重载，以集合的形式添加任务。 一般情况下，任务集合的构造方式是在循环访问一组输入文件或作业的参数时定义任务。

可在单个调用中添加的任务集合的最大大小取决于所用的 Batch API：

* 以下 Batch API 将集合限制为 **100 个任务**。 根据任务的大小，此限制可能更小 - 例如，如果任务包含大量的资源文件或环境变量。

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  使用这些 API 时，需要提供逻辑来分割任务数目以符合集合限制，以及在添加任务失败时处理错误并重试。 如果任务集合太大，以致无法添加，则请求会生成错误，并在减少任务后重试。

* 以下 API 支持的任务集合要大得多 - 仅受提交方客户端上的 RAM 可用性的限制。 这些 API 以透明方式将任务集合分割为较低级别 API 的“区块”，并在添加任务失败时重试。

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * 包含 Batch CLI 模板的 [Azure Batch CLI 扩展](batch-cli-templates.md)
    * [Python SDK 扩展](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>增大用于提交任务的吞吐量

将大型任务集合提交到作业可能需要一段时间 - 例如，通过 .NET API 添加 20,000 个任务最长需要 1 分钟时间。 根据具体的 Batch API 和工作负荷，可以通过修改以下一项或多项来提高任务吞吐量：

* **任务大小** - 添加大型任务所需的时间比添加小型任务更长。 若要减小集合中每个任务的大小，可以简化任务命令行、减少环境变量的数目，或者更有效地处理任务执行要求。 例如，不要使用大量的资源文件，而是使用池中的[启动任务](jobs-and-tasks.md#start-task)来安装任务依赖项，或使用[应用程序包](batch-application-packages.md)或 [Docker 容器](batch-docker-container-workloads.md)。

* **并行操作数目** - 根据具体的 Batch API，通过增加 Batch 客户端的最大并发操作数目来提高吞吐量。 在 .NET API 中使用 [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 属性，或者在 Batch Python SDK 扩展中使用 [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) 等方法的 `threads` 参数来配置此项设置。 （此属性在本机 Batch Python SDK 中不可用。）此属性默认设置为 1，但将其设置为更大的值可提高操作吞吐量。 提高吞吐量的代价是会消耗网络带宽，并在一定程度上降低 CPU 的性能。 最高可按 `MaxDegreeOfParallelism` 或 `threads` 的 100 倍提高任务吞吐量。 在实践中，应将并发操作数目设置为 100 以下。 
 
  包含 Batch 模板的 Azure Batch CLI 扩展会根据可用核心数自动增加并发操作数目，但无法在 CLI 中配置此属性。 

* **HTTP 连接限制** - 当 Batch 客户端添加大量的任务时，并发 HTTP 连接数可能会限制该客户端的性能。 可以使用某些 API 限制 HTTP 连接数。 例如，使用 .NET API 进行开发时，[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 属性默认设置为 2。 我们建议将该值增大到接近或大于并行操作数目。

## <a name="example-batch-net"></a>示例：批处理 .NET

以下 C# 代码片段演示了在使用 Batch .NET API 添加大量任务时要配置的设置。

若要提高任务吞吐量，请增大 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet) 的 [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 属性值。 例如：

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
使用 [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) 或 [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) 方法的相应重载将任务集合添加到作业。 例如：

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>示例：Batch CLI 扩展

使用包含 [Batch CLI 模板](batch-cli-templates.md)的 Azure Batch CLI 扩展，创建包含[任务工厂](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)的作业模板 JSON 文件。 任务工厂为单个任务定义中的某个作业配置相关任务的集合。  

下面是包含大量任务（在本例中为 250,000 个）的一维参数扫描作业的示例作业模板。 任务命令行是一个简单的 `echo` 命令。

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
若要使用模板运行作业，请参阅[使用 Azure Batch CLI 模板和文件传输](batch-cli-templates.md)。

## <a name="example-batch-python-sdk-extension"></a>示例：Batch Python SDK 扩展

若要使用 Azure Batch Python SDK 扩展，请先安装 Python SDK 和扩展：

```
pip install azure-batch
pip install azure-batch-extensions
```

设置使用该 SDK 扩展的 `BatchExtensionsClient`：

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

创建要添加到作业的任务集合。 例如：


```python
tasks = list()
# Populate the list with your tasks
...
```

使用 [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) 添加任务集合。 设置 `threads` 参数以增加并发操作数目：

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch Python SDK 扩展还支持使用任务工厂的 JSON 规范将任务参数添加到作业。 例如，为前面 Batch CLI 模板示例中所示的参数扫描配置作业参数：

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

将作业参数添加到作业。 设置 `threads` 参数以增加并发操作数目：

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>后续步骤

* 详细了解如何使用包含 [Batch CLI 模板](batch-cli-templates.md)的 Azure Batch CLI 扩展。
* 详细了解 [Batch Python SDK 扩展](https://pypi.org/project/azure-batch-extensions/)。
