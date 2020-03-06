---
title: 调试机器学习管道并对其进行故障排除
titleSuffix: Azure Machine Learning
description: 调试 Azure 机器学习 SDK for Python 中的机器学习管道并对其进行故障排除。 了解开发管道的常见缺陷，以及帮助你在远程执行前后调试脚本的技巧。 了解如何使用 Visual Studio Code 以交互方式调试机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: c81d4db5798c15327e06471f1cb0da4841bd61b2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396248"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>调试机器学习管道并对其进行故障排除
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)和[Azure 机器学习设计器（预览版）](https://docs.microsoft.com/azure/machine-learning/concept-designer)中对[计算机学习管道](concept-ml-pipelines.md)进行调试和故障排除。 提供了有关如何：

* 使用 Azure 机器学习 SDK 进行调试
* 使用 Azure 机器学习设计器进行调试
* 使用 Application Insights 调试
* 使用 Visual Studio Code （VS Code）和针对 Visual Studio 的 Python 工具交互调试（PTVSD）

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure 机器学习 SDK 中的调试和故障排除
以下部分概述了生成管道时的常见缺陷，以及用于调试在管道中运行的代码的不同策略。 如果在使管道按预期运行时遇到问题，请使用以下提示。

### <a name="testing-scripts-locally"></a>本地测试脚本

管道中最常见的失败之一是附加的脚本（数据清除脚本、计分脚本等）未按预期运行，或者包含在 Azure 计算机的工作区中难以调试的远程计算上下文中的运行时错误。学习工作室。 

管道本身不能在本地运行，但在本地计算机上运行隔离的脚本可以更快地进行调试，因为无需等待计算和环境生成过程。 执行此操作需要执行某些开发工作：

* 如果数据位于云数据存储中，则需要下载数据并使其可供脚本使用。 使用较小的数据示例，是一种更好的方法，可减少运行时并快速获取有关脚本行为的反馈
* 如果你正在尝试模拟中间管道步骤，你可能需要手动生成特定脚本从上一步开始的对象类型
* 还需要定义自己的环境，并复制远程计算环境中定义的依赖项

在您的本地环境中运行脚本安装程序后，执行以下调试任务会更容易：

* 附加自定义调试配置
* 暂停执行并检查对象状态
* 捕获在运行时之前不会公开的类型或逻辑错误

> [!TIP] 
> 验证脚本是否按预期运行后，下一步是在单步管道中运行该脚本，然后再尝试在包含多个步骤的管道中运行该脚本。

### <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

在开始生成管道之前，本地测试脚本是调试主要代码片段和复杂逻辑的好方法，但在某些时候，你可能需要在实际管道运行时调试脚本，尤其是在诊断发生的行为时。在管道步骤之间进行交互的过程中。 建议在步骤脚本中使用 `print()` 语句，以便在远程执行过程中可以看到对象状态和预期值，这与调试 JavaScript 代码的方式类似。

日志文件 `70_driver_log.txt` 包含： 

* 脚本执行过程中的所有打印语句
* 脚本的堆栈跟踪 

若要在门户中查找此日志文件和其他日志文件，请首先单击工作区中的管道运行。

![管道运行列表页](./media/how-to-debug-pipelines/pipelinerun-01.png)

导航到管道运行详细信息页。

![管道运行详细信息页](./media/how-to-debug-pipelines/pipelinerun-02.png)

单击特定步骤的模块。 导航到 "**日志**" 选项卡。其他日志包含有关环境映像生成过程和步骤准备脚本的信息。

![管道运行详细信息页日志选项卡](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> 可在工作区的 "**终结点**" 选项卡中找到*已发布管道*的运行。 可在**试验**或**管道**中找到*非发布管道*的运行。

### <a name="troubleshooting-tips"></a>故障排除提示

下表包含了管道开发期间的一些常见问题，其中包含可能的解决方案。

| Problem | 可能的解决方案 |
|--|--|
| 无法将数据传递到 `PipelineData` directory | 请确保已在脚本中创建了一个目录，该目录对应于管道期望步骤输出数据的位置。 在大多数情况下，输入参数将定义输出目录，然后显式创建目录。 使用 `os.makedirs(args.output_dir, exist_ok=True)` 创建输出目录。 有关显示此设计模式的评分脚本示例，请参阅[教程](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)。 |
| 依赖项 bug | 如果你在本地开发并测试了脚本，但在管道中的远程计算上运行时发现依赖项问题，请确保你的计算环境依赖项和版本与你的测试环境匹配。 |
| 计算目标存在不明确错误 | 删除并重新创建计算目标可以解决计算目标的某些问题。 |
| 管道未重复使用步骤 | 默认情况下，将启用步骤重用，但请确保未在管道步骤中禁用它。 如果重新使用处于禁用状态，则步骤中的 `allow_reuse` 参数将设置为 `False`。 |
| 不必要地重新运行管道 | 若要确保步骤仅在基础数据或脚本更改时重新运行，请为每个步骤分离你的目录。 如果对多个步骤使用相同的源目录，则可能会遇到不必要的重新运行。 使用管道步骤对象上的 `source_directory` 参数指向该步骤的隔离目录，并确保没有为多个步骤使用相同的 `source_directory` 路径。 |

### <a name="logging-options-and-behavior"></a>日志记录选项和行为

下表提供了针对管道的不同调试选项的信息。 这并不是一个详尽的列表，因为除了此处所示的 Azure 机器学习、Python 和 OpenCensus 以外，其他选项也存在。

| 库                    | 类型   | 示例                                                          | 目标                                  | 资源                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure 机器学习 SDK | 跃点数 | `run.log(name, val)`                                             | Azure 机器学习门户 UI             | [如何跟踪试验](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml。运行类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python 打印/日志记录    | 日志    | `print(val)`<br>`logging.info(message)`                          | 驱动程序日志，Azure 机器学习设计器 | [如何跟踪试验](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python 日志记录](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | 日志    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights 跟踪                | [在 Application Insights 中调试管道](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)（OpenCensus Azure Monitor 导出程序）<br>[Python 日志记录指南](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>日志记录选项示例

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>在 Azure 机器学习设计器中进行调试和故障排除（预览）

本部分概述了如何在设计器中对管道进行故障排除。
对于在设计器中创建的管道，可以在 "创作" 页或 "管道运行详细信息" 页中找到**日志文件**。

### <a name="access-logs-from-the-authoring-page"></a>从创作页访问日志

提交管道运行并停留在创作页面时，可以找到为每个模块生成的日志文件。

1. 在创作画布中选择任何模块。
1. 在 "属性" 窗格中，切换到 "**日志**" 选项卡。
1. 选择日志文件 `70_driver_log.txt`

    ![创作页模块日志](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>从管道运行访问日志

您还可以在 "管道 **" 或 "** **试验**" 部分的 "管道运行详细信息" 页中找到特定运行的日志文件。

1. 选择在设计器中创建的管道运行。
    ![管道运行页](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. 选择预览窗格中的任何模块。
1. 在 "属性" 窗格中，切换到 "**日志**" 选项卡。
1. 选择日志文件 `70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Application Insights 中的调试和故障排除
有关以这种方式使用 OpenCensus Python 库的详细信息，请参阅本指南：[在 Application Insights 中调试和解决机器学习管道问题](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Visual Studio Code 中的调试和故障排除

在某些情况下，可能需要以交互方式调试 ML 管道中使用的 Python 代码。 通过使用 Visual Studio Code （VS Code）和针对 Visual Studio 的 Python 工具（PTVSD），可以在代码在定型环境中运行时附加到代码。

### <a name="prerequisites"></a>先决条件

* 配置为使用__Azure 虚拟网络__的__Azure 机器学习工作区__。
* 一个__Azure 机器学习管道__，它使用 Python 脚本作为管道步骤的一部分。 例如，PythonScriptStep。
* Azure 机器学习计算群集，它位于__虚拟网络中__，__由管道用于定型__。
* __虚拟网络中__的__开发环境__。 开发环境可能是以下项之一：

    * 虚拟网络中的 Azure 虚拟机
    * 虚拟网络中笔记本 VM 的计算实例
    * 虚拟专用网络（VPN）连接到虚拟网络的客户端计算机。

有关将 Azure 虚拟网络与 Azure 机器学习配合使用的详细信息，请参阅[在 Azure 虚拟网络中保护 AZURE ML 试验和推理作业](how-to-enable-virtual-network.md)。

### <a name="how-it-works"></a>工作原理

ML 管道步骤运行 Python 脚本。 修改这些脚本以执行以下操作：
    
1. 记录正在其上运行的主机的 IP 地址。 使用 IP 地址将调试器连接到脚本。

2. 启动 PTVSD 调试组件，并等待调试程序连接。

3. 在您的开发环境中，您将监视定型过程创建的日志，以查找运行脚本的 IP 地址。

4. 使用 `launch.json` 文件告诉 VS Code 要将调试器连接到的 IP 地址。

5. 附加调试器并以交互方式执行该脚本。

### <a name="configure-python-scripts"></a>配置 Python 脚本

若要启用调试，请对 ML 管道中的步骤所使用的 Python 脚本进行以下更改：

1. 添加以下 import 语句：

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. 添加以下自变量。 这些自变量允许您根据需要启用调试器，并设置附加调试器的超时值：

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. 添加以下语句。 这些语句将加载当前的运行上下文，以便您可以记录运行代码的节点的 IP 地址：

    ```python
    global run
    run = Run.get_context()
    ```

1. 添加一个 `if` 语句，该语句启动 PTVSD 并等待调试器附加。 如果在超时之前未附加调试器，则脚本将继续正常运行。

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

以下 Python 示例显示了启用调试的基本 `train.py` 文件：

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>配置 ML 管道

若要提供启动 PTVSD 和获取运行上下文所需的 Python 包，请创建环境并设置 `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`。 更改 SDK 版本，使其与你正在使用的版本匹配。 下面的代码段演示如何创建环境：

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

在[配置 Python 脚本](#configure-python-scripts)部分，将两个新参数添加到 ML 管道步骤所使用的脚本中。 下面的代码段演示如何使用这些参数启用组件调试并设置超时。 它还演示了如何使用之前通过设置 `runconfig=run_config`创建的环境：

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

管道运行时，每个步骤创建一个子运行。 如果启用调试，则修改后的脚本将在子运行的 `70_driver_log.txt` 中记录类似于以下文本的信息：

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

保存 `ip_address` 值。 在下一部分中使用。

> [!TIP]
> 还可以从运行日志中查找此管道步骤的子运行日志的 IP 地址。 有关查看此信息的详细信息，请参阅[监视 AZURE ML 试验运行和指标](how-to-track-experiments.md)。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在 VS Code 开发环境中安装针对 Visual Studio 的 Python 工具（PTVSD），请使用以下命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    有关将 PTVSD 与 VS Code 一起使用的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要配置 VS Code 以便与运行调试器的 Azure 机器学习计算进行通信，请创建新的调试配置：

    1. 在 VS Code 中，选择 "__调试__" 菜单，然后选择 "__打开配置__"。 将打开一个名为 "__启动__" 的文件。

    1. 在__启动 json__文件中，找到包含 `"configurations": [`的行，然后在其后面插入以下文本。 将 `"host": "10.3.0.5"` 项更改为在上一节的日志中返回的 IP 地址。 将 `"localRoot": "${workspaceFolder}/code/step"` 项更改为包含要调试的脚本副本的本地目录：

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果 "配置" 部分中已有其他项，请在插入的代码后添加一个逗号（，）。

        > [!TIP]
        > 最佳做法是将脚本的资源保留在不同的目录中，这就是 `localRoot` 示例值引用 `/code/step1`的原因。
        >
        > 如果正在调试多个脚本，请在不同的目录中为每个脚本创建一个单独的配置节。

    1. 保存__启动__文件。

### <a name="connect-the-debugger"></a>连接调试器

1. 打开 VS Code 并打开脚本的本地副本。
2. 设置在附加后要使脚本停止的断点。
3. 当子进程正在运行脚本，并且 `Timeout for debug connection` 显示在日志中时，请使用 F5 键或选择 "__调试__"。 出现提示时，请选择 " __Azure 机器学习计算：远程调试__" 配置。 还可以从侧栏中选择 "调试" 图标，从 "调试" 下拉菜单中选择 " __Azure 机器学习：远程调试__"，然后使用绿色箭头附加调试器。

    此时，VS Code 连接到计算节点上的 PTVSD，并在之前设置的断点处停止。 现在可以单步执行代码，就像运行、查看变量等。

    > [!NOTE]
    > 如果日志中显示一条说明 `Debugger attached = False`的条目，则超时时间已到，并且该脚本在没有调试器的情况下继续。 再次提交管道，并在 `Timeout for debug connection` 消息后和超时过期之前连接调试器。

## <a name="next-steps"></a>后续步骤

* 有关[azureml 管道核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)包和[azureml 管道-步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)包的帮助，请参阅 SDK 参考。

* 请参阅[设计器异常和错误代码](algorithm-module-reference/designer-error-codes.md)的列表。
