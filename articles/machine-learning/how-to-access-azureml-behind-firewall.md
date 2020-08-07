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
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 990a2d5279c796f354055328e6968ea705ea10b2
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873630"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>使用防火墙后面的工作区进行 Azure 机器学习

本文介绍如何将 Azure 防火墙配置为控制对 Azure 机器学习工作区和公共 internet 的访问。   若要了解有关保护 Azure 机器学习的详细信息，请参阅[企业安全性 Azure 机器学习](concept-enterprise-security.md)

虽然本文档中的信息基于使用[Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)，但你应该能够将其与其他防火墙产品一起使用。 如果有关于如何允许通过防火墙进行通信的问题，请查阅所使用防火墙的相关文档。

## <a name="network-rules"></a>网络规则

在防火墙上创建网络规则，允许流量流入和流出本文中的地址。

> [!TIP]
> 添加网络规则时，请将“协议”设置为“任何”，并将端口设置为 `*`。
>
> 有关配置 Azure 防火墙的详细信息，请参阅[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)。

## <a name="microsoft-hosts"></a>Microsoft 主机

如果未正确配置，则防火墙可能会在使用工作区时导致问题。 Azure 机器学习工作区均使用各种主机名。

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
| **your-acr-server-name.azurecr.io** | 仅当 Azure 容器注册表位于虚拟网络后面时才需要。 在此配置中，将从 Microsoft 环境创建专用链接到订阅中的 ACR 实例。 为 Azure 机器学习工作区使用 ACR 服务器名称。 |
| **\*。 notebooks.azure.net** | Azure 机器学习 studio 中的笔记本需要。 |

## <a name="python-hosts"></a>Python 主机

本部分中的主机用于安装 Python 包。 开发、训练和部署过程中需要使用它们。 

| **主机名** | **用途** |
| ---- | ---- |
| **anaconda.com** | 用于安装默认包。 |
| **\*。 anaconda.org** | 用于获取存储库数据。 |
| **pypi.org** | 用于列出默认索引的依赖项（如果有），并且索引不会被用户设置覆盖。 如果覆盖索引，则还必须允许** \* pythonhosted.org**。 |

## <a name="r-hosts"></a>R 主机

本部分中的主机用于安装 R 包。 开发、训练和部署过程中需要使用它们。

> [!IMPORTANT]
> 在内部，Azure 机器学习的 R SDK 使用 Python 包。 因此，还必须允许 Python 主机通过防火墙。

| **主机名** | **用途** |
| ---- | ---- |
| **cloud.r-project.org** | 在安装 CRAN 包时使用。 |

## <a name="next-steps"></a>后续步骤

* [教程：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)
* [保护 Azure 虚拟网络中的 Azure ML 试验和推理作业](how-to-enable-virtual-network.md)
