---
title: 对 ParallelRunStep 进行调试和故障排除
titleSuffix: Azure Machine Learning
description: 在适用于 Python 的 Azure 机器学习 SDK 中对机器学习管道中的 ParallelRunStep 进行调试和故障排除。 了解通过管道进行开发的常见缺陷，以及有助于在远程执行之前和期间调试你的脚本的技巧。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122959"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>对 ParallelRunStep 进行调试和故障排除
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍了如何通过 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)对 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 类进行调试和故障排除。

## <a name="testing-scripts-locally"></a>在本地测试脚本

有关机器学习管道，请参阅[“在本地测试脚本”部分](how-to-debug-pipelines.md#testing-scripts-locally)。 ParallelRunStep 作为机器学习管道中的一个步骤运行，因此，同一答案同时适用于两者。

## <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

从在本地调试评分脚本转变为在实际管道中调试评分脚本可能是一个困难的飞跃。 若要了解如何在门户中查找日志，请参阅[有关从远程上下文调试脚本的机器学习管道部分](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)。 该部分中的信息也适用于并行步骤运行。

例如，日志文件 `70_driver_log.txt` 包含用于启动并行运行步骤代码的控制器中的信息。

由于并行运行作业是分布式的，因此会存在来自多个不同来源的日志。 但是，会创建两个合并的文件来提供概要信息：

- `~/logs/overview.txt`：此文件提供目前为止已创建的微型批处理（也称为任务）数目和目前为止已处理的微型批处理数目的概要信息。 在该文件的末尾，它显示作业的结果。 如果作业失败，它将显示错误消息以及从何处开始排除故障。

- `~/logs/sys/master.txt`：此文件提供运行中作业的主节点（也称为业务流程协调程序）视图。 包括任务创建、进度监视和运行结果。

使用 EntryScript.logger 和 print 语句从条目脚本生成的日志可以在以下文件中找到：

- `~/logs/user/<ip_address>/Process-*.txt`：此文件包含使用 EntryScript.logger 从 entry_script 写入的日志。 它还包含来自 entry_script 的 print 语句 (stdout)。

当需要全面了解每个节点如何执行了评分脚本时，请查看每个节点各自的进程日志。 可在 `sys/worker` 文件夹中找到进程日志，它们按工作器节点分组：

- `~/logs/sys/worker/<ip_address>/Process-*.txt`：此文件提供有关每个微型批处理在工作器拾取或完成它时的详细信息。 对于每个微型批处理，此文件包括：

    - 工作进程的 IP 地址和 PID。 
    - 总项数、成功处理的项计数和失败的项计数。
    - 开始时间、持续时间、处理时间和运行方法时间。

你还可以找到每个工作器进程的资源使用情况的相关信息。 此信息采用 CSV 格式，位于 `~/logs/sys/perf/<ip_address>/`。 对于单个节点，作业文件将在 `~logs/sys/perf` 下提供。 例如，检查资源利用率时，请查看以下文件：

- `Process-*.csv`：每个工作进程的资源使用情况。 
- `sys.csv`：每个节点的日志。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何从远程上下文通过我的用户脚本记录日志？
如下面的示例代码所示，可以从 EntryScript 获取记录器，使日志显示在门户的 **logs/user** 文件夹中。

**使用了记录器的一个示例入口脚本：**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何将旁路输入（例如包含查找表的一个或多个文件）传递到我的所有辅助角色？

构造一个包含旁路输入的[数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)对象，并将其注册到工作区。 然后，可以在推断脚本（例如，init() 方法）中访问它，如下所示：

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>后续步骤

* 参阅 SDK 参考来获得 [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) 包的帮助信息，参阅此[文档](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)来了解 ParallelRunStep 类。

* 按照[高级教程](tutorial-pipeline-batch-scoring-classification.md)操作，将管道与并行运行步骤配合使用。
