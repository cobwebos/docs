---
title: 使用防火墙
titleSuffix: Azure Machine Learning
description: 使用 Azure 防火墙控制对 Azure 机器学习工作区的访问。 了解必须通过防火墙才能使 Azure 机器学习正常运行的主机。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 75e2ffcad4caeb6500f05ce3801797084c18a043
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321643"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>将 Azure 防火墙后的工作区用于 Azure 机器学习

本文介绍如何配置 Azure 防火墙以便用于 Azure 机器学习工作区。

Azure 防火墙可用于控制对 Azure 机器学习工作区和公共 Internet 的访问。 如果未正确配置，则防火墙可能会在使用工作区时导致问题。 Azure 机器学习工作区使用各种主机名，本文将对此进行介绍。

## <a name="network-rules"></a>网络规则

在防火墙上创建网络规则，允许流量流入和流出本文中的地址。

> [!TIP]
> 添加网络规则时，请将“协议”设置为“任何”，并将端口设置为 `*`。
>
> 有关配置 Azure 防火墙的详细信息，请参阅[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)。

## <a name="microsoft-hosts"></a>Microsoft 主机

本部分中的主机归 Microsoft 所有，它们提供工作区正常工作所需的服务。

| **主机名** | **用途** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | 训练群集 |
| **ml.azure.com** | Azure 机器学习工作室 |
| **default.exp-tas.com** | 由 Azure 机器学习工作室使用 |
| **\*.azureml.ms** | 由 Azure 机器学习 API 使用 |
| **\*.experiments.azureml.net** | 由 Azure 机器学习中运行的试验使用 |
| **\*.modelmanagement.azureml.net** | 用于注册和部署模型|
| **mlworkspace.azure.ai** | 由 Azure 门户在查看工作区时使用 |
| **\*.aether.ms** | 在运行 Azure 机器学习管道时使用 |
| **\*.instances.azureml.net** | Azure 机器学习计算实例 |
| **\*.instances.azureml.ms** | 当工作区启用了专用链接时的 Azure 机器学习计算实例 |
| **windows.net** | Azure Blob 存储 |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure 容器注册表 |
| **mcr.microsoft.com** | 用于 docker 基础映像的 Microsoft 容器注册表 |

## <a name="python-hosts"></a>Python 主机

本部分中的主机用于安装 Python 包。 开发、训练和部署过程中需要使用它们。 

| **主机名** | **用途** |
| ---- | ---- |
| **anaconda.com** | 在安装 conda 包时使用 |
| **pypi.org** | 在安装 pip 包时使用 |


## <a name="r-hosts"></a>R 主机

本部分中的主机用于安装 R 包。 开发、训练和部署过程中需要使用它们。

> [!IMPORTANT]
> 在内部，Azure 机器学习的 R SDK 使用 Python 包。 因此，还必须允许 Python 主机通过防火墙。

| **主机名** | **用途** |
| ---- | ---- |
| **cloud.r-project.org** | 在安装 CRAN 包时使用。 |

后续步骤

* [[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)]
* [保护 Azure 虚拟网络中的 Azure ML 试验和推理作业](how-to-enable-virtual-network.md)
