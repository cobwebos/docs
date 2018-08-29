---
title: 使用 Bash in Azure Cloud Shell 运行 Ansible
description: 了解如何使用 Bash in Azure Cloud Shell 执行各种 Ansible 任务
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 9928f646905dd0da4b15166ec55e5d8a183cb210
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "42139781"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>使用 Bash in Azure Cloud Shell 运行 Ansible

在本教程中，将学习如何在 Cloud Shell 中使用 Bash 将 Azure 订阅配置为 Ansible 工作区。 

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **配置 Azure Cloud Shell** - 如果不熟悉 Azure Cloud Shell，请参阅 [Bash in Azure Cloud Shell 快速入门](https://docs.microsoft.com/azure/cloud-shell/quickstart)一文，了解如何启动和配置 Cloud Shell。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>自动凭据配置

登录 Cloud Shell 后，Ansible 将通过 Azure 进行身份验证以管理基础架构，无需任何其他配置。 如果有多个订阅，可通过导出 `AZURE_SUBSCRIPTION_ID` 环境变量来选择 Ansible 应使用哪个订阅。 要列出所有 Azure 订阅，请运行以下命令：

```azurecli-interactive
az account list
```

通过要使用的订阅的 ID，按如下设置 AZURE_SUBSCRIPTION_ID：

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>验证配置
要验证配置是否成功，请使用 Ansible 创建资源组。

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [使用 Ansible 在 Azure 中创建基本的虚拟机](/azure/virtual-machines/linux/ansible-create-vm)