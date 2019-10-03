---
title: 教程 - 使用 Ansible 在 Azure 中配置 Azure Kubernetes 服务 (AKS) 群集 | Microsoft Docs
description: 了解如何在 Azure 中使用 Ansible 创建和管理 Azure Kubernetes 服务群集
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, 容器, aks, Kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1467afce60038e086daace72947c1ab21569865a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231323"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>教程：使用 Ansible 在 Azure 中配置 Azure Kubernetes 服务 (AKS) 群集

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

可将 AKS 配置为使用 [Azure Active Directory (AD)](/azure/active-directory/) 进行用户身份验证。 配置后，使用 Azure AD 身份验证令牌登录到 AKS 群集。 RBAC 可基于用户标识或目录组成员身份。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 创建 AKS 群集
> * 配置 AKS 群集

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>创建托管的 AKS 群集

示例 playbook 会创建资源组，并在资源组中创建 AKS 群集。

将以下 playbook 保存为 `azure_create_aks.yml`：

```yml
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

运行 playbook 之前，请参阅以下说明：

- `tasks` 中的第一部分定义了 `eastus` 位置中名为 `myResourceGroup` 的资源组。
- `tasks` 中的第二部分定义了 `myResourceGroup` 资源组中名为 `myAKSCluster` 的 AKS 群集。
- 对于 `your_ssh_key` 占位符，请以单行格式输入你的 RSA 公钥 - 以 "ssh-rsa" 开头（不含引号）。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook azure_create_aks.yml
```

运行 playbook 会显示如下所示的结果：

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>缩放 AKS 节点

前一部分中的示例 playbook 定义了两个节点。 可通过修改 `agent_pool_profiles` 块中的 `count` 值来调整节点数量。

将以下 playbook 保存为 `azure_configure_aks.yml`：

```yml
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

运行 playbook 之前，请参阅以下说明：

- 对于 `your_ssh_key` 占位符，请以单行格式输入你的 RSA 公钥 - 以 "ssh-rsa" 开头（不含引号）。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook azure_configure_aks.yml
```

运行 playbook 会显示如下所示的结果：

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>删除托管的 AKS 群集

示例 playbook 会删除 AKS 群集。

将以下 playbook 保存为 `azure_delete_aks.yml`：


```yml
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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook azure_delete_aks.yml
```

运行 playbook 会显示如下所示的结果：

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：在 Azure Kubernetes 服务 (AKS) 中缩放应用程序](/azure/aks/tutorial-kubernetes-scale)