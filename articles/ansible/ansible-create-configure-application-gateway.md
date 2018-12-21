---
title: 使用 Ansible 通过 Azure 应用程序网关管理 Web 流量
description: 了解如何使用 Ansible 创建并配置 Azure 应用程序网关来管理 Web 流量
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, 应用程序网关, 负载均衡器, web 流量
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: af7f22ae5c289a01e6876d8ce586cb32383c8d3b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253338"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>使用 Ansible 通过 Azure 应用程序网关管理 Web 流量

[Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。

使用 Ansible 可以在环境中自动部署和配置资源。 本文介绍了如何使用 Ansible 创建应用程序网关， 以及如何使用网关管理发往两个在 Azure 容器实例中运行的 Web 服务器的流量。

本教程演示如何：

> [!div class="checklist"]
> * 设置网络
> * 使用 HTTPD 映像创建两个 Azure 容器实例
> * 创建一个应用程序网关，该网关适用于服务器池中的 Azure 容器实例

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.7。 

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。  

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

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

将此 playbook 另存为 *rg.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>创建网络资源

首先，请创建一个虚拟网络，以便应用程序网关与其他资源通信。

以下示例创建一个名为 **myVNet** 的虚拟网络、一个名为 **myAGSubnet** 的子网、一个名为 **myAGPublicIPAddress** 的公用 IP 地址和一个名为 **mydomain** 的域。

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

将此 playbook 另存为 *vnet_create.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>创建服务器

以下示例介绍如何使用 HTTPD 映像创建两个 Azure 容器实例，用作应用程序网关的 Web 服务器。  

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

将此 playbook 另存为 *aci_create.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

以下示例创建一个名为 **myAppGateway** 且具有后端、前端和 HTTP 配置的应用程序网关。  

* **appGatewayIP** 在 **gateway_ip_configurations** 块中定义。 子网引用是网关的 IP 配置所必需的。
* **appGatewayBackendPool** 在 **backend_address_pools** 块中定义。 应用程序网关必须至少具有一个后端地址池。
* **appGatewayBackendHttpSettings** 在 **backend_http_settings_collection** 块中定义。 它指定将端口 80 和 HTTP 协议用于通信。
* **appGatewayHttpListener** 在 **backend_http_settings_collection** 块中定义。 它是与 appGatewayBackendPool 关联的默认侦听器。
* **appGatewayFrontendIP** 在 **frontend_ip_configurations** 块中定义。 它将 myAGPublicIPAddress 分配到 appGatewayHttpListener。
* **rule1** 在 **request_routing_rules** 块中定义。 它是与 appGatewayHttpListener 关联的默认路由规则。

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

将此 playbook 另存为 *appgw_create.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook appgw_create.yml
```

创建应用程序网关可能需要几分钟时间。

## <a name="test-the-application-gateway"></a>测试应用程序网关

在网络资源的示例 playbook 中，你在 **eastus** 中创建了名为 **mydomain** 的域。 转到浏览器中的 `http://mydomain.eastus.cloudapp.azure.com`。 如果看到以下页面，则说明应用程序网关正在按预期工作。

![成功测试正在工作的应用程序网关](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>清理资源

如果不需要这些资源，可运行以下代码将其删除。 此命令删除名为 **myResourceGroup** 的资源组。

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

将此 playbook 另存为 *rg_delete*.yml。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 上的 Ansible](https://docs.microsoft.com/azure/ansible/)
