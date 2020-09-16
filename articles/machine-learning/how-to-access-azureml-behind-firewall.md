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
ms.openlocfilehash: 081c07be49178be2415edccbfc2026336eb8a8a5
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604404"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>将防火墙后的工作区用于 Azure 机器学习

本文介绍如何配置 Azure 防火墙以控制对 Azure 机器学习工作区和公共 Internet 的访问。   若要详细了解如何保护 Azure 机器学习，请参阅 [Azure 机器学习的企业安全性](concept-enterprise-security.md)

虽然本文档中的信息基于使用 [Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)，但你应该能够将其与其他防火墙产品一起使用。 如果你对如何允许通过防火墙进行通信有疑问，请查阅你正在使用的防火墙的相关文档。

## <a name="application-rules"></a>应用程序规则

在防火墙上，创建一个 _应用程序规则_ ，以允许与本文中的地址通信。

> [!TIP]
> 添加网络规则时，请将“协议”设置为“任何”，并将端口设置为 `*`。
>
> 有关配置 Azure 防火墙的详细信息，请参阅[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)。

## <a name="routes"></a>路由

配置包含 Azure 机器学习资源的子网的出站路由时，请使用 " [强制隧道](how-to-secure-training-vnet.md#forced-tunneling) " 一节中的指南来保护培训环境。

## <a name="microsoft-hosts"></a>Microsoft 主机

如果未正确配置，则防火墙可能会在使用工作区时导致问题。 Azure 机器学习工作区使用各种主机名。

本部分中的主机归 Microsoft 所有，它们提供工作区正常工作所需的服务。

| **主机名** | **用途** |
| ---- | ---- |
| **login.microsoftonline.com** | 身份验证 |
| **management.azure.com** | 用于获取工作区信息 |
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
| **your-acr-server-name.azurecr.io** | 仅当 Azure 容器注册表位于虚拟网络后面时才需要。 通过此配置，将创建从 Microsoft 环境到订阅中的 ACR 实例的专用链接。 将 ACR 服务器名称用于 Azure 机器学习工作区。 |
| **\*.notebooks.azure.net** | Azure 机器学习工作室中的笔记本需要。 |
| **graph.windows.net** | 笔记本需要 |

> [!TIP]
> 如果你计划使用联合标识，请遵循 [保护 Active Directory 联合身份验证服务文章的最佳实践](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) 。

## <a name="python-hosts"></a>Python 主机

本部分中的主机用于安装 Python 包。 开发、训练和部署过程中需要使用它们。 

| **主机名** | **用途** |
| ---- | ---- |
| **anaconda.com**</br>**\*。 anaconda.com** | 用于安装默认包。 |
| \*.anaconda.org | 用于获取存储库数据。 |
| **pypi.org** | 用于列出默认索引的依赖项（如果有），索引不会被用户设置覆盖。 如果索引被覆盖，则还必须允许“\*.pythonhosted.org”。 |

## <a name="r-hosts"></a>R 主机

本部分中的主机用于安装 R 包。 开发、训练和部署过程中需要使用它们。

> [!IMPORTANT]
> 在内部，Azure 机器学习的 R SDK 使用 Python 包。 因此，还必须允许 Python 主机通过防火墙。

| **主机名** | **用途** |
| ---- | ---- |
| **cloud.r-project.org** | 在安装 CRAN 包时使用。 |

## <a name="azure-government-region"></a>Azure 政府区域

Azure 政府区域所需的 Url。

| **主机名** | **用途** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | 美国-亚利桑那地区 |
| **usgovvirginia.api.ml.azure.us** | 美国中南部地区 |

## <a name="next-steps"></a>后续步骤

* [教程：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)
* [保护 Azure 虚拟网络中的 Azure ML 试验和推理作业](how-to-network-security-overview.md)
