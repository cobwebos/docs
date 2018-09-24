---
title: 使用 Ansible 在 Azure 中创建 Linux 虚拟机
description: 了解如何使用 Ansible 在 Azure 中创建 Linux 虚拟机
ms.service: ansible
keywords: ansible, azure, devops, 虚拟机
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/22/2018
ms.openlocfilehash: bf2116d7fc143e66309e3bbbdefc50bfa33f1521
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974555"
---
# <a name="use-ansible-to-create-a-linux-virtual-machine-in-azure"></a>使用 Ansible 在 Azure 中创建 Linux 虚拟机
Ansible 使用声明性语言，适用于通过 Ansible *playbook* 来自动完成 Azure 资源的创建、配置和部署。 本文的各个部分介绍该部分中用于创建和配置 Linux 虚拟机不同部分的 Ansible playbook 的外观。 [完整的 Ansible playbook](#complete-sample-ansible-playbook) 列在本文末尾。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>创建资源组
Ansible 需要一个在其中部署了资源的资源组。 以下示例 Ansible playbook 部分在 `eastus` 位置创建名为 `myResourceGroup` 的资源组：

```yaml
- name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
创建 Azure 虚拟机时，必须创建[虚拟网络](/azure/virtual-network/virtual-networks-overview)或使用现有的虚拟网络。 此外，还需要确定如何在虚拟网络上访问虚拟机。 以下示例 Ansible playbook 部分在 `10.0.0.0/16` 地址空间中创建名为 `myVnet` 的虚拟网络：

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

部署到虚拟网络的所有 Azure 资源都将部署到虚拟网络内的[子网](/azure/virtual-network/virtual-network-manage-subnet)中。 

以下示例 Ansible playbook 部分在 `myVnet` 虚拟网络中创建名为 `mySubnet` 的子网：

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址
[公共 IP 地址](/azure/virtual-network/virtual-network-ip-addresses-overview-arm)允许 Internet 资源与 Azure 资源进行入站通信。 在 IP 地址已分配给 Azure 资源的情况下，公共 IP 地址还允许这些资源与 Internet 和面向公众的 Azure 服务进行出站通信。 此地址专门用于该资源，直到你对其取消分配。 如果公共 IP 地址未分配给资源，该资源仍可与 Internet 进行出站通信，但 Azure 会动态分配不专用于该资源的可用 IP 地址。 

以下示例 Ansible playbook 部分创建名为 `myPublicIP` 的公共 IP 地址：

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>创建网络安全组
可以使用[网络安全组](/azure/virtual-network/security-overview)来筛选 Azure 虚拟网络中出入 Azure 资源的网络流量。 网络安全组包含安全规则，这些规则可允许或拒绝多种 Azure 资源的入站和出站网络流量。 

以下示例 Ansible playbook 部分创建名为 `myNetworkSecurityGroup` 的网络安全组并定义允许 TCP 端口 22 上的 SSH 流量的规则：

```yaml
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
```


## <a name="create-a-virtual-network-interface-card"></a>创建虚拟网络接口卡
虚拟网络接口卡将虚拟机连接到规定的虚拟网络、公共 IP 地址和网络安全组。 

示例 Ansible playbook 部分中的以下部分创建名为 `myNIC` 的虚拟网络接口卡，该卡连接到已创建的虚拟网络资源：

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

## <a name="create-a-virtual-machine"></a>创建虚拟机
最后一步是创建虚拟机，该虚拟机使用在本文的前述部分创建的所有资源。 

在此部分提供的示例 Ansible playbook 部分创建名为 `myVM` 的虚拟机，并附加名为 `myNIC` 的虚拟网络接口卡。 将 &lt;your-key-data> 占位符替换为你自己的完整公钥数据。

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
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>完整的示例 Ansible playbook

此部分列出在本文中从头至尾生成的整个示例 Ansible playbook。 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
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
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
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
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>运行示例 Ansible playbook

此部分详述如何运行在本文中提供的示例 Ansible playbook。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 打开 [Cloud Shell](/azure/cloud-shell/overview)。

1. 创建名为 `azure_create_complete_vm.yml` 的文件（用于包含 playbook）并在 VI 编辑器中将其打开，如下所示：

  ```azurecli-interactive
  vi azure_create_complete_vm.yml
  ```

1. 按 **I** 键进入插入模式。

1. 将[完整的示例 Ansible playbook](#complete-sample-ansible-playbook) 粘贴到编辑器中。

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

1. 运行示例 Ansible playbook。

  ```bash
  ansible-playbook azure_create_complete_vm.yml
  ```

1. 输出如下所示，其中可以看到虚拟机已成功创建：

  ```bash
  PLAY [Create Azure VM] ****************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Create resource group] *********************************************
  changed: [localhost]

  TASK [Create virtual network] *********************************************
  changed: [localhost]

  TASK [Add subnet] *********************************************************
  changed: [localhost]

  TASK [Create public IP address] *******************************************
  changed: [localhost]

  TASK [Dump public IP for VM which will be created] ********************************************************************
  ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
  }

  TASK [Create Network Security Group that allows SSH] **********************
  changed: [localhost]

  TASK [Create virtual network inteface card] *******************************
  changed: [localhost]

  TASK [Create VM] **********************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0
  ```

1. SSH 命令用于访问 Linux VM。 将 &lt;ip-address> 占位符替换为上一步骤中的 IP 地址。

  ```bash
  ssh azureuser@<ip-address>
  ```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [使用 Ansible 在 Azure 中管理 Linux 虚拟机](./ansible-manage-linux-vm.md)
