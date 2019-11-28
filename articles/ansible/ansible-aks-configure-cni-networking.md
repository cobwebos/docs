---
title: 教程 - 使用 Ansible 在 Azure Kubernetes 服务 (AKS) 中配置 Azure CNI 网络
description: 了解如何使用 Ansible 在 Azure Kubernetes 服务 (AKS) 群集中配置 kubenet 网络
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, 容器, aks, Kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156898"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>教程：使用 Ansible 在 Azure Kubernetes 服务 (AKS) 中配置 Azure CNI 网络

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

通过 AKS，可使用以下网络模型部署群集：

- [Kubenet 网络](/azure/aks/configure-kubenet) - 通常在部署 AKS 群集时创建和配置网络资源。
- [Azure CNI 网络](/azure/aks/configure-azure-cni) - AKS 群集连接到现有虚拟网络资源和配置。

要详细了解 AKS 中到应用程序的网络连接，请参阅 [AKS 中应用程序的网络概念](/azure/aks/concepts-network)。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 创建 AKS 群集
> * 配置 Azure CNI 网络

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>创建虚拟网络和子网

本部分的示例 playbook 代码用于：

- 创建虚拟网络
- 在虚拟网络中创建子网

将以下 playbook 保存为 `vnet.yml`：

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>在虚拟网络中创建 AKS 群集

本部分的示例 playbook 代码用于：

- 在虚拟网络中创建 AKS 群集。

将以下 playbook 保存为 `aks.yml`：

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

下面是使用示例 playbook 时要考虑的部分关键注意事项：

- 使用 `azure_rm_aks_version` 模块查找受支持的版本。
- `vnet_subnet_id` 是在上一部分中创建的子网。
- playbook 通过 `~/.ssh/id_rsa.pub` 加载 `ssh_key`。 若要修改，使用以“ssh-rsa”开头（不带引号）的单行格式。
- `client_id` 和 `client_secret` 值通过默认的凭据文件 `~/.azure/credentials` 进行加载。 可将这些值设置为服务主体，也可通过环境变量加载：

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>运行示例 playbook

本部分的示例 playbook 代码用于测试本教程中所示的各项功能。

将以下 playbook 保存为 `aks-azure-cni.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

下面是使用示例 playbook 时要考虑的部分关键注意事项：

- 将 `aksansibletest` 更改为资源组名称。
- 将 `aksansibletest` 更改为 AKS 名称。
- 将 `eastus` 更改为资源组位置。

使用 ansible-playbook 命令运行 playbook：

```bash
ansible-playbook aks-azure-cni.yml
```

运行 playbook 后，将看到与下述结果类似的输出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。 

本部分的示例 playbook 代码用于：

- 删除 `vars` 部分中引用的资源组。

将以下 playbook 保存为 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

下面是使用示例 playbook 时要考虑的部分关键注意事项：

- 将 `{{ resource_group_name }}` 占位符替换为资源组的名称。
- 将删除指定资源组中的所有资源。

使用 ansible-playbook 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Ansible 在 AKS 中配置 Azure Active Directory](./ansible-aks-configure-rbac.md)