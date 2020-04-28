---
title: 使用防火墙后面的 Azure 机器学习
titleSuffix: Azure Machine Learning
description: 安全地使用 Azure 防火墙后面的 Azure 机器学习。 了解必须通过防火墙允许 Azure 机器学习正常运行的主机。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196317"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>使用 Azure 防火墙后面的 Azure 机器学习工作区

本文介绍如何配置用于 Azure 机器学习的 Azure 防火墙。

Azure 防火墙可用于控制对 Azure 机器学习工作区和公共 internet 的访问。 如果配置不正确，则防火墙可能会在使用工作区时出现问题。

## <a name="network-rules"></a>网络规则

在防火墙上创建一个网络规则，以允许与本文中的地址通信。

> [!TIP]
> 添加网络规则时，将__协议__设置为 "任意"，将端口设置`*`为。
>
> 有关配置 Azure 防火墙的详细信息，请参阅[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)。

## <a name="microsoft-hosts"></a>Microsoft 主机

本部分中的主机由 Microsoft 所有，并提供工作区正常工作所需的服务。

| **主机名** | **目的** |
| ---- | ---- |
| **\*。 batchai.core.windows.net** | 训练群集 |
| **ml.azure.com** | Azure 机器学习工作室 |
| **\*。 azureml.ms** | 由 Azure 机器学习 Api 使用 |
| **\*。 experiments.azureml.net** | 由运行在 Azure 机器学习中的试验使用|
| **\*。 modelmanagement.azureml.net** | 用于注册和部署模型|
| **mlworkspace.azure.ai** | 查看工作区时由 Azure 门户使用 |
| **\*。 aether.ms** | 在运行 Azure 机器学习管道时使用 |
| **\*。 instances.azureml.net** | Azure 机器学习计算实例 |
| **windows.net** | Azure Blob 存储 |
| **vault.azure.net** | Azure Key Vault |
| **microsoft.com** | 基本 docker 映像 |
| **azurecr.io** | Azure 容器注册表 |

## <a name="python-hosts"></a>Python 主机

本部分中的主机用于安装 Python 包。 它们在开发、培训和部署过程中是必需的。 

| **主机名** | **目的** |
| ---- | ---- |
| **anaconda.com** | 安装 conda 包时使用 |
| **pypi.org** | 安装 pip 包时使用 |

## <a name="r-hosts"></a>R 主机

本部分中的主机用于安装 R 包。 它们在开发、培训和部署过程中是必需的。

> [!IMPORTANT]
> 在内部，R SDK for Azure 机器学习使用 Python 包。 因此，你还必须通过防火墙允许 Python 主机。

| **主机名** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | 安装 CRAN 包时使用。 |

后续步骤

* [[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)]
* [在 Azure 虚拟网络中保护 Azure ML 试验和推理作业](how-to-enable-virtual-network.md)