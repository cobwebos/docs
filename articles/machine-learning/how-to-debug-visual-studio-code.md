---
title: 使用 Visual Studio Code 进行交互式调试
titleSuffix: Azure Machine Learning
description: 使用 Visual Studio Code 以交互方式调试 Azure 机器学习代码、管道和部署
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 08/06/2020
ms.openlocfilehash: a16a8432f61e39a3e36aeb748cabfa2c4b60d796
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315348"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>使用 Visual Studio Code 进行交互式调试



了解如何使用 Visual Studio Code (VS Code) 和 [depugpy](https://github.com/microsoft/debugpy/) 以交互方式调试 Azure 机器学习管道和部署。

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>对机器学习管道进行调试和故障排除

在某些情况下，可能需要以交互方式调试 ML 管道中使用的 Python 代码。 通过使用 VS Code 和 debugpy，可以在代码在训练环境中运行时附加到该代码。

### <a name="prerequisites"></a>先决条件

* 一个配置为使用 Azure 虚拟网络的 Azure 机器学习工作区。 
* 一个使用 Python 脚本作为管道步骤的一部分的 Azure 机器学习管道。 例如 PythonScriptStep。
* 一个位于虚拟网络中并供管道用来训练的 Azure 机器学习计算群集。 
* 一个位于虚拟网络中的开发环境。  该开发环境可以是下列其中一项：

  * 虚拟网络中的 Azure 虚拟机
  * 虚拟网络中笔记本 VM 的计算实例
  * 通过 VPN 或 ExpressRoute 与虚拟网络建立了专用网络连接的客户端计算机。

有关将 Azure 虚拟网络与 Azure 机器学习配合使用的详细信息，请参阅 [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)。

> [!TIP]
> 虽然可以使用不在虚拟网络后面的 Azure 机器学习资源，但仍建议使用虚拟网络。

### <a name="how-it-works"></a>工作原理

ML 管道步骤运行 Python 脚本。 可修改这些脚本来执行以下操作：

1. 记录运行这些脚本的主机的 IP 地址。 使用 IP 地址将调试器连接到脚本。

2. 启动 debugpy 调试组件，并等待调试程序建立连接。

3. 在开发环境中，监视训练过程创建的日志，以查找运行脚本的 IP 地址。

4. 使用 `launch.json` 文件告知 VS Code 要将调试器连接到哪个 IP 地址。

5. 附加调试器并以交互方式逐步运行脚本。

### <a name="configure-python-scripts"></a>配置 Python 脚本

若要启用调试，请对 ML 管道中的步骤使用的 Python 脚本进行以下更改：

1. 添加以下 import 语句：

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. 添加以下参数。 这些参数使你能够按需启用调试器，并设置附加调试器的超时：

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. 添加以下语句。 这些语句加载当前运行上下文，使你能够记录运行代码的节点的 IP 地址：

    ```python
    global run
    run = Run.get_context()
    ```

1. 添加一个 `if` 语句，用于启动 debugpy 并等待调试程序附加完成。 如果在超时之前未附加任何调试器，脚本将继续正常运行。 确保用自己的值替换 `listen` 函数的 `HOST` 和 `PORT` 值。

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

以下 Python 示例显示了一个 `train.py` 启用调试的简单文件：

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
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
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>配置 ML 管道

若要提供所需的 Python 包来启动 debugpy 并获取运行上下文，请创建一个环境并设置 `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']`。 更改 SDK 版本，使之与当前使用的版本匹配。 以下代码片段演示如何创建环境：

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
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

在[配置 Python 脚本](#configure-python-scripts)部分，已将新参数添加到 ML 管道步骤使用的脚本。 以下代码片段演示如何使用这些参数来为组件启用调试并设置超时。 此外，演示如何使用前面通过设置 `runconfig=run_config` 创建的环境：

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

当管道运行时，每个步骤将创建一个子运行。 如果启用了调试，则修改后的脚本将在子运行的 `70_driver_log.txt` 中记录类似于以下文本的信息：

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

保存 `ip_address` 值。 下一部分会用到它。

> [!TIP]
> 还可以在此管道步骤的子运行的运行日志中找到 IP 地址。 有关如何查看此信息的详细信息，请参阅[监视 Azure ML 试验运行和指标](how-to-track-experiments.md)。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在 VS Code 部署环境中安装 debugpy，请使用以下命令：

    ```bash
    python -m pip install --upgrade debugpy
    ```

    有关结合使用 VS Code 和 debugpy 的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)。

1. 若要配置 VS Code 以便与运行调试器的 Azure 机器学习计算进行通信，请创建新的调试配置：

    1. 在 VS Code 中，选择“调试”菜单，然后选择“打开配置” 。 打开一个名为 launch.json 的文件。

    1. 在 launch.json 文件中，找到包含 `"configurations": [` 的行，然后在其后插入以下文本： 将 `"host": "<IP-ADDRESS>"` 项更改为在上一部分所述的、在日志中返回的 IP 地址。 将 `"localRoot": "${workspaceFolder}/code/step"` 项更改为包含所调试脚本的副本的本地目录：

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
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
        > 最佳做法（尤其是对于管道）是将脚本的资源保留在不同的目录中，以便代码仅与每个步骤相关。 在此示例中，`localRoot` 示例值引用 `/code/step1`。
        >
        > 如果你正在调试多个脚本，请在不同的目录中为每个脚本创建一个单独的配置节。

    1. 保存 launch.json 文件。

### <a name="connect-the-debugger"></a>连接调试器

1. 打开 VS Code，然后打开脚本的本地副本。
2. 设置断点，在附加调试器后，脚本将在这些断点处停止。
3. 当子进程正在运行脚本，并且 `Timeout for debug connection` 已显示在日志中时，请按 F5 键或选择“调试”。 出现提示时，选择“Azure 机器学习计算: 远程调试”配置。 还可以从侧栏中选择“调试”图标，从“调试”下拉菜单中选择“Azure 机器学习: 远程调试”项，然后使用绿色箭头附加调试器。

    此时，VS Code 将连接到计算节点上的 debugpy，并在前面设置的断点处停止。 现在可以在代码运行时逐句调试代码、查看变量等。

    > [!NOTE]
    > 如果日志中显示的某个项指出 `Debugger attached = False`，则表示超时期限已过，而脚本在没有调试器的情况下继续运行。 再次提交管道，并在显示 `Timeout for debug connection` 消息之后、超时期限已过之前连接调试器。

## <a name="debug-and-troubleshoot-deployments"></a>对部署进行调试和故障排除

某些情况下，可能需要以交互方式调试包含在模型部署中的 Python 代码。 例如，如果输入脚本失败，并且无法通过其他记录确定原因。 通过使用 VS Code 和 debugpy，可以附加到在 Docker 容器中运行的代码。

> [!IMPORTANT]
> 使用 `Model.deploy()` 和 `LocalWebservice.deploy_configuration` 在本地部署模型时，此调试方法不起作用。 相反，你必须使用 [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-) 方法创建一个映像。

若要在本地部署 Web 服务，需要在本地系统上安装能够正常工作的 Docker。 有关使用 Docker 的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。 请注意，在使用计算实例时，已安装 Docker。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在本地 VS Code 部署环境中安装 debugpy，请使用以下命令：

    ```bash
    python -m pip install --upgrade debugpy
    ```

    有关结合使用 VS Code 和 debugpy 的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)。

1. 若要配置 VS Code，使其与 Docker 映像进行通信，请创建新的调试配置：

    1. 在 VS Code 中，选择“调试”菜单，然后选择“打开配置” 。 打开一个名为 launch.json 的文件。

    1. 在 launch.json 文件中，找到包含 `"configurations": [` 的行，然后在其后插入以下文本：

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果“配置”部分已存在其他项，请在插入的代码后添加一个逗号 (,)。

        本部分使用端口 5678 附加到 Docker 容器。

    1. 保存 launch.json 文件。

### <a name="create-an-image-that-includes-debugpy"></a>创建包括 debugpy 的映像

1. 修改部署的 Conda 环境，使其包括 debugpy。 以下示例演示使用 `pip_packages` 参数添加它的过程：

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. 若要在服务启动时启动 debugpy 并等待连接，请将以下内容添加到 `score.py` 文件的顶部：

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. 基于环境定义创建一个映像，并将该映像提取到本地注册表。 

    > [!NOTE]
    > 此示例假定 `ws` 指向 Azure 机器学习工作区，且 `model` 是要部署的模型。 `myenv.yml` 文件包含步骤 1 中创建的 Conda 依赖项。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    创建并下载映像后，映像路径（包括存储库、名称和标记，在此示例中也是摘要）会显示在类似于以下内容的消息中：

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 如果希望简化映像的操作，请使用以下命令添加标记。 将 `myimagepath` 替换为前面步骤中的位置值。

    ```bash
    docker tag myimagepath debug:1
    ```

    对于其余步骤，可以将本地映像作为 `debug:1` 而不是完整的映像路径值来进行引用。

### <a name="debug-the-service"></a>调试服务

> [!TIP]
> 如果在 `score.py` 文件中为 debugpy 连接设置超时，则必须在超时到达之前将 VS Code 连接到调试会话。 启动 VS Code，打开 `score.py` 的本地副本，设置一个断点，使其准备就绪，然后再使用本部分中的步骤进行操作。
>
> 有关调试和设置断点的详细信息，请参阅[调试](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要使用映像启动 Docker 容器，请使用以下命令：

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    这会将 `score.py` 本地附加到容器中的对应项。 因此，在编辑器中所做的任何更改都将自动反映到容器中。

1. 在容器内，在 shell 中运行以下命令

    ```bash
    runsvdir /var/runit
    ```

1. 若要将 VS Code 附加到容器中的 debugpy，请打开 VS Code 并按 F5 或选择“调试”。 出现提示时，请选择“Azure 机器学习部署: Docker 调试”配置。 还可以从侧栏中选择“调试”图标，即“Azure 机器学习部署: Docker 调试”项（位于“调试”下拉菜单），然后使用绿色箭头附加调试器。

    ![“调试”图标、“启动调试”按钮和“配置”选择器](./media/how-to-troubleshoot-deployment/start-debugging.png)

此时，VS Code 会连接到 Docker 容器内的 debugpy，并在之前设置的断点处停止。 现在可以在代码运行时逐句调试代码、查看变量等。

有关使用 VS Code 调试 Python 的详细信息，请参阅[调试 Python 代码](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019&preserve-view=true)。

### <a name="stop-the-container"></a>停止容器

若要停止容器，请使用以下命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>后续步骤

现在，你已设置 Visual Studio Code Remote，可以将计算实例用作 Visual Studio Code 中的远程计算，从而对代码进行交互式调试。 

[教程：训练自己的首个 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何将计算实例与集成的笔记本配合使用。
