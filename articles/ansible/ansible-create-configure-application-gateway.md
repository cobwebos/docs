---
title: 使用 Ansible（预览版）通过 Azure 应用程序网关管理 Web 流量
description: 了解如何使用 Ansible 创建并配置 Azure 应用程序网关来管理 Web 流量
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, azure 应用程序网关, 负载均衡器, web 流量
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 02b98cb22d897fc9599f6e44ddc57ef4211b0893
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410802"
---
# <a name="manage-web-traffic-with-azure-application-gateway-using-ansible-preview"></a>使用 Ansible（预览版）通过 Azure 应用程序网关管理 Web 流量
[Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。 

使用 Ansible 可以在环境中自动部署和配置资源。 本文展示了如何使用 Ansible 创建 Azure 应用程序网关并使用它来管理在 Azure 容器实例中运行的两台 Web 服务器的流量。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置网络
> * 使用 httpd 映像创建两个 Azure 容器实例
> * 创建后端池中包含以上 Azure 容器实例的应用程序网关


## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.7。 可以通过运行 `sudo pip install ansible[azure]==2.7.0rc2` 来安装 Ansible 2.7 RC 版本。 Ansible 2.7 将在 2018 年 10 月发布。 在那之后，你不需要在此指定版本，因为默认版本将是 2.7。 

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

将上面的 playbook 保存为 *rg.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>创建网络资源 
需要为应用程序网关创建虚拟网络才能与其他资源进行通信。 

以下示例创建一个名为 **myVNet** 的虚拟网络、一个名为 **myAGSubnet** 的子网、一个名为 **myAGPublicIPAddress** 的公用 IP 地址和名为 **mydomain** 的域。 

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

将上面的 playbook 保存为 *vnet_create.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook vnet_create.yml
```

## <a name="create-backend-servers"></a>创建后端服务器
在此示例中，你将使用 httpd 映像创建两个 Azure 容器实例，以用作应用程序网关的后端服务器。  

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

将上面的 playbook 保存为 *aci_create.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

现在，让我们创建一个应用程序网关。 以下示例创建一个名为 **myAppGateway** 且具有后端、前端和 http 配置的应用程序网关。  

> [!div class="checklist"]
> * **gateway_ip_configurations** 块中定义的 **appGatewayIP** - 子网引用是网关的 ip 配置所必需的。 
> * **backend_address_pools** 块中定义的 **appGatewayBackendPool** - 应用程序网关必须至少具有一个后端地址池。 
> * **backend_http_settings_collection** 块中定义的 **appGatewayBackendHttpSettings** - 指定将端口 80 和 HTTP 协议用于通信。 
> * **backend_http_settings_collection** 块中定义的 **appGatewayHttpListener** - 与 appGatewayBackendPool 关联的默认侦听器。 
> * **frontend_ip_configurations** 块中定义的 **appGatewayFrontendIP** - 将 myAGPublicIPAddress 分配给 appGatewayHttpListener。 
> * **request_routing_rules** 块中定义的 **rule1** - 与 appGatewayHttpListener 关联的默认路由规则。 

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

将上面的 playbook 保存为 *appgw_create.yml*。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook appgw_create.yml
```

创建应用程序网关可能需要几分钟时间。 

## <a name="test-the-application-gateway"></a>测试应用程序网关

在网络资源的以上示例 playbook 中，在**eastus**中创建了名为 **mydomain** 的域。 现在，你可以导航到浏览器，键入 `http://mydomain.eastus.cloudapp.azure.com`，你应当会看到以下页面，其中确认应用程序网关正在按预期工作。

![访问应用程序网关](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>清理资源

如果不需要这些资源，可运行以下示例命令将其删除。 此命令删除名为 **myResourceGroup** 的资源组。 

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

将上面的 playbook 保存为 *rg_delete*.yml。 若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](https://docs.microsoft.com/azure/ansible/)
