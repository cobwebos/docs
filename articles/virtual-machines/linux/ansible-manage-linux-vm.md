---
title: 使用 Ansible 在 Azure 中管理 Linux 虚拟机
description: 了解如何使用 Ansible 在 Azure 中管理 Linux 虚拟机
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: d9ff0387a1d02eb5f4684048aeed8ad0079b28ef
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434409"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>使用 Ansible 在 Azure 中管理 Linux 虚拟机
使用 Ansible 可以在环境中自动部署和配置资源。 可以使用 Ansible 管理 Azure 虚拟机，就像管理任何其他资源一样。 本文介绍如何使用 Ansible playbook 启动和停止 Linux 虚拟机。 

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>使用 Ansible 解除分配（停止）Azure 虚拟机
本部分演示如何使用 Ansible 解除分配（停止）Azure 虚拟机

1.  登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1.  打开 [Cloud Shell](/azure/cloud-shell/overview)。

1.  创建名为 `azure-vm-stop.yml` 的文件（用于包含 playbook）并在 VI 编辑器中将其打开，如下所示：

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  按 **I** 键进入插入模式。

1.  将以下示例代码粘贴到编辑器中：

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
          allocated: no
    ```

1.  按 **Esc** 键退出插入模式。

1.  保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

1.  运行示例 Ansible playbook。

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  输出如以下示例所示，其中显示虚拟机已成功地解除分配（停止）：

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>使用 Ansible 启动已解除分配（停止）的 Azure 虚拟机
本部分演示如何使用 Ansible 启动已解除分配（停止）的 Azure 虚拟机

1.  登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1.  打开 [Cloud Shell](/azure/cloud-shell/overview)。

1.  创建名为 `azure-vm-start.yml` 的文件（用于包含 playbook）并在 VI 编辑器中将其打开，如下所示：

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  按 **I** 键进入插入模式。

1.  将以下示例代码粘贴到编辑器中：

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Start the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
    ```

1.  按 **Esc** 键退出插入模式。

1.  保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

1.  运行示例 Ansible playbook。

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  输出如以下示例所示，其中显示虚拟机已成功启动：

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [使用 Ansible 管理 Azure 动态库存](/articles/ansible/ansible-manage-azure-dynamic-inventories)