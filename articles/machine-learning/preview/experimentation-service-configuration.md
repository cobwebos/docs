---
title: "配置 Azure 机器学习试验服务 | Microsoft Docs"
description: "本文提供 Azure 机器学习试验服务的综合概述，并提供其配置方法的说明。"
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: f93c74d0c2f66e6a5001289efca07f074e3d3c5a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>配置 Azure 机器学习试验服务

## <a name="overview"></a>概述
凭借 Azure 机器学习试验服务，数据科学家可使用 Azure 机器学习的执行和运行管理功能来执行其试验。 它通过快速迭代提供敏捷试验的框架。 使用 Azure Machine Learning Workbench，可从计算机上的本地运行开始使用，还可轻松纵向和横向扩展到其他环境，如使用 GPU 的远程数据科学 VM 或运行 Spark 的 HDInsight 群集。

试验服务旨在为试验提供隔离、可重现且一致的运行。 它可帮助管理计算目标、执行环境和运行配置。 通过使用 Azure Machine Learning Workbench 执行和运行管理功能，可以在不同环境之间轻松移动。 

可以在本地执行或在云中大规模执行 Workbench 项目中的 Python 或 PySpark 脚本。 

可在以下环境中运行脚本： 

* 通过 Workbench 安装在本地计算机上的 Python (3.5.2) 环境
* 本地计算机上 Docker 容器内部的 Conda Python 环境
* 在远程 Linux 计算机上拥有和管理的 Python 环境
* 远程 Linux 计算机上 Docker 容器内部的 Conda Python 环境。 例如，[Azure 上基于 Ubuntu 的 DSVM] (https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Azure 上的 [HDInsight for Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/)

>[!IMPORTANT]
>Azure 机器学习试验服务当前支持的 Python 运行时版本为 Python 3.5.2，支持的 Spark 运行时版本为 Spark 2.1.11。 


### <a name="key-concepts-in-experimentation-service"></a>试验服务中的重要概念
请务必了解 Azure 机器学习试验执行中的以下概念。 在后续部分中，我们将详细讨论如何使用这些概念。 

#### <a name="compute-target"></a>计算目标
计算目标指定执行程序的位置，如台式机、VM 上的远程 Docker 或群集。 计算目标必须是可寻址且可供访问的。 通过 Workbench，可使用 Workbench 应用程序和 CLI 创建计算目标并管理它们。 

凭借 CLI 中的 az ml computetarget attach 命令，可创建用于运行的计算目标。

支持的计算目标为：
* 通过 Workbench 安装在计算机上的本地 Python (3.5.2) 环境。
* 计算机上的本地 Docker
* 远程 Linux-Ubuntu VM 上用户托管的 Python 环境。 例如，[Azure 上基于 Ubuntu 的 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Linux Ubuntu VM 上的远程 Docker。 例如，[Azure 上基于 Ubuntu 的 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Azure 上的 [HDInsight for Spark 群集](https://azure.microsoft.com/services/hdinsight/apache-spark/)

试验服务当前支持的 Python 运行时版本为 Python 3.5.2，支持的 Spark 运行时版本为 Spark 2.1.11。 

>[!IMPORTANT]
> 不支持将运行 Docker 的 Windows VM 作为远程计算目标。

#### <a name="execution-environment"></a>执行环境
执行环境定义在 Workbench 中运行程序所需的运行时配置和依赖项。

如果在 Workbench 默认运行时环境中运行，则可使用自己青睐的工具和包管理器管理本地执行环境。 

Conda 用于管理本地 Docker 和远程 Docker 执行，以及基于 HDInsight 的执行。 对于这些计算目标，通过 **Conda_dependencies.yml** 和 **Spark_dependencies.yml 文件**管理执行环境配置。 这些文件位于项目中的 **aml_config** 文件夹。

**支持执行环境的运行时：**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>运行配置
除了计算目标和执行环境，Azure 机器学习还提供定义和更改运行配置的框架。 在迭代试验中，试验的不同执行可能需要不同的配置。 可能需要覆盖不同的参数范围、使用不同的数据源并微调 spark 参数。 试验服务提供管理运行配置的框架。

运行 _az ml computetarget attach_ 命令将在项目的 **aml_config** 文件夹中生成两个文件：.compute 和 .runconfig，文件名遵循以下约定：_<your_computetarget_name>.compute_ 和 _<your_computetarget_name>.runconfig_。 方便起见，创建计算目标时，将自动创建 .runconfig 文件。 可在 CLI 中使用 az ml runconfigurations 命令创建和管理其他运行配置。 还可在文件系统中创建和编辑运行配置。

Workbench 中的运行配置可指定环境变量。 通过在 .runconfig 文件中添加以下部分，可指定环境变量并在代码中使用它们。 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

可在代码中访问这些环境变量。 例如，此 phyton 代码片段将打印名为“EXAMPLE_ENV_VAR1”的环境变量
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

下图显示初始试验运行的简要流程。
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>试验执行方案
在本部分中，我们将深入探讨执行方案，并了解 Azure 机器学习如何运行试验，特别是如何在本地、远程 VM 和 HDInsight 群集上运行试验。 本部分将从创建计算目标演示到执行试验。

>[!NOTE]
>对于本文的其余部分，我们将使用 CLI（命令行接口）命令来演示概念和功能。 此处所述的功能也可从 Workbench 使用。

## <a name="launching-the-cli"></a>启动 CLI
快速启动 CLI 的方法是在 Workbench 中打开项目，导航到“打开”-->“打开命令提示符”。

![](media/experimentation-service-configuration/opening-cli.png)

此命令将启动终端窗口，可在其中输入执行当前项目文件夹中脚本的命令。 此终端窗口配置了使用 Workbench 安装的 Python 3.5.2 环境。

>[!NOTE]
> 从命令窗口执行任何 az ml 命令，都需要通过 Azure 身份验证。 CLI 使用独立的身份验证缓存和桌面应用程序，因此登录到 Workbench 并不意味着已在 CLI 环境中进行身份验证。 若要进行身份验证，请使用以下步骤。 身份验证令牌将在本地缓存一段时间，因此令牌过期后重复这些步骤即可。 如果令牌过期或看到身份验证错误，请执行以下命令：

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>在项目文件夹中运行 _az ml_ 命令时，请确保项目属于当前 Azure 订阅中的 Azure 机器学习试验帐户。 否则可能会遇到执行错误。


## <a name="running-scripts-and-experiments"></a>运行脚本和试验
凭借 Workbench，可以使用 _az ml experiment submit_ 命令，执行针对各种计算目标的 Python 和 PySpark 脚本。 此命令要求运行配置定义。 

创建计算目标时，Workbench 会创建相应的 runconfig 文件，但可使用 _az ml runconfiguration create_ 命令创建其他运行配置。 还可以手动编辑运行配置文件。

Workbench 中的试验运行体验将显示运行配置。 

>[!NOTE]
>在[试验执行配置参考](experimentation-service-configuration-reference.md)部分，可以了解关于运行配置文件的详细信息。

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>在 Workbench 安装的运行时环境中本地运行脚本
凭借 Workbench，可直接针对 Workbench 安装的 Python 3.5.2 运行时运行脚本。 此默认运行时在 Workbench 安装时安装，它包括 Azure 机器学习库和依赖项。 本地执行的运行结果和项目仍保存在云中的运行历史记录服务内。

与基于 Docker 的执行不同，此配置不由 Conda 托管。 需要手动预配本地 Workbench Python 环境的包依赖项。

可以执行以下命令，在 Workbench 安装的 Python 环境中本地运行脚本。 

```
$az ml experiment submit -c local myscript.py
```

可在 Workbench CLI 窗口中键入以下命令，以查找默认 Python 环境的路径。
```
$ conda env list
```

>[!NOTE]
>目前不支持针对本地 Spark 环境直接本地运行 PySpark。 Workbench 支持在本地 Docker 上运行 PySpark 脚本。 Azure 机器学习基础 Docker 映像随预装的 Spark 2.1.11 一同安装。 

_**本地执行 Python 脚本概述：**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>在本地 Docker 上运行脚本
还可以通过试验服务，针对本地计算机上的 Docker 容器运行项目。 Workbench 提供基础 Docker 映像、Azure 机器学习库以及 Spark 2.1.11 运行时，使本地 Spark 执行更加轻松。 Docker 需要已在本地计算机上运行。

对于在本地 Docker 上运行 Python 或 PySpark 脚本，可以在 CLI 中执行以下命令。

```
$az ml experiment submit -c docker myscript.py
```
或
```
az ml experiment submit --run-configuration docker myscript.py
```

在本地 Docker 上的执行环境使用 Azure 机器学习基本 Docker 映像准备。 第一次运行时，Workbench 将下载此映像，并使用 conda_dependencies.yml 文件中指定的包将其覆盖。 此操作使初始运行较慢，但是由于 Workbench 重复使用缓存层，后续运行将非常快。 

>[!IMPORTANT]
>需要先运行az ml experiment prepare -c docker 命令来为首次运行准备 Docker 映像。 还可以在 docker.runconfig 文件中将 PrepareEnvironment 参数设置为 true。 此操作将自动准备环境作为运行执行的一部分。  

>[!NOTE]
>如果在 Spark 中运行 PySpark 脚本，除了 conda_dependencies.yml，还会使用spark_dependencies.yml。

在 Docker 映像上运行脚本有以下好处：

1. 可确保脚本在其他执行环境中可靠执行。 在 Docker 容器上运行，可帮助你发现并避免可能会影响可移植性的任何本地引用。 

2. 可以针对安装和配置复杂的运行时和框架（如 Apache Spark）快速测试代码，而无需安装这些运行时和框架。


_**本地 Docker 执行 Python 脚本概述：**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>在远程 Docker 上运行脚本
某些情况下，本地计算机上的可用资源可能不足以训练出所需模型。 在这种情况下，可通过试验服务使用远程 Docker 执行，在更强大的 VM 上轻松运行 Python 或 PySpark 脚本。 

远程 VM 应满足以下要求：
* 远程 VM 须运行 Linux-Ubuntu，并可通过 SSH 访问。 
* 远程 VM 需运行 Docker。

>[!IMPORTANT]
> 不支持将运行 Docker 的 Windows VM 作为远程计算目标


可使用以下命令，为基于 Docker 的远程执行创建计算目标定义和运行配置。

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

配置计算目标后，可使用以下命令运行脚本。
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>请记住，要使用 conda_dependencies.yml 中的规范配置执行环境。 如果 .runconfig 文件中指定了 PySpark 框架，还要使用 spark_dependencies.yml。 

远程 VM 的 Docker 构造过程与本地 Docker 运行的过程完全相同，所以应可用相同方式执行。

>[!TIP]
>若要避免由于构建第一次运行的 Docker 映像引入的延迟，可先使用以下命令准备计算目标，再执行脚本。 az ml experiment prepare -c remotedocker

远程 VM 执行 Python 脚本概述：
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>在针对远程 VM 的用户管理环境中运行脚本
试验服务还支持在远程 Ubuntu 虚拟机内部用户自己的 Python 环境中运行脚本。 这允许你管理自己的执行环境，并仍使用 Azure 机器学习功能。 

请按照以下步骤在自己的环境中运行脚本。
* 在远程 Ubuntu VM 或 DSVM 上准备 Python 环境并安装依赖项。
* 使用以下命令安装 Azure 机器学习所需项。

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>在某些情况下，可能需要以 sudo 模式运行此命令，具体取决于你的特权。 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* 使用以下命令可同时为远程 VM 执行上的用户管理运行创建计算目标定义和运行配置。
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>这会将 .compute 配置文件中的“userManagedEnvironment”参数设置为 true。

* 在 .compute 文件中设置 Python 运行时可执行文件的位置。 应引用 python 可执行文件的完整路径。 
```
pythonLocation: python3
```

配置计算目标后，可使用以下命令运行脚本。
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> 在 DSVM 上运行时，应使用以下命令

如果要直接在 DSVM 的全局 python 环境中运行，请运行此命令。
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>在 HDInsight 群集上运行脚本
HDInsight 支持 Apache Spark 的大数据分析常用平台。 Workbench 允许使用 HDInsight Spark 群集进行大数据试验。 

>[!NOTE]
>HDInsight 群集必须使用 Azure Blob 作为主存储。 尚不支持使用 Azure Data Lake 存储。

可使用以下命令为 HDInsight Spark 群集创建计算目标和运行配置：

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>如果使用 FQDN 而不是 IP 地址，并且 HDI Spark 群集的名称为 foo，则 SSH 终结点位于名为 foo-ssh.azurehdinsight.net 的驱动程序节点上。 对于使用 --address 参数的 FQDN，请务必在服务器名称中添加 -ssh 后缀。


有了计算上下文后，可以运行以下命令执行 PySpark 脚本。

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench 使用 Conda 在 HDInsight 群集上准备和管理执行环境。 通过 conda_dependencies.yml 和 spark_dependencies.yml 配置文件来管理配置。 

需要通过 SSH 访问 HDInsight 群集，才能在此模式下执行试验。 

>[!NOTE]
>支持的配置是运行 Linux（具有 Python/PySpark 3.5.2 和 Spark 2.1.11 的 Ubuntu）的 HDInsight Spark 群集。

基于 HDInsight 执行 PySpark 脚本概述
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>在 GPU 上运行脚本
若要在 GPU 上运行脚本，可以遵循此文中的指南：[如何在 Azure 机器学习中使用 GPU](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>使用基于 SSH 密钥的身份验证来创建和使用计算目标
除了基于用户名/密码的身份验证方案以外，Azure Machine Learning Workbench 还允许使用基于 SSH 密钥的身份验证来创建和使用计算目标。 将 remotedocker 或群集用作计算目标时可以使用此功能。 使用此方案时，Workbench 会创建公钥/私钥对，并报告回公钥。 将公钥追加至 ~/.ssh/authorized_keys 文件（针对你的用户名）。 然后 Azure Machine Learning Workbench 会使用基于 SSH 密钥的身份验证来访问此计算目标以及执行操作。 由于计算目标的私钥保存在工作区的密钥存储中，工作区的其他用户可以通过同样的方式使用计算目标，即提供用于创建计算目标的用户名。  

请按照以下步骤使用此功能。 

- 使用以下命令之一创建计算目标。

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
或
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- 将 Workbench 生成的公钥追加至附加计算目标上的 ~/.ssh/authorized_keys 文件。 

>[!IMPORTANT]
>需要使用创建计算目标时所用的同一个用户名登录计算目标。 

- 接着就可以使用基于 SSH 密钥的身份验证准备和使用计算目标。

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>后续步骤
* [创建和安装 Azure 机器学习](quickstart-installation.md)
* [模型管理](model-management-overview.md)
