---
title: 使用 Ansible 创建、更改或删除 Azure 路由表
description: 了解如何使用 Ansible 创建、更改或删除路由表
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, 网络, 路由, 路由表
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 29672a75408e42fb9239e5d826784b46e7280805
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332262"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>使用 Ansible 创建、更改或删除 Azure 路由表
Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 上的任何默认路由，可以创建一个[路由表](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

使用 Ansible 可以在环境中自动部署和配置资源。 本文介绍如何创建、更改或删除 Azure 路由表，以及如何将路由表附加到子网。 

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.7。

## <a name="create-a-route-table"></a>创建路由表
本部分提供了一个示例 Ansible playbook，它创建了一个路由表。 在每个 Azure 位置和订阅中可创建的路由表数目有限制。 有关详细信息，请参阅 [Azure 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

将此 playbook 另存为 `route_table_create.yml`。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网
子网可以没有或有一个与其相关联的路由表。 一个路由表可与零个或多个子网相关联。 由于路由表不会关联到虚拟网络，因此，必须将路由表关联到每个相关子网。 如果虚拟网络已连接到 Azure 虚拟网络网关（ExpressRoute 或 VPN，如果结合 VPN 网关使用 BGP），则离开子网的所有流量将会根据路由表中创建的路由、[默认路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default)以及从本地网络传播的路由进行路由。 只能将路由表关联到该路由表所在的同一 Azure 位置和订阅中的虚拟网络内的子网。

本部分提供了一个示例 Ansible playbook，它用于创建一个虚拟网络和一个子网，然后将路由表关联到该子网。

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

将此 playbook 另存为 `route_table_associate.yml`。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>从子网取消关联路由表
在从子网取消关联路由表时，只需要将子网中的 `route_table` 设置为 `None`。 下面是示例 Ansible playbook。 

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

将此 playbook 另存为 `route_table_dissociate.yml`。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>创建路由
本部分提供了一个示例 Ansible playbook，它在路由表下创建路由。 它将 `virtual_network_gateway` 定义为 `next_hop_type`，并将 `10.1.0.0/16` 定义为 `address_prefix`。 该前缀不能在路由表的多个路由中重复，不过，可以包含在另一个前缀中。 若要详细了解 Azure 如何选择路由并查看所有下一跃点类型的详细说明，请参阅[路由概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```
将此 playbook 另存为 `route_create.yml`。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>删除路由
本部分提供了一个示例 Ansible playbook，它从路由表中删除路由。

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

将此 playbook 另存为 `route_delete.yml`。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>获取路由表的信息
可以通过名为 `azure_rm_routetable_facts` 的 Ansible 模块查看路由表的详细信息。 事实模块将返回路由表的信息，包括表中附加的所有路由。
下面是示例 Ansible playbook。 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

将此 playbook 另存为 `route_table_facts.yml`。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>删除路由表
如果路由表已关联到任何子网，则无法删除它。 在尝试删除路由表之前，请从所有子网[取消关联](#dissociate-a-route-table-from-a-subnet)该路由表。

可以删除该路由表和所有路由。 下面是示例 Ansible playbook。 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

将此 playbook 另存为 `route_table_delete.yml`。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](https://docs.microsoft.com/azure/ansible/)
