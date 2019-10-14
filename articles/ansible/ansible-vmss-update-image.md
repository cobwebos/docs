---
title: 教程 - 使用 Ansible 更新 Azure 虚拟机规模集的自定义映像
description: 了解如何在 Azure 中使用 Ansible 更新虚拟机规模集的自定义映像
keywords: ansible, azure, devops, bash, playbook, 虚拟机, 虚拟机规模集, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 3b7baffe6ce0fadbac2dd56b9c8296c80546fa72
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241330"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>教程：使用 Ansible 更新 Azure 虚拟机规模集的自定义映像

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

部署 VM 后，可以使用应用所需的软件配置 VM。 可创建自定义映像，而不是为每个 VM 执行此配置任务。 自定义映像是包含任何已安装软件的现有 VM 快照。 在[配置规模集](./ansible-create-configure-vmss.md)时，可以指定要用于该规模集的 VM 映像。 通过使用自定义映像，每个 VM 实例都为应用进行相同的配置。 有时，可能需要更新规模集的自定义映像。 该任务是本教程的重点。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 使用 HTTPD 配置两个 VM
> * 从现有 VM 创建自定义映像
> * 从映像创建规模集
> * 更新自定义映像

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>配置两个 VM

本部分中的 playbook 代码创建两个虚拟机并在这两个虚拟机上都安装了 HTTPD。 

每个 VM 的 `index.html` 页面显示一个测试字符串：

* 第一个 VM 显示值 `Image A`
* 第二个 VM 显示值 `Image B`

此字符串用于模拟使用不同的软件配置每个 VM。

可通过两种方式获取示例 playbook：

* [下载 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) 并将其保存到 `create_vms.yml`。
* 新建名为 `create_vms.yml` 的文件，并将以下内容复制到其中：

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

使用 `ansible-playbook` 命令运行 playbook，用资源组名称替换 `myrg`：

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

由于 playbook 的 `debug` 部分，`ansible-playbook` 命令将打印每个 VM 的 IP 地址。 请复制这些 IP 地址供稍后使用。

![虚拟机 IP 地址](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>连接到两个 VM

在本部分中，你将连接到每个 VM。 如前一部分所述，字符串 `Image A` 和 `Image B` 模拟具有不同配置的两个不同 VM。

使用上一节中的 IP 地址，连接到两个 VM：

![虚拟机 A 的屏幕截图](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![虚拟机 B 的屏幕截图](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>从每个 VM 创建映像

此时，你有两个 VM，其配置（它们的 `index.html` 文件）略有不同。

本部分中的 playbook 代码为每个 VM 创建自定义映像：

* `image_vmforimageA` - 为在其主页上显示 `Image A` 的 VM 创建的自定义映像。
* `image_vmforimageB` - 为在其主页上显示 `Image B` 的 VM 创建的自定义映像。

可通过两种方式获取示例 playbook：

* [下载 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) 并将其保存到 `capture-images.yml`。
* 新建名为 `capture-images.yml` 的文件，并将以下内容复制到其中：

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

使用 `ansible-playbook` 命令运行 playbook，用资源组名称替换 `myrg`：

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>使用映像 A 创建规模集

在本部分中，使用了 playbook 配置以下 Azure 资源：

* 公共 IP 地址
* 负载均衡
* 规模集引用了 `image_vmforimageA`

可通过两种方式获取示例 playbook：

* [下载 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) 并将其保存到 `create-vmss.yml`。
* 创建名为 `create-vmss.yml` 的新文件，并将以下内容复制到其中：

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

使用 `ansible-playbook` 命令运行 playbook，用资源组名称替换 `myrg`：

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

由于 playbook 的 `debug` 部分，`ansible-playbook` 命令将打印规模集的 IP 地址。 请复制此 IP 地址供稍后使用。

![公共 IP 地址](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>连接到规模集

在本部分中，你将连接到规模集。 

使用上一节中的 IP 地址，连接到规模集。

如前一部分所述，字符串 `Image A` 和 `Image B` 模拟具有不同配置的两个不同 VM。

规模集引用名为 `image_vmforimageA` 的自定义映像。 自定义映像 `image_vmforimageA` 是从主页显示 `Image A` 的 VM 中创建的。

因此，你将看到主页上显示 `Image A`：

![规模集与第一个 VM 相关联。](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

在继续进行下一部分时，请保持浏览器窗口处于打开状态。

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>更改规模集和升级实例中的自定义映像

本部分中的 playbook 代码会更改规模集的映像 - 从 `image_vmforimageA` 更改为 `image_vmforimageB`。 此外，还会更新由规模集部署的所有当前虚拟机。

可通过两种方式获取示例 playbook：

* [下载 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) 并将其保存到 `update-vmss-image.yml`。
* 新建名为 `update-vmss-image.yml` 的文件，并将以下内容复制到其中：

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

使用 `ansible-playbook` 命令运行 playbook，用资源组名称替换 `myrg`：

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

返回到浏览器并刷新页面。 

可看到虚拟机的基础自定义映像已更新。

![规模集与第二个 VM 相关联](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。 

将以下代码保存为 `cleanup.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](/azure/ansible)