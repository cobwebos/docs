---
title: 教程 - 使用 Ansible 通过 Azure 应用程序网关管理 Web 流量 | Microsoft Docs
description: 了解如何使用 Ansible 创建并配置 Azure 应用程序网关来管理 Web 流量
keywords: ansible, azure, devops, bash, playbook, 应用程序网关, 负载均衡器, web 流量
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9f8ed3e1da72db3e1b13d5d2aef1cce8fc3922a2
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231256"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>教程：使用 Ansible 通过 Azure 应用程序网关管理 Web 流量

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure 应用程序网关](/azure/application-gateway/overview)是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。 传统负载均衡器根据源 IP 地址和端口将流量路由到目标 IP 地址和端口。 应用程序网关提供更精细的控制级别，可根据 URL 路由流量。 例如，可进行如下定义：如果 `images` 是 URL 的路径，则将流量路由到为映像配置的特定服务器集（称为池）。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 设置网络
> * 使用 HTTPD 映像创建两个 Azure 容器实例
> * 创建一个应用程序网关，该网关适用于服务器池中的 Azure 容器实例

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>创建资源组

本部分中的 playbook 代码将创建一个 Azure 资源组。 资源组是配置 Azure 资源的逻辑容器。  

将以下 playbook 保存为 `rg.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

运行 playbook 之前，请参阅以下说明：

- 该资源组名为 `myResourceGroup`。 本教程中的所有示例都使用此值。
- 在 `eastus` 位置创建资源组。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>创建网络资源

本部分中的 playbook 代码创建了一个虚拟网络，使应用程序网关能够与其他资源进行通信。

将以下 playbook 保存为 `vnet_create.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

运行 playbook 之前，请参阅以下说明：

* `vars` 部分包含用于创建网络资源的值。 
* 需要为特定环境更改这些值。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>创建服务器

本部分中的 playbook 代码创建两个具有 HTTPD 映像的 Azure 容器实例，用作应用程序网关的 Web 服务器。  

将以下 playbook 保存为 `aci_create.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

本部分中的 playbook 代码创建名为 `myAppGateway` 的应用程序网关。  

将以下 playbook 保存为 `appgw_create.yml`：

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

运行 playbook 之前，请参阅以下说明：

* `appGatewayIP` 在 `gateway_ip_configurations` 块中定义。 子网引用是网关的 IP 配置所必需的。
* `appGatewayBackendPool` 在 `backend_address_pools` 块中定义。 应用程序网关必须至少具有一个后端地址池。
* `appGatewayBackendHttpSettings` 在 `backend_http_settings_collection` 块中定义。 它指定将端口 80 和 HTTP 协议用于通信。
* `appGatewayHttpListener` 在 `backend_http_settings_collection` 块中定义。 它是与 appGatewayBackendPool 关联的默认侦听器。
* `appGatewayFrontendIP` 在 `frontend_ip_configurations` 块中定义。 它将 myAGPublicIPAddress 分配到 appGatewayHttpListener。
* `rule1` 在 `request_routing_rules` 块中定义。 它是与 appGatewayHttpListener 关联的默认路由规则。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook appgw_create.yml
```

创建应用程序网关可能需要几分钟时间。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在[创建资源组](#create-a-resource-group)部分中指定位置。 请注意它的值。

1. 在[创建网络资源](#create-network-resources)部分中指定域。 请注意它的值。

1. 对于测试 URL，将以下模式替换为位置和域：`http://<domain>.<location>.cloudapp.azure.com`。

1. 浏览到测试 URL。

1. 如果看到以下页面，则说明应用程序网关正在按预期工作。

    ![成功测试正在工作的应用程序网关](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。 

将以下代码保存为 `cleanup.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 上的 Ansible](/azure/ansible/)