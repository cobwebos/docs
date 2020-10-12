---
title: 对 ParallelRunStep 进行调试和故障排除
titleSuffix: Azure Machine Learning
description: 在适用于 Python 的 Azure 机器学习 SDK 中的机器学习管道对 ParallelRunStep 进行调试和故障排除。 了解通过管道进行开发的常见缺陷，以及有助于在远程执行之前和期间调试脚本的提示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 7866f2dcaebe396759eb7f6315c457bfce307723
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315569"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>对 ParallelRunStep 进行调试和故障排除


本文介绍如何在 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 中对 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py&preserve-view=true) 类进行调试和故障排除。

## <a name="testing-scripts-locally"></a>在本地测试脚本

有关机器学习管道的信息，请参阅[在本地测试脚本部分](how-to-debug-pipelines.md#debug-scripts-locally)。 ParallelRunStep 作为 ML 管道中的一个步骤运行，因此相同的答案对两种情况均适用。

## <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

要实现从在本地调试评分脚本到在实际管道中调试评分脚本这一飞跃可能很困难。 有关如何在门户中查找日志的信息，请参阅[有关从远程上下文调试脚本的机器学习管道部分](how-to-debug-pipelines.md)。 该部分中的信息也适用于 ParallelRunStep。

例如，日志文件 `70_driver_log.txt` 包含来自启动 ParallelRunStep 代码的控制器的信息。

由于 ParallelRunStep 作业具有分布式特性，因此存在来自多个不同源的日志。 但是，会创建两个合并文件来提供高级信息：

- `~/logs/job_progress_overview.txt`：此文件提供了有关到目前为止已创建的微型批处理数（也称为任务数）以及已处理的微型批处理数的高级信息。 最后，它会显示作业的结果。 如果作业失败，它将显示错误消息以及开始进行故障排除的位置。

- `~/logs/sys/master_role.txt`：此文件提供运行中作业的主节点（也称为业务流程协调程序）视图。 包括任务创建、进度监视和运行结果。

使用 EntryScript 帮助程序和 print 语句，通过入口脚本生成的日志将显示在以下文件中：

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`：这些文件是使用 EntryScript 帮助程序从 entry_script 写入的日志。

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`：这些文件是来自 stdout (的日志，例如 print 语句) 的 entry_script。

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`：这些文件是来自 entry_script 的 stderr 的日志。

要简要了解脚本中的错误，请参阅以下文件：

- `~/logs/user/error.txt`：此文件将尝试汇总脚本中的错误。

有关脚本中错误的详细信息，请参阅以下文件：

- `~/logs/user/error/`：包含加载和运行条目脚本时引发的异常的完整堆栈跟踪。

如需全面了解每个节点如何执行评分脚本，请查看每个节点单独的进程日志。 进程日志位于 `sys/node` 文件夹中，按工作器节点分组：

- `~/logs/sys/node/<ip_address>/<process_name>.txt`：此文件提供有关每个微型批处理在工作器拾取或完成它时的详细信息。 对于每个微型批处理，此文件包括以下内容：

    - 工作进程的 IP 地址和 PID。 
    - 总项数、成功处理的项计数和失败的项计数。
    - 开始时间、持续时间、处理时间和运行方法时间。

此外，还可以找到有关每个工作进程的资源使用情况的信息。 此信息采用 CSV 格式，并且位于 `~/logs/sys/perf/<ip_address>/node_resource_usage.csv` 中。 有关每个进程的信息可在 `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` 下找到。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何从远程上下文中的用户脚本记录？
ParallelRunStep 可以基于 process_count_per_node 在一个节点上运行多个进程。 为了组织节点上每个进程的日志并结合 print 和 log 语句，我们建议使用 ParallelRunStep 记录器，如下所示。 从 EntryScript 获取记录器，使日志显示在门户的 logs/user 文件夹中。

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

用户可以使用 ParalleRunStep 的 side_inputs 参数将引用数据传递给脚本。 作为 side_inputs 提供的所有数据集将装载到每个工作器节点上。 用户可以通过传递参数获取装载的位置。

构造包含引用数据的[数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true)，并将其注册到工作区。 将其传递到 `ParallelRunStep` 的 `side_inputs` 参数。 此外，还可以在 `arguments` 节中添加其路径，以便轻松访问其已装载的路径：

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

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>如何使用服务主体身份验证的输入数据集？

用户可以使用工作区中使用的服务主体身份验证传递输入数据集。 若要在 ParallelRunStep 中使用此类数据集，需要注册该数据集以构造 ParallelRunStep 配置。

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>后续步骤

* 请查看 SDK 参考，获取有关 [azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) 包的帮助。 查看 ParallelRunStep 类的参考[文档](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py&preserve-view=true)。

* 按照[高级教程](tutorial-pipeline-batch-scoring-classification.md)操作，将管道与 ParallelRunStep 配合使用。 本教程演示如何将另一个文件作为旁路输入。 
