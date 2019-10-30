---
title: 共享池
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何创建 & 部署数据科学虚拟机（Dsvm）的共享池作为团队的共享资源。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 2a74df3aaf70270353b10f757cf3a61e8f479d74
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052996"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>创建数据科学虚拟机共享池

本文介绍如何为团队创建数据科学虚拟机（Dsvm）的共享池。 使用共享池的好处包括：更好的资源利用率、更轻松的共享和协作，以及更有效地管理 DSVM 资源。

可以使用许多方法和技术创建 DSVM 池。 本文重点介绍交互式虚拟机（Vm）的池。 另一个可选的托管计算基础结构是 Azure 机器学习计算。 有关详细信息，请参阅[设置计算目标](../service/how-to-set-up-training-targets.md#amlcompute)。

## <a name="interactive-vm-pool"></a>交互式 VM 池

使用供整个 AI/数据科学团队共享的交互式 VM 池，用户可以登录到提供的 DSVM 实例，因此不必为每组用户分配专用的实例。 此设置可实现更好的可用性，并更有效地利用资源。

使用[Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)技术创建交互式 VM 池。 可以使用规模集创建并管理一组完全相同的、负载均衡的、自动缩放的 VM。

用户登录到主池的 IP 或 DNS 地址。 规模集将会话自动路由到规模集中的可用 DSVM。 由于用户需要一个一致且熟悉的环境，而不管他们登录到的 VM 如何，规模集中 VM 的所有实例都装载共享网络驱动器，例如 Azure 文件共享或网络文件系统（NFS）共享。 用户的共享工作区通常保留在已装载到每个实例上的共享文件存储上。

[GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) 上提供了一个示例性的 Azure 资源管理器模板，用于通过 Ubuntu DSVM 实例创建规模集。 你将在同一位置找到 Azure 资源管理器模板的[参数文件](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)示例。

可以通过在 Azure CLI 中指定参数文件的值，从 Azure 资源管理器模板创建规模集：

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
前面的命令假定你有：
* 一个参数文件副本，其中包含为规模集实例指定的值。
* VM 实例数。
* 指向 Azure 文件共享的指针。
* 将在每个 VM 上装载的存储帐户的凭据。

在命令中，参数文件是在本地引用的。 也可通过内联的方式传递参数，或者在脚本中提示用户输入这些参数。  

上面的模板启用 SSH 以及 JupyterHub 端口（从 Ubuntu DSVM 的前端规模集通往后端池）。 用户以正常方式登录到安全外壳（SSH）或 JupyterHub 上的 VM。 由于 VM 实例可以动态地扩展或缩减，因此必须将任何状态保存在已装载的 Azure 文件共享中。 可以使用同一方法创建 Windows DSVM 池。

GitHub 中的 Azure DataScienceVM 存储库也提供[装载 Azure 文件共享的脚本](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)。 该脚本在参数文件的指定装载点装载 Azure 文件共享。 该脚本还在初始用户的主目录中创建指向已装载驱动器的软链接。 Azure 文件共享中的特定于用户的笔记本目录会软链接到 `$HOME/notebooks/remote` 目录，以便用户可以访问、运行和保存其 Jupyter 笔记本。 在 VM 上创建其他用户，以便将每个用户的 Jupyter 工作区指向 Azure 文件共享时，可以使用同一约定。

虚拟机规模集支持自动缩放。 你可以设置有关何时创建其他实例以及何时缩减实例的规则。 例如，可以缩减至零个实例以在未使用任何 VM 的情况下节省云硬件的使用成本。 VM 规模集的文档页提供了有关[自动缩放](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)的详细步骤。

## <a name="next-steps"></a>后续步骤

* [设置通用标识](dsvm-common-identity.md)
* [以安全方式存储访问云资源的凭据](dsvm-secure-access-keys.md)















