---
title: 教程 - 使用 Ansible 从 Azure 共享映像库创建 VM 或虚拟机规模集
description: 了解如何使用 Ansible，以便根据共享映像库中的通用化映像创建 VM 或虚拟机规模集。
keywords: ansible, azure, devops, bash, playbook, 虚拟机, 虚拟机规模集, 共享映像库
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155838"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>教程：使用 Ansible 从 Azure 共享映像库创建 VM 或虚拟机规模集

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[共享映像库](/azure/virtual-machines/windows/shared-image-galleries)是一项服务，你可以通过它轻松地管理、共享和组织自定义托管映像。 此功能适用于保留和共享许多映像的方案。 自定义映像可以跨订阅共享，也可以在 Azure Active Directory 租户之间共享。 映像还可以复制到多个区域，以便更快地进行部署缩放。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 创建通用化 VM 和自定义映像
> * 创建共享映像库
> * 创建共享映像和映像版本
> * 使用通用化映像创建 VM
> * 使用通用化映像创建虚拟机规模集
> * 获取有关共享映像库、映像和版本的信息。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>获取示例 playbook

可通过两种方法获取完整的示例 playbook 集：

- [下载 SIG 文件夹](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image)，将其保存到本地计算机。
- 为每个部分创建一个新文件，将示例 playbook 复制到其中。

`vars.yml` 文件包含的变量供本教程的所有示例 playbook 使用。 可以编辑此文件，以便提供唯一名称和值。

第一个示例 playbook `00-prerequisites.yml` 创建完成本教程所需的内容：
- 一个资源组，它是一个逻辑容器，可在其中部署和管理 Azure 资源。
- 虚拟网络、子网、公共 IP 地址以及用于 VM 的网络接口卡。
- 一个源虚拟机，用于创建通用化映像。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 00-prerequisites.yml
```

在 [Azure 门户](https://portal.azure.com)中检查在 `vars.yml` 中指定的资源组，查看创建的新虚拟机和各种资源。

## <a name="generalize-the-vm-and-create-a-custom-image"></a>通用化 VM 并创建自定义映像

随后的 playbook `01a-create-generalized-image.yml` 可将上一步创建的源 VM 通用化，然后创建基于它的自定义映像。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 01a-create-generalized-image.yml
```

检查资源组，确保 `testimagea` 显示。

## <a name="create-the-shared-image-gallery"></a>创建共享映像库

映像库是用于共享和托管映像的存储库。 `02-create-shared-image-gallery.yml` 中的示例 playbook 代码在资源组中创建一个共享映像库。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

现在会在资源组中看到新库 `myGallery`。

## <a name="create-a-shared-image-and-image-version"></a>创建共享映像和映像版本

下一 playbook `03a-create-shared-image-generalized.yml` 创建映像定义和映像版本。

映像定义包括映像类型（Windows 或 Linux）、发行说明以及最低和最高内存要求。 映像版本是映像的版本。 库、映像定义和映像版本用于在逻辑组中组织映像。 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

资源组现在有用于库的映像定义和映像版本。

## <a name="create-a-vm-based-on-the-generalized-image"></a>根据通用化映像创建 VM

最后请运行 `04a-create-vm-using-generalized-image.yml`，根据你在上一步创建的通用化映像创建 VM。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>根据通用化映像创建虚拟机规模集

也可根据通用化映像创建虚拟机规模集。 为此，请运行 `05a-create-vmss-using-generalized-image.yml`。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>获取有关库的信息

可以通过运行 `06-get-info.yml` 获取有关库、映像定义和版本的信息。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>删除共享映像

若要删除库资源，请参阅示例 playbook `07-delete-gallery.yml`。 按相反顺序删除资源。 从删除映像版本开始。 删除所有映像版本以后，即可删除映像定义。 删除所有映像定义以后，即可删除库。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。 

本部分的示例 playbook 代码用于：

- 删除前面创建的两个资源组

将以下 playbook 保存为 `cleanup.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

下面是使用示例 playbook 时要考虑的部分关键注意事项：

- 将 `{{ resource_group_name }}` 占位符替换为资源组的名称。
- 两个指定资源组中的所有资源将被删除。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](/azure/ansible/)