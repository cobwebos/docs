---
title: 在 Azure 中使用 Ansible 创建和配置 Azure Kubernetes 服务群集
description: 了解如何在 Azure 中使用 Ansible 创建和管理 Azure Kubernetes 服务群集
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, 容器, Kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: df1efc1506fbbe51ba5afb03f147c51a57d9bbdb
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727052"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>在 Azure 中使用 Ansible 创建和配置 Azure Kubernetes 服务群集
使用 Ansible 可以在环境中自动部署和配置资源。 可以使用 Ansible 管理 Azure Kubernetes 服务 (AKS)。 本文介绍如何使用 Ansible 创建和配置 Azure Kubernetes 服务群集。

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- **Azure 服务主体** - [创建服务主体](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal)时，请记下以下值：**appId**、**displayName**、**密码**和**租户**。

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.6。

## <a name="create-a-managed-aks-cluster"></a>创建托管的 AKS 群集
本部分中的代码提供了一个示例 Ansible playbook，用以创建一个资源组，以及一个驻留在该资源组中的 AKS 群集。

> [!Tip]
> 对于 `your_ssh_key` 占位符，请以单行格式输入你的 RSA 公钥 - 以 "ssh-rsa" 开头（不含引号）。

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

以下项目符号有助于说明前面的 Ansible playbook 代码：
- **tasks** 中的第一部分在 **eastus** 位置定义了名为 **myResourceGroup** 的资源组。
- **tasks** 中的第二部分在 **myResourceGroup** 资源组中定义了名为 **myAKSCluster** 的 AKS 群集。

若要使用 Ansible 创建 AKS 群集，请将前面的示例 playbook 保存为 `azure_create_aks.yml`，然后使用以下命令运行该 playbook：

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

**ansible-playbook* 命令的输出类似于以下内容，表明已成功创建 AKS 群集：

  ```Output
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>缩放 AKS 节点

前一部分中的示例 playbook 定义了两个节点。 如果群集上需要更少或更多容器工作负荷，则可以轻松调整节点数。 本部分中的示例 playbook 将节点数从两个节点增加到三个节点。 通过更改 **agent_pool_profiles** 块中的 **count** 值来修改节点计数。

> [!Tip]
> 对于 `your_ssh_key` 占位符，请以单行格式输入你的 RSA 公钥 - 以 "ssh-rsa" 开头（不含引号）。

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

若要使用 Ansible 缩放 Azure Kubernetes 服务群集，请将前面的 playbook 保存为 *azure_configure_aks.yml*，然后运行 playbook，如下所示：

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

以下输出显示已成功创建 AKS 群集：

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>删除托管的 AKS 群集

以下示例 Ansible playbook 部分演示了如何删除 AKS 群集：

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name:
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

若要使用 Ansible 删除 Azure Kubernetes 服务群集，请将前面的 playbook 保存为 *azure_delete_aks.yml*，然后运行 playbook，如下所示：

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

以下输出显示已成功删除 AKS 群集：
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [教程：在 Azure Kubernetes 服务 (AKS) 中缩放应用程序](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
