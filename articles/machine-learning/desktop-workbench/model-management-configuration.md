---
title: Azure 机器学习模型管理安装和配置 | Microsoft Docs
description: 本文档介绍了安装和配置 Azure 机器学习中的模型管理时所涉及的步骤和概念。
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 6802d1dfc360a48d8085ff07a8d4488ee1751e33
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832095"
---
# <a name="model-management-setup"></a>模型管理安装

## <a name="overview"></a>概述
本文档首先为你介绍如何使用 Azure ML 模型管理将机器学习模型作为 Web 服务进行部署和管理。 

使用 Azure ML 模型管理，可以高效部署和管理使用许多框架（包括 SparkML、Keras、TensorFlow、Microsoft 认知工具包或 Python）构建的机器学习模型。 

读完本文档后，你应该能够设置模型管理环境，并随时可以部署机器学习模型。

## <a name="what-you-need-to-get-started"></a>入门所需操作
为了充分利用本指南，你对可将模型部署到的 Azure 订阅应该拥有所有者访问权限。
Azure Machine Learning Workbench 和 [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) 上预安装了 CLI。

## <a name="using-the-cli"></a>使用 CLI
若要从 Workbench 使用命令行接口 (CLI)，请单击“文件”->“打开命令提示符”。  

在数据科学虚拟机上，连接并打开命令提示符。 键入 `az ml -h` 以查看选项。 有关命令的详细信息，请使用 --help 标志。

在所有其他系统中，将必须安装 CLI。

### <a name="installing-or-updating-on-windows"></a>在 Windows 上安装（或更新）

从 https://www.python.org/ 安装 Python。 确保已选择了安装 pip。

使用“以管理员身份运行”打开命令提示符，然后运行以下命令：

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>如果已安装了较早的版本，请先使用以下命令卸载它：
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>在 Linux 上安装（或更新）
从命令行运行以下命令，然后按照提示进行操作：

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

安装完成后，运行以下命令：

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>注销并重新登录回 SSH 会话以使更改生效。
>可以使用前面的命令更新 DSVM 上的较低版本的 CLI。
>

## <a name="deploying-your-model"></a>部署模型
使用 CLI 将模型部署为 Web 服务。 可以在本地部署 Web 服务，也可以将其部署到群集。

从本地部署开始，验证模型和代码是否正常工作，然后部署到群集以供大规模生产使用。

首先，需要设置部署环境。 环境设置是一个一次性任务。 设置完成后，可以重复使用环境进行后续部署。 有关详细信息，请参阅下一部分。

完成环境设置后：
- 系统会提示你登录到 Azure。 若要登录，请使用 Web 浏览器打开页 https://aka.ms/devicelogin，然后输入所提供的代码进行身份验证。
- 在身份验证过程中，系统会提示你输入帐户进行身份验证。 重要说明：选择一个具有有效 Azure 订阅和足够权限的帐户，以在帐户中创建资源。登录完成后，会显示订阅信息，并且会提示你确认是否要继续使用所选帐户。

### <a name="environment-setup"></a>环境设置
若要启动设置过程，需要通过输入以下命令来注册环境提供程序：

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>本地部署
若要在本地计算机上部署和测试 Web 服务，请使用以下命令设置本地环境：

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>本地 Web 服务部署要求在本地计算机上安装 Docker。 
>

本地环境设置命令在订阅中创建以下资源：
- 一个资源组（如果未提供）
- 一个存储帐户
- 一个 Azure 容器注册表 (ACR)
- Application insights

设置成功完成后，使用以下命令设置要使用的环境：

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>群集部署
对于大规模生产方案，请使用群集部署。 它设置一个 ACS 群集，并且使用 Kubernetes 作为业务流程协调程序。 可以横向扩展 ACS 群集，以处理更高的 Web 服务调用吞吐量。

若要将 Web 服务部署到生产环境，请首先使用以下命令设置环境：

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

群集环境设置命令在订阅中创建以下资源：
- 一个资源组（如果未提供）
- 一个存储帐户
- 一个 Azure 容器注册表 (ACR)
- Azure 容器服务 (ACS) 群集上的 Kubernetes 部署
- Application insights

系统将快速创建资源组、存储帐户和 ACR。 ACS 部署最多可能需要 20 分钟。 

设置完成后，需要设置用于此部署的环境。 请使用以下命令：

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> 创建环境后，对于后续部署，只需要使用上面的设置命令重复使用它即可。
>

>[!NOTE] 
>要创建 HTTPS 终结点，请在创建群集时指定 SSL 证书，方法是在 az ml env 设置中使用 --cert-name 和 --cert-pem 选项。 这将设置群集以供 https 上的请求使用，并使用提供的证书进行安全保护。 完成设置后，请创建指向群集 FQDN 的 CNAME DNS 记录。

### <a name="create-an-account"></a>创建帐户
若要部署模型，需要具有帐户。 需要为每个帐户执行一次此操作，并可以在多个部署中重复使用同一帐户。

若要创建新帐户，请使用以下命令：

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

### <a name="next-steps"></a>后续步骤
尝试库中的诸多示例之一。
