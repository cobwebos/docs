---
title: 使用 Bash in Azure Cloud Shell 运行 Ansible
description: 了解如何使用 Bash in Azure Cloud Shell 执行各种 Ansible 任务
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 9fe65f4cf10119002bcb7a3855d112d850e20f1a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>使用 Bash in Azure Cloud Shell 运行 Ansible

本教程介绍如何从 Bash in Cloud Shell 执行各种 Ansible 任务。 这些任务包括连接到虚拟机，以及创建用于创建和删除 Azure 资源组的 Ansible playbook。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **Azure 凭据** - [创建 Azure 凭据和配置 Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **配置 Azure Cloud Shell** - 如果不熟悉 Azure Cloud Shell，[Bash in Azure Cloud Shell 快速入门](https://docs.microsoft.com/azure/cloud-shell/quickstart)一文演示了如何启动和配置 Cloud Shell。 在此处启动 Cloud Shell 专用的网站：

[![启动 Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "启动 Cloud Shell")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>使用 Ansible 连接到 VM
Ansible 已创建了一个名为 [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) 的 Python 脚本，该脚本可通过向 Azure 资源管理器发出 API 请求生成 Azure 资源的动态清单。 以下步骤引导你完成使用 `azure_rm.py` 脚本连接到 Azure 虚拟机：

1. 打开 Bash in Cloud Shell。 Shell 类型在 Cloud Shell 窗口左侧表示。

1. 如果没有可使用的虚拟机，请在 Cloud Shell 中输入以下命令，以创建用于测试的虚拟机：

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. 使用 GNU `wget` 命令检索 `azure_rm.py` 脚本：

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. 使用 `chmod` 命令更改对 `azure_rm.py` 脚本的访问权限。 以下命令使用 `+x` 参数允许执行（运行）指定的文件 (`azure_rm.py`)：

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. 使用 [ansible 命令](https://docs.ansible.com/ansible/2.4/ansible.html) 连接到虚拟机： 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  连接后，应看到结果类似于以下输出：

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. 如果在本部分中创建了资源组和虚拟机

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>在 Cloud Shell 中运行 Playbook
[ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) 命令执行 Ansible playbook，并在目标主机上运行任务。 本部分介绍如何使用 Cloud Shell 创建和执行两个 playbook（一个用于创建资源组，另一个用于删除资源组）。 

1. 创建一个名为 `rg.yml` 的文件，如下所示：

  ```azurecli-interactive
  vi rg.yml
  ```

1. 将以下内容复制到 Cloud Shell 窗口（现在承载 VI 编辑器的一个实例）：

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. 保存该文件，并通过输入 `:wq` 和按 &lt;Enter> 退出 VI 编辑器。

1. 使用 `ansible-playbook` 命令运行 `rg.yml` playbook：

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 应看到结果类似于以下输出：

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. 验证部署：

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. 现在已创建了资源组，将创建第二个 Ansible playbook 来删除该资源组：

  ```azurecli-interactive
  vi rg2.yml
  ```

1. 将以下内容复制到 Cloud Shell 窗口（现在承载 VI 编辑器的一个实例）：

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. 保存该文件，并通过输入 `:wq` 和按 &lt;Enter> 退出 VI 编辑器。

1. 使用 `ansible-playbook` 命令运行 `rg2.yml` playbook：

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 应看到结果类似于以下输出：

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [使用 Ansible 在 Azure 中创建基本的虚拟机](/azure/virtual-machines/linux/ansible-create-vm)
