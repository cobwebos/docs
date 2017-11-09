---
title: "在 Azure 中使用 Ansible 创建基本的 Linux VM | Microsoft Docs"
description: "了解如何在 Azure 中使用 Ansible 创建和管理基本的 Linux 虚拟机"
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
ms.openlocfilehash: 07cb786e3053cb933fb87b2991cebb979a91ace0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>使用 Ansible 在 Azure 中创建基本的虚拟机
使用 Ansible 可以在环境中自动部署和配置资源。 可以在 Azure 中使用 Ansible 管理虚拟机 (VM)，管理其他任意资源也一样。 本文介绍如何使用 Ansible 创建基本的 VM。 还可以了解如何[使用 Ansible 创建完整的 VM 环境](ansible-create-complete-vm.md)。


## <a name="prerequisites"></a>先决条件
若要使用 Ansible 管理 Azure 资源，需要以下条件：

- 安装在主机系统上的 Ansible 和 Azure Python SDK 模块。
    - 在 [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts)、[CentOS 7.3](ansible-install-configure.md#centos-73) 和 [SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2) 上安装 Ansible
- Azure 凭据和配置后使用 Azure 凭据的 Ansible。
    - [创建 Azure 凭据和配置 Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 
    - 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 也可以通过浏览器使用 [Cloud Shell](/azure/cloud-shell/quickstart)。


## <a name="create-supporting-azure-resources"></a>创建支持的 Azure 资源
在此示例中，我们创建一个 runbook 将 VM 部署到现有的基础结构中。 首先，使用 [az group create](/cli/azure/vm#create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#create) 为 VM 创建虚拟网络。 以下示例创建一个名为 *myVnet* 的虚拟网络和一个名为 *mySubnet* 的子网：

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>创建并运行 Ansible 操作手册
创建名为 azure_create_vm.yml 的 Ansible 操作手册并粘贴以下内容。 此示例创建一个单独的 VM 并配置 SSH 凭据。 在 key_data 对中输入如下所示的公钥数据：

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
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
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

若要使用 Ansible 创建 VM，请如下所示运行操作手册：

```bash
ansible-playbook azure_create_vm.yml
```

输出类似于显示已成功创建 VM 的以下示例：

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>后续步骤
此示例在已部署虚拟网络的情况下在现有的资源组中创建 VM。 有关如何使用 Ansible 创建支持资源（例如虚拟网络和网络安全组规则）的详细示例，请参阅[使用 Ansible 创建完整的 VM 环境](ansible-create-complete-vm.md)。