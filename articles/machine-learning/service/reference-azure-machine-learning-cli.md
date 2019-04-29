---
title: 机器学习 CLI 扩展
titleSuffix: Azure Machine Learning service
description: 了解适用于 Azure CLI 的 Azure 机器学习的机器学习 CLI 扩展。 Azure CLI 是一个跨平台命令行实用工具，可让你使用 Azure 云中的资源。 借助该机器学习扩展可以使用 Azure 机器学习服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2504ca9cb785529a9eab321c2521db46390632b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752983"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>将 CLI 扩展用于 Azure 机器学习服务

Azure 机器学习 CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)（适用于 Azure 平台的跨平台命令行接口）的一个扩展。 借助此扩展提供的命令，可以从命令行使用 Azure 机器学习服务。 它允许创建用于自动化机器学习工作流的脚本。 例如，可以创建用于执行以下操作的脚本：

+ 运行试验以创建机器学习模型

+ 注册机器学习模型以供客户使用

+ 打包、部署和跟踪机器学习模型的生命周期

CLI 不能取代 Azure 机器学习 SDK。 它是一个经过优化的补充工具，可以处理高度参数化的任务，例如：

* 创建计算资源

* 参数化试验提交

* 模型的注册

* 映像创建

* 服务部署

## <a name="prerequisites"></a>必备组件


* 若要使用 CLI，必须拥有 Azure 订阅。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安装扩展

若要安装机器学习 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

出现提示时，选择 `y` 安装该扩展。

若要验证是否已安装该扩展，请使用以下命令显示特定于机器学习的子命令列表：

```azurecli-interactive
az ml -h
```

> [!TIP]
> 若要更新该扩展，必须先将其__删除__，然后再__安装__。 这会安装最新版本。

## <a name="remove-the-extension"></a>删除扩展

若要删除 CLI 扩展，请使用以下命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>资源管理

以下命令演示如何使用 CLI 来管理 Azure 机器学习所用的资源。


+ 创建 Azure 机器学习服务工作区：

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ 设置默认工作区：

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
+ 附加 AKS 群集

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>试验

以下命令演示如何通过 CLI 使用试验：

* 提交试验之前附加项目（运行配置）：

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* 开始运行试验。 使用此命令时，请指定包含运行配置的 runconfig 文件的名称。 计算目标使用运行配置来为模型创建训练环境。 在此示例中，运行配置是从 `./aml_config/myrunconfig.runconfig` 文件加载的。

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    有关 runconfig 文件的详细信息，请参阅 [RunConfig](#runconfig) 部分。

* 查看提交的试验列表：

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>模型注册、映像创建和部署

以下命令演示如何注册已训练的模型，然后将其部署为生产服务：

+ 将模型注册到 Azure 机器学习：

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ 创建包含机器学习模型和依赖项的映像： 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ 将映像部署到计算目标：

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> Runconfig 文件

运行配置用于配置训练模型时所使用的训练环境。 可以使用 SDK 在内存中创建此配置，也可以从 runconfig 文件加载此配置。

Runconfig 文件是一个描述训练环境配置的文本文档。 例如，它列出了训练脚本的名称，以及包含训练模型所需的 conda 依赖项的文件。

使用 `az ml project attach` 命令附加项目时，Azure 机器学习 CLI 会创建两个默认的 `.runconfig` 文件，名为 `docker.runconfig` 和 `local.runconfig`。 

如果拥有可使用 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 类创建运行配置的代码，则可使用 `save()` 方法将其保存到 `.runconfig` 文件。

以下是 `.runconfig` 文件的内容示例：

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.4
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes. The list of vm sizes is available in 'https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```
