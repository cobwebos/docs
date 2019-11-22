---
title: 教程 - 使用 Ansible 在 Azure Kubernetes 服务 (AKS) 中配置 kubenet 网络
description: 了解如何使用 Ansible 在 Azure Kubernetes 服务 (AKS) 群集中配置 kubenet 网络
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, 容器, aks, Kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/23/2019
ms.openlocfilehash: 67b4eb9e9ee53613ec8b54b2bf8d3bbdb89778c7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881522"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>教程：使用 Ansible 在 Azure Kubernetes 服务 (AKS) 中配置 kubenet 网络

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

通过 AKS，可使用以下网络模型部署群集：

- [Kubenet 网络](/azure/aks/configure-kubenet) - 通常在部署 AKS 群集时创建和配置网络资源。
- [Azure 容器网络接口 (CNI) 网络](/azure/aks/configure-azure-cni) - AKS 群集与现有虚拟网络资源和配置相连接。

要详细了解 AKS 中到应用程序的网络连接，请参阅 [AKS 中应用程序的网络概念](/azure/aks/concepts-network)。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 创建 AKS 群集
> * 配置 Azure kubenet 网络

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>创建虚拟网络和子网

本部分的 playbook 代码会创建以下 Azure 资源：

- 虚拟网络
- 虚拟网络中的子网

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

本部分的 playbook 代码会在虚拟网络中创建 AKS 群集。 

将以下 playbook 保存为 `aks.yml`：

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

下面是使用示例 playbook 时要考虑的部分关键注意事项：

- 使用 `azure_rm_aks_version` 模块查找受支持的版本。
- `vnet_subnet_id` 是在上一部分中创建的子网。
- `network_profile` 定义了 kubenet 网络插件的属性。
- `service_cidr` 用于将 AKS 群集中的内部服务分配到 IP 地址。 此 IP 地址范围应该是未在网络中的其他位置使用的地址空间。 
- `dns_service_ip` 地址应为服务 IP 地址范围的“.10”地址。
- `pod_cidr` 应该是未在网络环境中的其他位置使用的较大地址空间。 此地址范围必须足够大，可容纳预期要扩展到的节点数。 部署群集后，无法更改此地址范围。
- Pod IP 地址范围用于将 /24 地址空间分配给群集中的每个节点。 在以下示例中，`pod_cidr` 192.168.0.0/16 为第一个节点分配 192.168.0.0/24，为第二个节点分配 192.168.1.0/24，为第三节点分配 192.168.2.0/24。
- 群集扩展或升级时，Azure 会继续将 Pod IP 地址范围分配到每个新节点。
- playbook 通过 `~/.ssh/id_rsa.pub` 加载 `ssh_key`。 若要修改，使用以“ssh-rsa”开头（不带引号）的单行格式。
- `client_id` 和 `client_secret` 值通过默认的凭据文件 `~/.azure/credentials` 进行加载。 可将这些值设置为服务主体，也可通过环境变量加载：

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>关联网络资源

创建 AKS 群集时，将创建网络安全组和路由表。 这些资源由 AKS 管理并在创建和公开服务时更新。 按如下所示将网络安全组和路由表与虚拟网络子网相关联。 

按 `associate.yml` 形式保存以下 playbook。

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

下面是使用示例 playbook 时要考虑的部分关键注意事项：

- `node_resource_group` 是创建 AKS 节点所用的资源组名称。
- `vnet_subnet_id` 是在上一部分中创建的子网。


## <a name="run-the-sample-playbook"></a>运行示例 playbook

本部分列出了用于调用本文所创任务的完整示例 playbook。 

将以下 playbook 保存为 `aks-kubenet.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

在 `vars` 部分进行如下更改：

- 将 `resource_group` 密钥的 `aksansibletest` 值更改为资源组名称。
- 将 `name` 密钥的 `aksansibletest` 值更改为 AKS 名称。
- 将 `Location` 密钥的 `eastus` 值更改为资源组位置。

使用 `ansible-playbook` 命令运行完整的 playbook：

```bash
ansible-playbook aks-kubenet.yml
```

运行 playbook 会显示如下所示的结果：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。 

将以下代码保存为 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

在 `vars` 部分，将 `{{ resource_group_name }}` 占位符替换为资源组名称。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程 - 使用 Ansible 在 AKS 中配置 Azure 容器网络接口 (CNI) 网络](./ansible-aks-configure-cni-networking.md)