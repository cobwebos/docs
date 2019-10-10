---
title: 在 Azure 中使用 Ansible
description: 介绍如何使用 Ansible 自动执行云预配、配置管理和应用程序部署。
keywords: ansible, azure, devops, 概述, 云预配, 配置管理, 应用程序部署, ansible 模块, ansible playbook
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b6b9656edc43f38a4cb005be53c33bb98781679c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241513"
---
# <a name="using-ansible-with-azure"></a>在 Azure 中使用 Ansible

[Ansible](https://www.ansible.com) 是一个开源产品，用于自动执行云预配、配置管理和应用程序部署。 使用 Ansible 可预配虚拟机、容器和网络，以及完成云基础结构。 此外，使用 Ansible 还可在环境中自动部署和配置资源。

本文提供了在 Azure 中使用 Ansible 的一些好处的基本概述。

## <a name="ansible-playbooks"></a>Ansible playbook

[Ansible playbook](https://docs.ansible.com/ansible/latest/playbooks.html) 可让你指导 Ansible 配置你的环境。 Playbook 是使用 YAML 编码的，以便人类可读。 教程部分提供了许多使用 Playbook 安装和配置 Azure 资源的示例。 

## <a name="ansible-modules"></a>Ansible 模块

Ansible 包括一组 [Ansible 模块](https://docs.ansible.com/ansible/latest/modules_by_category.html)，这些模块可直接在远程主机上或通过 [playbook](https://docs.ansible.com/ansible/latest/playbooks.html) 运行。 用户可创建他们自己的模块。 模块用于控制系统资源（例如，服务、包或文件）或执行系统命令。

为了与 Azure 服务进行交互，Ansible 包含一套 [Ansible 云模块](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure)。 可使用这些模块在 Azure 上创建和编排基础结构。 

## <a name="migrate-existing-workload-to-azure"></a>将现有工作负荷迁移到 Azure

使用 Ansible 定义基础结构后，就可以应用应用程序的 playbook，让 Azure 根据需要自动缩放环境。 

## <a name="automate-cloud-native-application-in-azure"></a>自动执行 Azure 中的云本机应用程序

使用 Ansible 可以通过 Azure 微服务自动执行 Azure 中的云本机应用程序（如 [Azure Functions](https://azure.microsoft.com//services/functions/) 和 [Kubernetes on Azure](https://azure.microsoft.com/services/container-service/kubernetes/)）。  

## <a name="manage-deployments-with-dynamic-inventory"></a>使用动态清单管理部署

Ansible 通过其[动态清单](https://docs.ansible.com/ansible/intro_dynamic_inventory.html)功能，提供从 Azure 资源拉取清单的功能。 然后，可以通过 Ansible 标记现有 Azure 部署和管理 这些标记的部署。

## <a name="additional-azure-marketplace-options"></a>其他 Azure 市场选项

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) 是由 Red Hat 制作的 Azure 市场映像。 

Ansible Tower 是 Ansible 的基于 Web 的 UI 和仪表板，具有以下功能：

* 可通过该工具定义基于角色的访问控制、作业调度和图形化库存管理。 
* 包括 REST API 和 CLI，因此你可将 Tower 插入现有工具和流程中。 
* 支持 playbook 运行的实时输出。 
* 加密凭据（如 Azure 和 SSH 密钥），这样就可以在不公开凭据的情况下委派任务。

## <a name="ansible-module-and-version-matrix-for-azure"></a>适用于 Azure 的 Ansible 模块和版本矩阵

Ansible 包括一套用于预配和配置 Azure 资源的模块。 这些资源包括虚拟机、规模集、网络服务和容器服务。 [Ansible 矩阵](./ansible-matrix.md)列出了 Azure 的 Ansible 模块及发布的 Ansible 版本。

## <a name="next-steps"></a>后续步骤

- [快速入门：将适用于 Azure 的 Ansible 解决方案模板部署到 CentOS](./ansible-deploy-solution-template.md)
- [快速入门：使用 Ansible 在 Azure 中配置 Linux 虚拟机](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)