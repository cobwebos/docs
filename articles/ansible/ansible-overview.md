---
title: 在 Azure 中使用 Ansible
description: 介绍如何使用 Ansible 自动执行云预配、配置管理和应用程序部署。
ms.service: ansible
keywords: ansible, azure, devops, 概述, 云预配, 配置管理, 应用程序部署, ansible 模块, ansible playbook
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: article
ms.openlocfilehash: d684c4238a7550ca3ec34629fffdc9791db55954
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052694"
---
# <a name="ansible-with-azure"></a>Ansible 与 Azure

[Ansible](https://www.ansible.com) 是一个开源产品，用于自动执行云预配、配置管理和应用程序部署。 使用 Ansible 可预配虚拟机、容器和网络，以及完成云基础结构。 此外，使用 Ansible 还可以在环境中自动部署和配置资源。

本文提供了在 Azure 中使用 Ansible 的一些好处的基本概述。

## <a name="ansible-playbooks"></a>Ansible playbook

[Ansible playbook](https://docs.ansible.com/ansible/latest/playbooks.html) 是 Ansible 的配置、部署和编排语言。 它们可以描述你想要远程系统强制实施的策略或常规 IT 流程中的一组步骤。 创建 playbook 时你使用 YAML 来实现，后者定义了配置或流程的模型。

## <a name="ansible-modules"></a>Ansible 模块

Ansible 包括一组 [Ansible 模块](https://docs.ansible.com/ansible/latest/modules_by_category.html)，这些模块可直接在远程主机上执行或通过 [playbook](https://docs.ansible.com/ansible/latest/playbooks.html) 执行。 用户也可以创建他们自己的模块。 模块可用于控制系统资源（例如，服务、包或文件）或执行系统命令。

为了与 Azure 服务进行交互，Ansible 包括一组 [Ansible 云模块](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure)，这些模块提供了用于在 Azure 上轻松创建和协调基础结构的工具。 

## <a name="migrate-existing-workload-to-azure"></a>将现有工作负荷迁移到 Azure

使用 Ansible 定义基础结构后，就可以应用应用程序的 playbook，让 Azure 根据需要自动调整环境。 

## <a name="automate-cloud-native-application-in-azure"></a>自动执行 Azure 中的云本机应用程序

使用 Ansible 可以通过 Azure 微服务自动执行 Azure 中的云本机应用程序（如 [Azure Functions](https://azure.microsoft.com//services/functions/) 和 [Kubernetes on Azure](https://azure.microsoft.com/services/container-service/kubernetes/)）。  

## <a name="manage-deployments-with-dynamic-inventory"></a>使用动态清单管理部署
Ansible 通过其[动态清单](https://docs.ansible.com/ansible/intro_dynamic_inventory.html)功能，提供从 Azure 资源拉取清单的功能。 然后，可以通过 Ansible 标记现有 Azure 部署和管理 这些标记的部署。

## <a name="additional-azure-marketplace-options"></a>其他 Azure 市场选项
[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) 是 Red Hat 提供的 Azure 市场映像，可帮助组织缩放 IT 自动化，以及跨物理、虚拟和云基础结构管理复杂部署。 Ansible Tower 包括的功能提供当今的企业所需的附加可见性、控制、安全性和效率级别。 Ansible Tower 对 Azure 和 SSH 密钥等凭据进行加密，以便你可以将作业委派给经验较少的员工，而不会有泄露凭据的风险。

## <a name="ansible-module-and-version-matrix-for-azure"></a>适用于 Azure 的 Ansible 模块和版本矩阵
Ansible 配备了多个模块，这些模块可远程主机上直接执行或通过 Playbook 执行。
[Ansible 模块和版本矩阵](./ansible-matrix.md)列出了适用于 Azure 的可以预配 Azure 云资源（如虚拟机、网络和容器服务）的 Ansible 模块。 

## <a name="next-steps"></a>后续步骤
- [配置 Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [创建 Linux VM](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
