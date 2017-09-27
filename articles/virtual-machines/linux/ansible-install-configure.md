---
title: "安装和配置适用于 Azure 虚拟机的 Ansible | Microsoft Docs"
description: "了解如何在 Ubuntu、CentOS 和 SLES 上安装和配置 Ansible 以管理 Azure 资源"
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
ms.openlocfilehash: 52b763274437961dccfc862c8a45fbd57ea9fc4e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017

---

# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>安装和配置 Ansible 以管理 Azure 中的虚拟机
本文详细介绍了如何为某些最常见的 Linux 发行版安装 Ansible 和所需的 Azure Python SDK 模块。 通过调整安装的程序包以适应特定的平台，在其他发行版上安装 Ansible。 若要安全地创建 Azure 资源，还需了解如何创建和定义用于 Ansible 的凭据。 

有关其他平台的安装方法和步骤的详细信息，请参阅[Ansible 安装指南](https://docs.ansible.com/ansible/intro_installation.html)。


## <a name="install-ansible"></a>安装 Ansible
首先，使用 [az group create](/cli/azure/group#create) 创建资源组。 以下示例在 "eastus" 位置创建名为 "myResourceGroupAnsible" 的资源组：

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

现在创建 VM 并安装适用于以下发行版之一的 Ansible：

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12.2 SP2](#sles-122-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
使用 [az vm create](/cli/azure/vm#create) 创建 VM。 以下示例创建一个名为 "myVMAnsible" 的 VM：

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 创建操作输出中记下的 `publicIpAddress` 通过 SSH 连接到 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在 VM 上，请按以下方法为 Azure Python SDK 模块和 Ansible 安装所需的程序包：

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

现在转到[创建 Azure 凭据](#create-azure-credentials)。


### <a name="centos-73"></a>CentOS 7.3
使用 [az vm create](/cli/azure/vm#create) 创建 VM。 以下示例创建一个名为 "myVMAnsible" 的 VM：

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 创建操作输出中记下的 `publicIpAddress` 通过 SSH 连接到 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在 VM 上，请按以下方法为 Azure Python SDK 模块和 Ansible 安装所需的程序包：

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

现在转到[创建 Azure 凭据](#create-azure-credentials)。


### <a name="sles-122-sp2"></a>SLES 12.2 SP2
使用 [az vm create](/cli/azure/vm#create) 创建 VM。 以下示例创建一个名为 "myVMAnsible" 的 VM：

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 创建操作输出中记下的 `publicIpAddress` 通过 SSH 连接到 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在 VM 上，请按以下方法为 Azure Python SDK 模块和 Ansible 安装所需的程序包：

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

现在转到[创建 Azure 凭据](#create-azure-credentials)。


## <a name="create-azure-credentials"></a>创建 Azure 凭据
Ansible 使用用户名和密码或服务主体来与 Azure 通信。 Azure 服务主体是可用于应用、服务和 Ansible 等自动化工具的安全标识。 由你控制和定义有关服务主体可以在 Azure 中执行哪些操作的权限。 为了提供比使用用户名和密码更高的安全性，本示例将创建一个基本的服务主体。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 创建服务主体并输出 Ansible 所需的凭据：

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

前述命令中的输出示例如下所示：

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

若要向 Azure 进行身份验证，还需使用 [az account show](/cli/azure/account#show) 获取 Azure 订阅 ID：

```azurecli
az account show --query [id] --output tsv
```

在下一步中使用这两个命令的输出。


## <a name="create-ansible-credentials-file"></a>创建 Ansible 凭据文件
若要向 Ansible 提供凭据，需定义环境变量，或创建本地凭据文件。 有关如何定义 Ansible 凭据的详细信息，请参阅[为 Azure 模块提供凭据](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)。 

对于开发环境，请按以下步骤在主机 VM 上创建 Ansible 的凭据文件：

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

凭据文件本身会将订阅 ID 与创建服务主体的输出相结合。 以前的 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 命令的输出顺序与 client_id、secret和 tenant 需要的顺序相同。 以下示例凭据文件显示了与以前的输出相匹配的这些值。 按如下所示输入自己的值：

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>使用 Ansible 环境变量
如果要使用 Ansible Tower 或 Jenkins 等工具，可以按以下步骤定义环境变量。 这些变量会将订阅 ID 与创建服务主体的输出相结合。 以前的 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 命令的输出顺序与 AZURE_CLIENT_ID、AZURE_SECRET 和 AZURE_TENANT 需要的顺序相同。 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>后续步骤
现在，Ansible 和所需的 Azure Python SDK 模块均已安装，用于 Ansible 的凭据也已定义。 了解如何[使用 Ansible 创建 VM](ansible-create-vm.md)。 还可以了解如何[通过 Ansible 创建完整的 Azure VM 和支持资源](ansible-create-complete-vm.md)。
