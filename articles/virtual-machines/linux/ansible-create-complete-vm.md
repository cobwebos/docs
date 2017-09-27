---
title: "在 Azure 中使用 Ansible 创建完整的 Linux VM | Microsoft Docs"
description: "了解如何在 Azure 中使用 Ansible 创建和管理完整的 Linux 虚拟机环境"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b2fcc288b40c12a9b3f966156ee2eedf4acca313
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017

---

# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>在 Azure 中使用 Ansible 创建完整的 Linux 虚拟机环境
使用 Ansible 可以在环境中自动部署和配置资源。 可以在 Azure 中使用 Ansible 管理虚拟机 (VM)，管理其他任意资源也一样。 本文介绍了如何使用 Ansible 创建完整的 Linux 环境和支持资源。 还可以了解如何[使用 Ansible 创建基本 VM](ansible-create-vm.md)。


## <a name="prerequisites"></a>先决条件
若要使用 Ansible 管理 Azure 资源，需要以下条件：

- 安装在主机系统上的 Ansible 和 Azure Python SDK 模块。
    - 在 [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts)、[CentOS 7.3](ansible-install-configure.md#centos-73) 和 [SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2) 上安装 Ansible
- Azure 凭据和配置后使用 Azure 凭据的 Ansible。
    - [创建 Azure 凭据和配置 Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 
    - 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 也可以通过浏览器使用 [Cloud Shell](/azure/cloud-shell/quickstart)。


## <a name="create-virtual-network"></a>创建虚拟网络
Ansible 操作手册中的以下部分在 10.0.0.0/16 地址空间中创建了名为 myVnet 的虚拟网络：

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

若要添加子网，以下部分在 "myVnet" 虚拟网络中创建名为 "mySubnet" 的子网：

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>创建公共 IP 地址
若要通过 Internet 访问资源，请创建公共 IP 地址并将其分配到 VM。 Ansible 操作手册的以下部分创建了名为 "myPublicIP" 的公共 IP 地址：

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>创建网络安全组
网络安全组控制传入和传出 VM 的网络流量。 Ansible 操作手册的以下部分创建了名为 "myNetworkSecurityGroup" 的网络安全组并定义了允许 TCP 端口 22 上的 SSH 流量的规则：

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>创建虚拟网络接口卡
虚拟网络接口卡 (NIC) 将 VM 连接到规定的虚拟网络、公共 IP 地址和网络安全组。 Ansible 操作手册的以下部分创建名为 "myNIC"并连接到已创建的虚拟网络资源的虚拟 NIC：

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>创建虚拟机
最后一步是创建 VM 并使用所有已创建的资源。 Ansible 操作手册的以下部分创建名为 "myVM" 的 VM 并附加名为 "myNIC" 的虚拟 NIC。 在 key_data 对中输入如下所示的公钥数据：

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys: 
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>完成 Ansible 操作手册
若要汇总所有部分，请创建名为 "azure_create_complete_vm.yml" 的 Ansible 操作手册并粘贴以下内容：

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible 需要一个资源组来部署所有资源。 使用 [az group create](/cli/azure/vm#create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

若要使用 Ansible 创建完整的 VM 环境，请如下所示运行操作手册：

```bash
ansible-playbook azure_create_complete_vm.yml
```

输出类似于显示已成功创建 VM 的以下示例：

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>后续步骤
此示例创建了包括所需虚拟网络资源的完整 VM 环境。 有关使用默认选项在现有网络资源内创建 VM 的更直接的示例，请参阅[创建 VM](ansible-create-vm.md)。
