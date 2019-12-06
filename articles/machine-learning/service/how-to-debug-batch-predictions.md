---
title: 调试和故障排除 ParallelRunStep
titleSuffix: Azure Machine Learning
description: 调试 Azure 机器学习 SDK for Python 中的机器学习管道并对其进行故障排除。 了解有关通过管道进行开发的常见缺陷，以及帮助你在远程执行前后调试脚本的技巧。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: db17dca1a859807f4d92b4953ec0e2785a3a4160
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852323"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>使用 ParallelRun 进行调试和故障排除
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何从[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)调试[ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py)类并对其进行故障排除。

## <a name="testing-scripts-locally"></a>本地测试脚本

有关机器学习管道，请参阅[本地测试脚本部分](how-to-debug-pipelines.md#testing-scripts-locally)。 你的 ParallelRunStep 作为 ML 管道中的一个步骤运行，因此相同的答案同样适用于这两种情况。

## <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

从本地调试评分脚本到在实际管道中调试评分脚本这一转换可能是一项困难的。 若要了解如何在门户中查找日志，请查看[从远程上下文调试脚本中的机器学习管道部分](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)。 此部分中的信息也适用于批处理推理运行。

例如，日志文件 `70_driver_log.txt` 还包含： 

* 脚本执行过程中的所有打印语句。
* 脚本的堆栈跟踪。

由于批处理推理作业的分布式特性，有多个不同源的日志。 但是，会创建两个合并文件来提供高级信息：

- `~/logs/overview.txt`：此文件提供了到目前为止创建的最小批处理数（也称为任务数）以及到目前为止已处理的小批处理数的高级信息。 此时，它会显示作业的结果。 如果作业失败，它将显示错误消息以及从何处开始进行故障排除。

- `~/logs/master.txt`：此文件提供正在运行的作业的主节点（也称为协调器）视图。 包含任务创建、进度监视和运行结果。

当你需要全面了解每个节点执行评分脚本的方式时，请查看每个节点的单独进程日志。 可在 `worker` 文件夹中找到进程日志，按辅助角色节点分组：

- `~/logs/worker/<ip_address>/Process-*.txt`：此文件提供了有关每个小型批处理的详细信息，因为它是由辅助角色选取或完成的。 对于每个小型批处理，此文件包括：

    - 工作进程的 IP 地址和 PID。 
    - 项总数和成功处理的项的数目。 
    - 时钟时间中的开始和结束时间（`start1` 和 `end1`）。 
    - 占用的处理器时间的开始时间和结束时间（`start2` 和 `end2`）。 

你还可以找到有关每个工作进程的进程的资源使用情况的信息。 此信息采用 CSV 格式，位于 `~/logs/performance/<ip_address>/`。 例如，检查资源利用率时，查看以下文件：

- `process_resource_monitor_<ip>_<pid>.csv`：每个工作进程的资源使用情况。 
- `sys_resource_monitor_<ip>.csv`：每个节点日志。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>从远程上下文中的用户脚本如何实现日志？
你可以使用以下步骤设置记录器，以使日志显示在门户的 "**日志/用户**" 文件夹中：
1. 将下面的第一个代码部分保存到文件 entry_script_helper py，并将该文件放在与条目脚本相同的文件夹中。 此类获取 AmlCompute 内的路径。 对于本地测试，可以更改 `get_working_dir()` 以返回本地文件夹。
2. 配置 `init()` 方法中的记录器，然后使用它。 下面的第二个代码部分是一个示例。 

**entry_script_helper py：**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**使用记录器的示例条目脚本：**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>后续步骤

* 有关 ParallelRunStep 类的[contrib](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py)的帮助，请参阅 SDK 参考，并提供类的[文档](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)。

* 按照[高级教程](tutorial-pipeline-batch-scoring-classification.md)，了解如何使用管道进行批处理评分。
