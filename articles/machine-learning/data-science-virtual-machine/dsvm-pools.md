---
title: 数据科学虚拟机池 - Azure | Microsoft Docs
description: 将数据科学 VM 池作为团队的共享资源部署
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: c7aab0435ecbd0aee57a15008ac0270159ec2eb3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837076"
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>创建数据科学虚拟机共享池

本文讨论如何创建供团队使用的数据科学虚拟机 (DSVM) 共享池。 使用共享池的好处是提高资源利用率、方便共享和协作，以及提高 IT 部门管理 DSVM 资源的效率。 

可以使用许多方法和不同的技术来创建 DSVM 池。  下面是主要方案：

* 适用于批处理的池
* 适用于交互式 VM 的池

## <a name="batch-processing-pool"></a>批处理池
若要设置主要用于以脱机批处理方式运行作业的 DSVM 池，则可使用 [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) 服务或 [Azure Batch](https://docs.microsoft.com/azure/batch/)。 

### <a name="azure-batch-ai"></a>Azure Batch AI
支持将 Ubuntu 版 DSVM 用作 Azure Batch AI 中的一个映像。 在创建 Azure Batch AI 群集的 Azure CLI 或 Python SDK 中，可以指定 ```image``` 参数并将其设置为 ```UbuntuDSVM```。 可以从 Azure 上提供的[大量 VM 实例选项](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)中选择所需处理节点的类型（是基于 GPU 的实例还是仅限 CPU 的实例）、CPU 数目、内存。 将 Batch AI 中的 Ubuntu DSVM 映像与基于 GPU 的节点配合使用时，所有必需的 GPU 驱动程序和深度学习框架都会预先安装，为你节省大量准备批处理节点的时间。 事实上，如果是在 Ubuntu DSVM 上进行交互式开发，你会注意到 Batch AI 节点和环境具有完全相同的设置和配置。 在创建 Batch AI 群集时，通常还会创建文件共享，该共享由所有节点装载并用于数据的输入和输出，以及批处理作业代码/脚本的存储。 

创建 Batch AI 群集以后，即可使用同一 CLI 或 Python SDK 来提交要运行的作业。 只需按运行批处理作业所用时间付费。 

#### <a name="more-information"></a>详细信息
* 分步演示如何使用 [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) 来管理 Batch AI
* 分步演示如何使用 [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) 来管理 Batch AI
* [Batch AI 工作程序](https://github.com/Azure/BatchAI)可用于演示如何将各种 AI/深度学习框架与 Batch AI 配合使用。

## <a name="interactive-vm-pool"></a>交互式 VM 池

使用供整个 AI/数据科学团队共享的交互式 DSVM 池，用户可以登录到提供的 DSVM 实例，因此不必为每组用户分配专用的实例。 这样可以提高资源的可用性和利用效率。 

用于创建交互式 VM 池的技术为 [Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS)，可创建并管理一组完全相同且负载均衡的自动缩放 VM。 用户登录到主池的 IP 或 DNS 地址。 规模集将会话自动路由到规模集中的可用 DSVM。 由于用户希望不管登录到什么 VM 都能使用相似的环境，因此规模集中的所有 VM 实例都会装载共享网络驱动器，例如 Azure 文件或 NFS 共享。 用户的共享工作区通常保留在已装载到每个实例上的共享文件存储上。 

[github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) 上提供了一个示例性的 Azure 资源管理器模板，用于通过 Ubuntu DSVM 实例创建 VM 规模集。 同一位置还提供了 Azure 资源管理器模板[参数文件](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)的示例。 

可以使用 Azure CLI 指定适合参数文件的值，以便通过 Azure 资源管理器模板创建 VM 规模集。 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
上述命令假定你有一份参数文件，其中包含为 VM 规模集的实例指定的值、VM 实例数、指向 Azure 文件的指针，以及将要装载到每个 VM 上的存储帐户的凭据。 在上面的命令中，参数文件是在本地引用的。 也可通过内联的方式传递参数，或者在脚本中提示用户输入这些参数。  

上面的模板启用 SSH 以及 Jupyterhub 端口（从 Ubuntu DSVM 的前端 VM 规模集通往后端池）。  请以用户身份通过正常方式直接登录到 SSH 或 JupyterHub 上的 VM。 由于 VM 实例可以动态地进行纵向缩放，因此需将任意状态保存在装载的 Azure 文件共享中。 可以使用同一方法创建 Windows DSVM 池。 

Azure DataScienceVM Github 上也提供[装载 Azure 文件的脚本](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)。 除了在参数文件的指定装载点装载 Azure 文件以外，该脚本还创建其他软链接，链接到初始用户的主目录中的已装载驱动器，并将共享 Azure 文件中特定于用户的 Notebook 目录以软链接的方式链接到 ```$HOME/notebooks/remote``` 目录，使用户能够访问、运行和保存其 Jupyter Notebook。  在 VM 上创建其他用户，以便将每个用户的 Jupyter 工作区指向共享的 Azure 文件时，可以使用同一约定。 

Azure VM 规模集支持自动缩放，允许你通过设置规则来确定何时创建更多的实例，以及在什么情况下减少实例（包括将实例数降至零），以便在 VM 根本不使用的情况下节省云硬件使用成本。 VM 规模集的文档页提供了[自动缩放](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)的详细步骤。

## <a name="next-steps"></a>后续步骤

* [设置通用标识](dsvm-common-identity.md)
* [以安全方式存储访问云资源的凭据](dsvm-secure-access-keys.md)















