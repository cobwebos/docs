---
title: 在 Azure 中使用 Ansible 创建虚拟机规模集
description: 了解如何在 Azure 中使用 Ansible 创建和配置虚拟机规模集
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, 虚拟机, 虚拟机规模集, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 5f915f7b1b425a3bd6e5d62eb70bb3f633b7eda8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011705"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>在 Azure 中使用 Ansible 创建虚拟机规模集
使用 Ansible 可以在环境中自动部署和配置资源。 可以在 Azure 中使用 Ansible 管理虚拟机规模集 (VMSS)，就像管理任何其他 Azure 资源一样。 本文介绍如何使用 Ansible 创建和横向扩展虚拟机规模集。 

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- **配置 Ansible** - [创建 Azure 凭据并配置 Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible 和 Azure Python SDK 模块** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.6。 

## <a name="create-a-vmss"></a>创建 VMSS
本部分提供一个示例 Ansible playbook，它定义了以下资源：
- 所有资源将部署到的**资源组**
- 10.0.0.0/16 地址空间中的**虚拟网络**
- 虚拟网络中的**子网**
- 用于通过 Internet 访问资源的**公共 IP 地址**
- **网络安全组**，控制传入和传出虚拟机规模集的网络流量
- **负载均衡器**，使用负载均衡器规则将流量分配到一组定义的 VM
- 使用所有已创建资源的**虚拟机规模集**

输入自己的密码作为 *admin_password* 值。

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

若要使用 Ansible 创建虚拟机规模集环境，请将前面的 playbook 保存为 `vmss-create.yml`，或者[下载示例 Ansible playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml)。

若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

  ```bash
  ansible-playbook vmss-create.yml
  ```

运行 playbook 后，类似于以下示例的输出显示已成功创建虚拟机规模集：

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>横向扩展 VMSS
创建的虚拟机规模集有两个实例。 如果在 Azure 门户中导航到虚拟机规模集，则会看到“Standard_DS1_v2 (2 个实例)”。 还可以通过在 Cloud Shell 中运行以下命令来使用 [Azure Cloud Shell](https://shell.azure.com/)：

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

输出应如下所示：

  ```bash
  {
    "capacity": 2,
  }
  ```

现在，让我们从两个实例扩展到三个实例。 以下 Ansible playbook 代码检索有关虚拟机规模的信息，并将其容量从 2 更改为 3。 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

若要横向扩展已创建的虚拟机规模集，请将前面的 playbook 保存为 `vmss-scale-out.yml`，或者[下载示例 Ansible playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)。 

以下命令将运行 playbook：

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

运行 Ansible playbook 的输出显示虚拟机规模集已成功横向扩展：

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

如果在 Azure 门户中导航到配置的虚拟机规模集，则会看到“Standard_DS1_v2 (3 个实例)”。 还可以通过运行以下命令，使用 [Azure Cloud Shell](https://shell.azure.com/) 验证更改：

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

在Cloud Shell 中运行命令的结果显示现在存在三个实例。 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [适用于 VMSS 的 Ansible 示例 playbook](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)