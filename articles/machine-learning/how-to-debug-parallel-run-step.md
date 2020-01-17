---
title: 调试和故障排除 ParallelRunStep
titleSuffix: Azure Machine Learning
description: 用于 Python 的 Azure 机器学习 SDK 中的机器学习管道中的 ParallelRunStep 调试和故障排除。 了解有关通过管道进行开发的常见缺陷，以及帮助你在远程执行前后调试脚本的技巧。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122959"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>调试和故障排除 ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何从[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)调试[ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py)类并对其进行故障排除。

## <a name="testing-scripts-locally"></a>本地测试脚本

有关机器学习管道，请参阅[本地测试脚本部分](how-to-debug-pipelines.md#testing-scripts-locally)。 你的 ParallelRunStep 作为 ML 管道中的一个步骤运行，因此相同的答案同样适用于这两种情况。

## <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

从本地调试评分脚本到在实际管道中调试评分脚本这一转换可能是一项困难的。 若要了解如何在门户中查找日志，请查看[从远程上下文调试脚本中的机器学习管道部分](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)。 此部分中的信息也适用于并行步骤运行。

例如，日志文件 `70_driver_log.txt` 包含用于启动并行运行步骤代码的控制器的信息。

由于并行运行作业的分布式特性，有多个不同源的日志。 但是，会创建两个合并文件来提供高级信息：

- `~/logs/overview.txt`：此文件提供了到目前为止创建的最小批处理数（也称为任务数）以及到目前为止已处理的小批处理数的高级信息。 此时，它会显示作业的结果。 如果作业失败，它将显示错误消息以及从何处开始进行故障排除。

- `~/logs/sys/master.txt`：此文件提供正在运行的作业的主节点（也称为协调器）视图。 包含任务创建、进度监视和运行结果。

使用 EntryScript 和 print 语句从条目脚本生成的日志将在以下文件中找到：

- `~/logs/user/<ip_address>/Process-*.txt`：此文件包含使用 EntryScript 从 entry_script 中写入的日志。 它还包含来自 entry_script 的打印语句（stdout）。

当你需要全面了解每个节点执行评分脚本的方式时，请查看每个节点的单独进程日志。 可在 `sys/worker` 文件夹中找到进程日志，按辅助角色节点分组：

- `~/logs/sys/worker/<ip_address>/Process-*.txt`：此文件提供了有关每个小型批处理的详细信息，因为它是由辅助角色选取或完成的。 对于每个小型批处理，此文件包括：

    - 工作进程的 IP 地址和 PID。 
    - 项的总数、成功处理的项计数和失败的项计数。
    - 开始时间、持续时间、处理时间和运行方法时间。

你还可以找到有关每个工作进程的进程的资源使用情况的信息。 此信息采用 CSV 格式，位于 `~/logs/sys/perf/<ip_address>/`。 对于单个节点，作业文件将在 "`~logs/sys/perf`" 下提供。 例如，检查资源利用率时，查看以下文件：

- `Process-*.csv`：每个工作进程的资源使用情况。 
- `sys.csv`：每个节点日志。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>从远程上下文中的用户脚本如何实现日志？
如下面的示例代码所示，可以从 EntryScript 获取记录器，以使日志显示在门户的 "**日志/用户**" 文件夹中。

**使用记录器的示例条目脚本：**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何将一方输入（如文件或包含查找表的文件）传递到所有工作线程？

构造包含端输入的[数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)对象，并将其注册到你的工作区。 之后，你可以在推断脚本中访问它（例如，在 init （）方法中），如下所示：

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>后续步骤

* 有关 ParallelRunStep 类的[contrib](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py)的帮助，请参阅 SDK 参考，并提供类的[文档](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)。

* 按照[高级教程](tutorial-pipeline-batch-scoring-classification.md)，通过并行运行步骤使用管道。
