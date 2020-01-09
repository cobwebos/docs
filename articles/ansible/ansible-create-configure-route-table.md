---
title: 教程 - 使用 Ansible 配置 Azure 路由表
description: 了解如何使用 Ansible 创建、管理和删除 Azure 路由表。 还了解如何创建和删除路由。
keywords: ansible, azure, devops, bash, playbook, 网络, 路由, 路由表
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659791"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>教程：使用 Ansible 配置 Azure 路由表

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 如果需要提高对环境路由的控制，则可创建[路由表](/azure/virtual-network/virtual-networks-udr-overview)。 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> 创建路由表 创建虚拟网络和子网 将路由表与子网关联 从子网中取消关联路由表 创建和删除路由 查询路由表 删除路由表

## <a name="prerequisites"></a>必备条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>创建路由表

本部分中的 playbook 代码将创建一个路由表。 有关路由表限制的信息，请参阅 [Azure 限制](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits)。 

将以下 playbook 保存为 `route_table_create.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

本部分中的 playbook 代码：

* 创建虚拟网络
* 在虚拟网络中创建子网
* 将路由表关联到子网

路由表不是与虚拟网络关联， 而是与虚拟网络的子网关联。

虚拟网络和路由表必须共存于同一 Azure 位置和订阅中。

子网和路由表具有一对多关系。 可使用没有关联的路由表或一个路由表定义子网。 路由表可以与一个或多个子网关联，也可以不与任何子网关联。 

来自子网的流量基于以下路径进行路由：

- 路由表中定义的路由
- [默认路由](/azure/virtual-network/virtual-networks-udr-overview#default)
- 从本地网络传播的路由

虚拟网络必须连接到 Azure 虚拟网络网关。 如果将 BGP 与 VPN 网关一起使用，则网关可为 ExpressRoute 或 VPN。

将以下 playbook 保存为 `route_table_associate.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>从子网取消关联路由表

本部分中的 playbook 代码将取消路由表与子网的关联。

当取消路由表与子网的关联时，请将子网的 `route_table` 设置为 `None`。 

将以下 playbook 保存为 `route_table_dissociate.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>创建路由

本部分中的 playbook 代码在路由表中路由。 

将以下 playbook 保存为 `route_create.yml`：

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

运行 playbook 之前，请参阅以下说明：

* `virtual_network_gateway` 定义为 `next_hop_type`。 有关 Azure 如何选择路由的详细信息，请参阅[路由概述](/azure/virtual-network/virtual-networks-udr-overview)。
* `address_prefix` 定义为 `10.1.0.0/16`。 前缀在路由表中不能重复。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>删除路由

本部分中的 playbook 代码从路由表中删除路由。

将以下 playbook 保存为 `route_delete.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>获取路由表信息

本部分中的 playbook 代码使用 Ansible 模块 `azure_rm_routetable_facts` 来检索路由表信息。

将以下 playbook 保存为 `route_table_facts.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>删除路由表

本部分中的 playbook 代码删除路由表。

删除路由表时，会同时删除所有其路由。

如果路由表与子网关联，则无法删除此路由表。 尝试删除路由表之前，请[取消路由表与任何子网的关联](#dissociate-a-route-table-from-a-subnet)。 

将以下 playbook 保存为 `route_table_delete.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](/azure/ansible/)