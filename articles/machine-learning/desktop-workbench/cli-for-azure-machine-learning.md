---
title: 为最常见的任务安装和使用 CLI - Azure 机器学习
description: 了解如何在 Azure 机器学习中为最常见的机器学习任务安装和使用 CLI。
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ms.openlocfilehash: 45f96884bf6abe1c3868680be29b7f90cc50489d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>在 Azure 机器学习中为最常见的任务安装和使用机器学习 CLI

Azure 机器学习服务是集成式的端到端数据科学和高级分析解决方案。 专业数据科学家可以使用 Azure 机器学习服务以云的规模准备数据、开发试验和部署模型。 

Azure 机器学习提供一个命令行接口 (CLI)，可用于：
+ 管理工作区和项目
+ 设置计算目标
+ 运行训练试验
+ 查看以往运行的历史记录和指标
+ 将模型作为 Web 服务部署到生产环境
+ 管理生产模型和服务

本文演示一些最有用的 CLI 命令以方便参考。 

![Azure 机器学习 CLI](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>入门所需操作

需要获得 Azure 订阅或资源组的参与者访问权限，以便能够在其中部署模型。 此外，需要安装 Azure Machine Learning Workbench 才能运行 CLI。 

>[!IMPORTANT]
>Azure 机器学习服务随附的 CLI 不同于用于管理 Azure 资源的 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="get-and-start-cli"></a>获取并启动 CLI

若要获取此 CLI，请从以下位置下载并安装 Azure Machine Learning Workbench：
    + Windows - https://aka.ms/azureml-wb-msi。 
    + MacOS - https://aka.ms/azureml-wb-dmg 

启动 CLI：
+ 在 Azure Machine Learning Workbench 中，通过“文件”->“打开命令提示符”启动 CLI。

## <a name="get-command-help"></a>获取命令帮助 

可以在命令的后面使用 `--debug` 或 `--help` 来获取有关 CLI 命令的附加信息，例如 `az ml <xyz> --debug`，其中 `<xyz>` 是命令名称。 例如：
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Azure 机器学习的常见 CLI 任务 

本部分介绍可以使用 CLI 执行的最常见任务，包括：
+ [设置计算目标](#target)
+ [提交作业以远程执行](#jobs)
+ [使用 Jupyter Notebook](#jupyter)
+ [与运行历史记录交互](#history)
+ [将环境配置为可操作](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>设置计算目标

可在不同的目标中计算机器学习模型，这些目标包括：
+ 在本地模式下
+ 在数据科学 VM (DSVM) 中
+ 在 HDInsight 群集上

附加数据科学 VM 目标：
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

附加 HDInsight 目标：
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

在 **aml_config** 文件夹中，可以更改 conda 依赖项。 

此外，还可以在 GPU DSVM 中使用 PySpark、Python 或 Python 进行操作。 

定义 Python 操作模式：
+ 对于 Python，请在 `<target name>.runconfig` 中添加 `Framework:Python` 

+ 对于 PySpark，请在 `<target name>.runconfig` 中添加 `Framework:PySpark` 

+ 对于 GPU DSVM 中的 Python，
    1. 请在 `<target name>.runconfig` 中添加 `Framework:Python` 

    1. 另外，在 `<target name>.compute` 中添加 `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true`

准备计算目标：
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>显示订阅：<br/>
>`az account show`<br/>
>
>设置订阅：<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>提交远程作业

将作业提交到远程目标：
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>使用 Jupyter Notebook

启动 Jupyter Notebook：
```azurecli
az ml notebook start
```

此命令在 localhost 中启动 Jupyter Notebook。 可以选择内核 Python 3 以便在本地工作，或者选择内核 `<target name>` 以便在远程 VM 中工作。

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>与运行历史记录交互和浏览运行历史记录

列出运行历史记录：
```azurecli
az ml history list -o table
```

列出所有已完成的运行：
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

查找完全匹配的运行：
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

还可以下载每个运行生成的文件。 

提升文件夹输出中保存的模型：
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

下载该模型：
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>将环境配置为可操作

若要设置操作化环境，必须创建：

> [!div class="checklist"]
> * 资源组 
> * 一个存储帐户
> * 一个 Azure 容器注册表 (ACR)
> * 一个 Application Insights 帐户
> * Azure 容器服务 (ACS) 群集上的 Kubernetes 部署


设置要在 Docker 容器中测试的本地部署：
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

设置使用 Kubernetes 的 ACS 群集：
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

监视部署的状态：
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

设置应使用的环境：
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>后续步骤

参阅以下入门文章之一： 
+ [安装和开始使用 Azure 机器学习](../service/quickstart-installation.md)
+ [鸢尾花数据分类教程：第 1 部分](tutorial-classifying-iris-part-1.md)

参阅以下深度文章之一：
+ [用于管理模型的 CLI 命令](model-management-cli-reference.md)
