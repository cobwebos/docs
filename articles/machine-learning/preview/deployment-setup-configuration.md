---
title: "Azure 机器学习模型管理安装和配置 | Microsoft Docs"
description: "本文档介绍了安装和配置 Azure 机器学习中的模型管理时所涉及的步骤和概念。"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: 391c02c5c76a3be3f5338790a81bca0311fb301b
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="model-management-setup"></a>模型管理安装

本文档首先为你介绍如何使用 Azure ML 模型管理将机器学习模型作为 Web 服务进行部署和管理。 

使用 Azure ML 模型管理，可以高效部署和管理使用许多框架（包括 SparkML、Keras、TensorFlow、Microsoft 认知工具包或 Python）构建的机器学习模型。 

读完本文档后，你应该能够设置模型管理环境，并随时可以部署机器学习模型。

## <a name="what-you-need-to-get-started"></a>入门所需操作
为了充分利用本指南，对可将模型部署到的 Azure 订阅或资源组应该拥有参与者访问权限。
Azure Machine Learning Workbench 和 [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) 上预安装了 CLI。

## <a name="using-the-cli"></a>使用 CLI
若要从 Workbench 使用命令行接口 (CLI)，请单击“文件” -> “打开命令提示符”。 

在数据科学虚拟机上，连接并打开命令提示符。 键入 `az ml -h` 以查看选项。 有关命令的详细信息，请使用 --help 标志。

在所有其他系统中，将必须安装 CLI。

### <a name="installing-or-updating-on-windows"></a>在 Windows 上安装（或更新）

从 https://www.python.org/ 安装 Python。 确保已选择了安装 pip。

使用“以管理员身份运行”打开命令提示符，然后运行以下命令：

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>在 Linux 上安装（或更新）
从命令行运行以下命令，然后按照提示操作：

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>在 Linux 上配置 Docker
为了在 Linux 上配置 Docker 以供非根用户使用，请按照以下说明操作：[Linux 安装后步骤](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> 在 Linux DSVM 上，你可以运行以下脚本来正确配置 Docker。 请记住，在运行脚本后注销并重新登录。
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>部署模型
使用 CLI 将模型部署为 Web 服务。 可在本地部署 Web 服务，也可以部署到群集。

从本地部署开始，验证模型和代码是否正常工作，然后部署到群集以供大规模生产使用。

首先，需要设置部署环境。 环境设置是一个一次性任务。 设置完成后，可以重复使用环境进行后续部署。 有关详细信息，请参阅下一部分。

完成环境设置后：
- 系统会提示你登录到 Azure。 若要登录，请使用 Web 浏览器打开页面 https://aka.ms/devicelogin，然后输入提供的代码进行身份验证。
- 在身份验证过程中，系统会提示你输入帐户进行身份验证。 重要说明：选择具有有效 Azure 订阅并且有足够权限在帐户中创建资源的帐户。 登录完成后，会显示你的订阅信息，并提示是否想要继续使用选定帐户。

### <a name="environment-setup"></a>环境设置
若要启动设置过程，需要通过输入以下命令来注册几个环境提供程序：

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>本地部署
要在本地计算机上部署和测试 Web 服务，请使用以下命令设置本地环境。 资源组名称是可选的。

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>本地 Web 服务部署要求在本地计算机上安装 Docker。 
>

本地环境安装命令在订阅中创建以下资源：
- 资源组（如果未提供，或者如果提供的名称不存在）
- 存储帐户
- Azure 容器注册表 (ACR)
- Application Insights 帐户

设置成功完成后，使用以下命令设置要使用的环境：

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>群集部署
对于大规模生产方案，请使用群集部署。 它设置一个 ACS 群集，并且使用 Kubernetes 作为业务流程协调程序。 可以横向扩展 ACS 群集，以处理更高的 Web 服务调用吞吐量。

若要将 Web 服务部署到生产环境，请首先使用以下命令设置环境：

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

群集环境设置命令在订阅中创建以下资源：
- 资源组（如果未提供，或者如果提供的名称不存在）
- 存储帐户
- 一个 Azure 容器注册表 (ACR)
- Azure 容器服务 (ACS) 群集上的 Kubernetes 部署
- Application Insights 帐户

>[!IMPORTANT]
> 若要成功创建群集环境，需要对 Azure 订阅或资源组拥有参与者访问权限。

系统将快速创建资源组、存储帐户和 ACR。 ACS 部署最多可能需要 20 分钟。 

要检查当前正在进行的群集调配的状态，请使用以下命令：

```azurecli
az ml env show -n [environment name] -g [resource group]
```

设置完成后，需要设置用于此部署的环境。 请使用以下命令：

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> 创建环境后，对于后续部署，只需要使用上面的设置命令重复使用它即可。
>

### <a name="create-a-model-management-account"></a>创建模型管理帐户
部署模型需要模型管理帐户。 每次订阅都需要执行此操作一次，并可以在多个部署中重复使用相同的帐户。

要创建新帐户，请使用以下命令：

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

若要使用现有帐户，请使用以下命令：
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>部署模型
现在可以将已保存模型部署为 Web 服务了。 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

## <a name="next-steps"></a>后续步骤
尝试库中的诸多示例之一。
