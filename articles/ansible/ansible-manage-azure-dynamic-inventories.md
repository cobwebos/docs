---
title: 教程 - 使用 Ansible 配置 Azure 资源的动态库存
description: 了解如何使用 Ansible 管理 Azure 动态库存
keywords: ansible, azure, devops, bash, cloudshell, 动态库存
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/23/2019
ms.openlocfilehash: 6d520518e7180f69ee7293523dd40c8158dcfb99
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990670"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>教程：使用 Ansible 配置 Azure 资源的动态库存

使用 Ansible 可将各种源（包括 Azure 等云源）中的库存信息提取到动态库存中。  

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 配置两个测试虚拟机。 
> * 标记其中一个虚拟机
> * 在已标记的虚拟机上安装 Nginx
> * 配置动态库存，使其包含已配置的 Azure 资源

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>创建测试 VM

1. 登录到 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 打开 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

1. 创建一个 Azure 资源组，用于保存本教程创建的虚拟机。

    > [!IMPORTANT]  
    > 在此步骤中创建的 Azure 资源组必须具有完全小写的名称。 否则，生成动态清单将会失败。

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. 使用以下方法之一在 Azure 上创建两个 Linux 虚拟机：

    - **Ansible 演练手册** - [使用 Ansible 在 Azure 中创建基本的虚拟机](/azure/virtual-machines/linux/ansible-create-vm)一文演示了如何通过 Ansible 演练手册创建虚拟机。 如果使用演练手册定义其中的一个或两个虚拟机，请确保使用 SSH 连接，而不要使用密码。

    - **Azure CLI** - 在 Cloud Shell 中发出以下每条命令，以创建两个虚拟机：

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>标记 VM

可以根据用户定义的类别[使用标记来组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli)。 

### <a name="using-ansible-version--28"></a>使用低于 2.8 的 Ansible 版本
输入以下 [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) 命令和键 `nginx` 来标记虚拟机 `ansible-inventory-test-vm1`：

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>使用 2.8 及更高的 Ansible 版本
输入以下 [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) 命令和键 `Ansible=nginx` 来标记虚拟机 `ansible-inventory-test-vm1`：

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>生成动态库存

定义（并标记）虚拟机后，便可以生成动态库存了。

### <a name="using-ansible-version--28"></a>使用低于 2.8 的 Ansible 版本

Ansible 提供一个名为 [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) 的 Python 脚本，该脚本生成 Azure 资源的动态库存。 以下步骤引导你使用 `azure_rm.py` 脚本连接到两个 Azure 测试虚拟机：

1. 使用 GNU `wget` 命令检索 `azure_rm.py` 脚本：

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. 使用 `chmod` 命令更改对 `azure_rm.py` 脚本的访问权限。 以下命令使用 `+x` 参数允许执行（运行）指定的文件 (`azure_rm.py`)：

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. 使用 [ansible 命令](https://docs.ansible.com/ansible/2.4/ansible.html)连接到资源组： 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. 连接后，将看到类似于以下输出的结果：

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Ansible 版本为 2.8 及更高版本

从 Ansible 2.8 开始，Ansible 便已提供 [Azure 动态库存插件](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py)。 以下步骤将引导你使用插件：

1. 库存插件需要配置文件。 配置文件必须以 `azure_rm` 结尾，且扩展名为 `yml` 或 `yaml`。 对于本教程示例，请将以下 playbook 保存为 `myazure_rm.yml`：

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. 运行以下命令以在资源组中对 VM 执行 ping 操作：

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. 运行上述命令时，可能会收到以下错误：

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    如果收到“主机密钥验证”错误，请将以下行添加到 Ansible 配置文件中。 Ansible 配置文件位于 `/etc/ansible/ansible.cfg` 或 `~/.ansible.cfg`。

    ```bash
    host_key_checking = False
    ```

1. 运行 playbook 后，将看到类似于以下输出的结果：
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>启用 VM 标记

### <a name="if-youre-using-ansible--28"></a>如果使用的是低于 2.8 的 Ansible 版本，

- 设置标记后，需要“启用”该标记。 启用标记的方法之一是通过 `export` 命令将标记导出到环境变量 `AZURE_TAGS`：

    ```azurecli-interactive
    export AZURE_TAGS=nginx
    ```
    
- 运行以下命令：

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    现在，只会看到一个虚拟机（其标记与导出到 `AZURE_TAGS` 环境变量的值匹配）：

    ```Output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>如果使用的是 2.8 及更高的 Ansible 版本

- 运行命令 `ansible-inventory -i myazure_rm.yml --graph` 以获取以下输出：

    ```Output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- 还可以运行以下命令来测试到 Nginx VM 的连接：
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>在标记的 VM 上安装 Nginx

标记的用途是让我们快速方便地使用虚拟机的子组。 例如，假设我们只想在分配了 `nginx` 标记的虚拟机上安装 Nginx。 以下步骤演示如何轻松实现此目的：

1. 创建名为 `nginx.yml` 的文件：

   ```azurecli-interactive
   code nginx.yml
   ```

1. 将以下示例代码粘贴到编辑器中：

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=installed
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. 保存文件并退出编辑器。

1. 使用 `ansible-playbook` 命令运行 playbook：

   - Ansible 版本低于 2.8：

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible 为版本 2.8 及更高版本：

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. 运行 playbook 后，可看到类似于以下结果的输出：

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>测试 Nginx 安装

本部分演示一种测试是否在虚拟机上安装了 Nginx 的方法。

1. 使用 [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) 命令检索 `ansible-inventory-test-vm1` 虚拟机的 IP 地址。 然后，将返回值（虚拟机的 IP 地址）用作 SSH 命令的参数，以连接到该虚拟机。

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. 连接到 `ansible-inventory-test-vm1` 虚拟机后，运行 [nginx -v](https://nginx.org/en/docs/switches.html) 命令来确定是否安装了 Nginx。

    ```azurecli-interactive
    nginx -v
    ```

1. 运行 `nginx -v` 命令后，会看到 Nginx 版本（第二行），这表示已安装了 Nginx。

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. 按 `<Ctrl>D` 组合键断开 SSH 会话。

1. 针对 `ansible-inventory-test-vm2` 虚拟机执行上述步骤会生成一条信息性消息，其中指出可在何处获取 Nginx（这意味着，目前尚未安装 Nginx）：

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [快速入门：使用 Ansible 在 Azure 中配置 Linux 虚拟机](/azure/virtual-machines/linux/ansible-create-vm)
