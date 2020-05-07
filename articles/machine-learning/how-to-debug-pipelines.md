---
title: 调试 & ML 管道疑难解答
titleSuffix: Azure Machine Learning
description: 在 Python 中调试 Azure 机器学习管道。 了解开发管道时的常见陷阱，以及有助于在远程执行之前和期间调试脚本的提示。 了解如何使用 Visual Studio Code 以交互方式调试机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 4f0eb6aa92dd8999baed6868a159c86d5e7bd0c8
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594606"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>对机器学习管道进行调试和故障排除
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将在 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 和 [Azure 机器学习设计器（预览版）](https://docs.microsoft.com/azure/machine-learning/concept-designer)中了解如何对[机器学习管道](concept-ml-pipelines.md)进行调试和故障排除。 提供了有关如何：

* 使用 Azure 机器学习 SDK 进行调试
* 使用 Azure 机器学习设计器进行调试
* 使用 Application Insights 调试
* 使用 Visual Studio Code （VS Code）和针对 Visual Studio 的 Python 工具交互调试（PTVSD）

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure 机器学习 SDK 中的调试和故障排除
以下部分概述了生成管道时的常见陷阱，以及用于调试管道中运行的代码的不同策略。 如果在使管道按预期运行时遇到问题，请参考以下提示。

### <a name="testing-scripts-locally"></a>在本地测试脚本

管道中最常见的失败之一是附加的脚本（数据清理脚本、评分脚本等）不按预期方式运行，或者在远程计算上下文中包含运行时错误，而这些错误在 Azure 机器学习工作室中的工作区内难以调试。 

管道本身不能在本地运行，但在本地计算机上运行隔离的脚本可以更快地进行调试，因为无需等待计算和环境生成过程。 执行此操作需要完成一些开发工作：

* 如果数据位于云数据存储中，则需要下载数据并使其可供脚本使用。 使用较小的数据样本能够很好地在运行时减少系统开销，并快速获取有关脚本行为的反馈
* 如果你正在尝试模拟某个中间管道步骤，可能需要手动生成特定脚本预期前一步骤提供的对象类型
* 还需要定义自己的环境，并复制远程计算环境中定义的依赖项

在本地环境中运行脚本安装后，执行如下所述的调试任务就会容易得多：

* 附加自定义调试配置
* 暂停执行和检查对象状态
* 捕获运行时之前不会公开的类型或逻辑错误

> [!TIP] 
> 确认脚本按预期运行后，合理的下一步是在单步管道中运行该脚本，然后尝试在包含多个步骤的管道中运行该脚本。

### <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

在开始生成管道之前，在本地测试脚本是调试主要代码段和复杂逻辑的适当方式，但在某个时间点，你可能需要在执行实际管道运行本身期间调试脚本，尤其是在诊断与管道步骤交互期间发生的行为时。 我们建议在步骤脚本中充分使用 `print()` 语句，以便可以查看远程执行期间的对象状态和预期值，就像在调试 JavaScript 代码时一样。

日志文件 `70_driver_log.txt` 包含： 

* 脚本执行期间输出的所有语句
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

下表包含管道开发期间出现的一些常见问题，以及可能的解决方法。

| 问题 | 可能的解决方法 |
|--|--|
| 无法将数据传递给 `PipelineData` 字典 | 确保已在脚本中创建了一个目录，该目录对应于管道预期步骤要将数据输出到的位置。 大多数情况下，输入参数将定义输出目录，然后你需要显式创建该目录。 使用 `os.makedirs(args.output_dir, exist_ok=True)` 创建输出目录。 有关演示此设计模式的评分脚本示例，请参阅[该教程](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)。 |
| 依赖项 bug | 如果在本地开发并测试了脚本，但在管道中的远程计算上运行时发现了依赖项问题，请确保计算环境依赖项和版本与测试环境相匹配。 （请参阅[环境构建、缓存和重新使用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| 计算目标出现不明确的错误 | 删除再重新创建计算目标可以解决计算目标的某些问题。 |
| 管道未重复使用步骤 | 默认已启用步骤重复使用，但是，请确保未在管道步骤中禁用它。 如果已禁用重复使用，则步骤中的 `allow_reuse` 参数将设置为 `False`。 |
| 管道不必要地重新运行 | 为了确保步骤仅在基础数据或脚本发生更改时才重新运行，请解耦每个步骤的目录。 如果对多个步骤使用同一个源目录，则可能会遇到不必要的重新运行。 在管道步骤对象中使用 `source_directory` 参数以指向该步骤的隔离目录，并确保未对多个步骤使用同一个 `source_directory` 路径。 |

### <a name="logging-options-and-behavior"></a>日志记录选项和行为

下表提供了针对管道的不同调试选项的信息。 这并不是一个详尽的列表，因为除了此处所示的 Azure 机器学习、Python 和 OpenCensus 以外，其他选项也存在。

| 库                    | 类型   | 示例                                                          | 目标                                  | 资源                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure 机器学习 SDK | 指标 | `run.log(name, val)`                                             | Azure 机器学习门户 UI             | [如何跟踪试验](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml。运行类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
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

本部分概述了如何在设计器中对管道进行故障排除。 对于在设计器中创建的管道，可以在 "创作" 页或 "管道运行详细信息" 页中找到**70_driver_log**文件。

### <a name="get-logs-from-the-authoring-page"></a>从创作页获取日志

提交管道运行并保留在创作页中时，可以查找每个模块完成运行时为每个模块生成的日志文件。

1. 在创作画布中选择已完成运行的模块。
1. 在模块的右窗格中，请参阅**输出 + 日志**选项卡。
1. 展开右窗格，然后选择 " **70_driver_log** "，以在浏览器中查看文件。 你还可以在本地下载日志。

    ![设计器中的展开输出窗格](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>从管道运行获取日志

您还可以在 "管道运行详细信息" 页中找到特定运行的日志文件，该文件可在工作室的 "**管道**" 或 "**试验**" 一节中找到。

1. 选择在设计器中创建的管道运行。

    !["管道运行" 页](./media/how-to-debug-pipelines/designer-pipelines.png)

1. 在预览窗格中选择模块。
1. 在模块的右窗格中，请参阅**输出 + 日志**选项卡。
1. 展开右窗格以便在浏览器中查看**70_driver_log**的文件，或选择文件以本地下载日志。

> [!IMPORTANT]
> 若要从管道运行详细信息页更新管道，必须将管道运行**克隆**到新管道草案。 管道运行是管道的快照。 它类似于日志文件，并且无法更改。 

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

4. 使用`launch.json`文件告诉 VS Code 要将调试器连接到的 IP 地址。

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

1. 添加一个`if`语句，该语句启动 PTVSD 并等待调试器附加。 如果在超时之前未附加调试器，则脚本将继续正常运行。

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

以下 Python 示例显示了启用调试`train.py`的基本文件：

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

若要提供启动 PTVSD 和获取运行上下文所需的 Python 包，请创建环境并设置`pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`。 更改 SDK 版本，使其与你正在使用的版本匹配。 下面的代码段演示如何创建环境：

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

在[配置 Python 脚本](#configure-python-scripts)部分，将两个新参数添加到 ML 管道步骤所使用的脚本中。 下面的代码段演示如何使用这些参数启用组件调试并设置超时。 它还演示了如何使用之前通过设置`runconfig=run_config`创建的环境：

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

管道运行时，每个步骤创建一个子运行。 如果启用调试，则修改后的脚本将在中记录类似于以下文本`70_driver_log.txt`的信息以供子运行：

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

保存`ip_address`值。 在下一部分中使用。

> [!TIP]
> 还可以从运行日志中查找此管道步骤的子运行日志的 IP 地址。 有关查看此信息的详细信息，请参阅[监视 AZURE ML 试验运行和指标](how-to-track-experiments.md)。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在 VS Code 开发环境中安装针对 Visual Studio 的 Python 工具（PTVSD），请使用以下命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    有关结合使用 VS Code 和 PTVSD 的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要配置 VS Code 以便与运行调试器的 Azure 机器学习计算进行通信，请创建新的调试配置：

    1. 在 VS Code 中，选择“调试”菜单，然后选择“打开配置”________。 打开一个名为 launch.json____ 的文件。

    1. 在__启动 json__文件中，找到包含`"configurations": [`的行，然后在其后面插入以下文本。 将`"host": "10.3.0.5"`条目更改为在上一节的日志中返回的 IP 地址。 将`"localRoot": "${workspaceFolder}/code/step"`条目更改为包含要调试的脚本副本的本地目录：

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
        > 如果“配置”部分已存在其他项，请在插入的代码后添加一个逗号 (,)。

        > [!TIP]
        > 最佳做法是将脚本的资源保留在不同的目录中，这就是`localRoot`示例值引用`/code/step1`的原因。
        >
        > 如果正在调试多个脚本，请在不同的目录中为每个脚本创建一个单独的配置节。

    1. 保存 launch.json____ 文件。

### <a name="connect-the-debugger"></a>连接调试器

1. 打开 VS Code 并打开脚本的本地副本。
2. 设置在附加后要使脚本停止的断点。
3. 当子进程正在运行脚本， `Timeout for debug connection`而在日志中显示时，请使用 F5 键或选择 "__调试__"。 出现提示时，请选择 " __Azure 机器学习计算：远程调试__" 配置。 还可以从侧栏中选择 "调试" 图标，从 "调试" 下拉菜单中选择 " __Azure 机器学习：远程调试__"，然后使用绿色箭头附加调试器。

    此时，VS Code 连接到计算节点上的 PTVSD，并在之前设置的断点处停止。 现在可以在代码运行时逐句调试代码、查看变量等。

    > [!NOTE]
    > 如果日志中显示一条指示`Debugger attached = False`，则超时已过期，该脚本在不使用调试器的情况下继续。 再次提交管道，然后在`Timeout for debug connection`消息之后、超时过期之前连接调试器。

## <a name="next-steps"></a>后续步骤

* 有关 [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 包和 [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 包的帮助信息，请参阅 SDK 参考。

* 请参阅[设计器异常和错误代码](algorithm-module-reference/designer-error-codes.md)的列表。
