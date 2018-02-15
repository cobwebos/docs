---
title: "使用 Ansible 管理 Azure 动态库存"
description: "了解如何使用 Ansible 管理 Azure 动态库存"
ms.service: ansible
keywords: "ansible, azure, devops, bash, cloudshell, 动态库存"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 8753d039582abdf22f105bf7f139a35c224e7c59
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>使用 Ansible 管理 Azure 动态库存
使用 Ansible 可将各种源（包括 Azure 等云源）中的库存信息提取到动态库存中。 本文介绍如何使用 [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) 配置一个 Ansible Azure 动态库存，并在该库存中创建两个虚拟机、标记其中的一个虚拟机，然后在标记的虚拟机上安装 Nginx。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **Azure 凭据** - [创建 Azure 凭据和配置 Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>创建测试虚拟机

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 打开 [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)。

1. 创建一个 Azure 资源组，用于保存本教程创建的虚拟机。

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

## <a name="tag-a-virtual-machine"></a>标记虚拟机
可以根据用户定义的类别[使用标记来组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli)。 

输入以下 [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) 命令和键 `nginx` 来标记虚拟机 `ansible-inventory-test-vm1`：

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/&lt;YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>生成动态库存
定义（并标记）虚拟机后，便可以生成动态库存了。 Ansible 提供一个名为 [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) 的 Python 脚本，该脚本可通过向 Azure 资源管理器发出 API 请求生成 Azure 资源的动态库存。 以下步骤引导你使用 `azure_rm.py` 脚本连接到两个 Azure 测试虚拟机：

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

## <a name="enable-the-virtual-machine-tag"></a>启用虚拟机标记
设置所需的标记后，需要“启用”该标记。 启用标记的方法之一是通过 **export** 命令将标记导出到名为 `AZURE_TAGS` 的环境变量：

```azurecli-interactive
export AZURE_TAGS=nginx
```

导出标记后，可以尝试再次运行 `ansible` 命令：

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

现在，只会看到一个虚拟机（其标记与导出到 **AZURE_TAGS** 环境变量的值匹配）：

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>在标记的 VM 上安装 Nginx
标记的用途是让我们快速方便地使用虚拟机的子组。 例如，假设我们只想在分配了 `nginx` 标记的虚拟机上安装 Nginx。 以下步骤演示如何轻松实现此目的：

1. 创建名为 `nginx.yml` 的文件（用于包含演练手册），如下所示：

  ```azurecli-interactive
  vi nginx.yml
  ```

1. 将以下代码插入新建的 `nginx.yml` 文件中：

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
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

1. 运行 `nginx.yml` 演练手册：

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. 运行该演练手册后，将看到类似于以下输出的结果：

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>测试 Nginx 安装
本部分演示一种测试是否在虚拟机上安装了 Nginx 的方法。

1. 使用 [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) 命令检索 `ansible-inventory-test-vm1` 虚拟机的 IP 地址。 然后，将返回值（虚拟机的 IP 地址）用作 SSH 命令的参数，以连接到该虚拟机。

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. [nginx -v](https://nginx.org/en/docs/switches.html) 命令通常用于列显 Nginx 版本。 但是，也可以使用它来确定是否安装了 Nginx。 请在连接到 `ansible-inventory-test-vm1` 虚拟机后输入该命令。

    ```azurecli-interactive
    nginx -v
    ```

1. 运行 `nginx -v` 命令后，会看到 Nginx 版本（第二行），这表示已安装了 Nginx。

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. 按 **&lt;Ctrl+D** 组合键断开 SSH 会话。

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
> [使用 Ansible 在 Azure 中创建基本的虚拟机](/azure/virtual-machines/linux/ansible-create-vm)