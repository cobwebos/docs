---
title: 对 ParallelRunStep 进行调试和故障排除
titleSuffix: Azure Machine Learning
description: 在适用于 Python 的 Azure 机器学习 SDK 中的机器学习管道对 ParallelRunStep 进行调试和故障排除。 了解通过管道进行开发的常见缺陷，以及有助于在远程执行之前和期间调试脚本的提示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
ms.openlocfilehash: a6a3e9a7a914711f6b7c923ac2249ebf3285c877
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031008"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>对 ParallelRunStep 进行调试和故障排除
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 中对 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 类进行调试和故障排除。

## <a name="testing-scripts-locally"></a>在本地测试脚本

有关机器学习管道的信息，请参阅[在本地测试脚本部分](how-to-debug-pipelines.md#testing-scripts-locally)。 ParallelRunStep 作为 ML 管道中的一个步骤运行，因此相同的答案对两种情况均适用。

## <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

要实现从在本地调试评分脚本到在实际管道中调试评分脚本这一飞跃可能很困难。 有关如何在门户中查找日志的信息，请参阅[有关从远程上下文调试脚本的机器学习管道部分](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)。 该部分中的信息也适用于 ParallelRunStep。

例如，日志文件 `70_driver_log.txt` 包含来自启动 ParallelRunStep 代码的控制器的信息。

由于 ParallelRunStep 作业具有分布式特性，因此存在来自多个不同源的日志。 但是，会创建两个合并文件来提供高级信息：

- `~/logs/overview.txt`：此文件提供了有关到目前为止已创建的微型批处理数（也称为任务数）以及已处理的微型批处理数的高级信息。 最后，它会显示作业的结果。 如果作业失败，它将显示错误消息以及开始进行故障排除的位置。

- `~/logs/sys/master.txt`：此文件提供正在运行的作业的主体节点（也称为协调器）视图。 包括任务创建、进度监视和运行结果。

使用 EntryScript 帮助程序和 print 语句，通过入口脚本生成的日志将显示在以下文件中：

- `~/logs/user/<ip_address>/<node_name>.log.txt`：这些文件是使用 EntryScript helper 从 entry_script 写入的日志。 还包含来自 entry_script 的 print 语句 (stdout)。

要简要了解脚本中的错误，请参阅以下文件：

- `~/logs/user/error.txt`：此文件将尝试汇总脚本中的错误。

有关脚本中错误的详细信息，请参阅以下文件：

- `~/logs/user/error/`：包含引发的所有错误和按节点组织的完整堆栈跟踪。

如需全面了解每个节点如何执行评分脚本，请查看每个节点单独的进程日志。 进程日志位于 `sys/node` 文件夹中，按工作器节点分组：

- `~/logs/sys/node/<node_name>.txt`：此文件提供了有关每个小型批处理的详细信息，因为它是由辅助角色选取或完成的。 对于每个微型批处理，此文件包括以下内容：

    - 工作进程的 IP 地址和 PID。 
    - 总项数、成功处理的项计数和失败的项计数。
    - 开始时间、持续时间、处理时间和运行方法时间。

此外，还可以找到有关每个工作进程的资源使用情况的信息。 此信息采用 CSV 格式，并且位于 `~/logs/sys/perf/overview.csv` 中。 下提供了有关每个流程的信息 `~logs/sys/processes.csv` 。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何从远程上下文中的用户脚本记录？
ParallelRunStep 可以基于 process_count_per_node 在一个节点上运行多个进程。 为了组织节点上每个进程的日志并组合 print 和 log 语句，我们建议使用 ParallelRunStep 记录器，如下所示。 从 EntryScript 获取记录器，并使日志显示在门户的 "**日志/用户**" 文件夹中。

使用记录器的示例入口脚本：
```python
from azureml_user.parallel_run import EntryScript

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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何将端输入（如包含查找表的单个或多个文件）传递到所有工作器？

用户可以使用 ParalleRunStep 的 side_inputs 参数将引用数据传递给脚本。 作为 side_inputs 提供的所有数据集将装载到每个辅助角色节点上。 用户可以通过传递参数获取装入的位置。

构造包含引用数据的[数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)，并将其注册到你的工作区。 将其传递到 `ParallelRunStep` 的 `side_inputs` 参数。 此外，还可以在节中添加其路径 `arguments` ，以便轻松访问其装入的路径：

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

之后，可以在推理脚本中访问它（例如在 init() 方法中），如下所示：

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="next-steps"></a>后续步骤

* 有关[azureml-管线-步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)包的帮助，请参阅 SDK 参考。 查看 ParallelRunStep 类的参考[文档](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py)。

* 按照[高级教程](tutorial-pipeline-batch-scoring-classification.md)，使用 ParallelRunStep 的管道。 本教程介绍如何将另一个文件作为侧输入传递。 
