---
title: 安装和配置适用于 Azure 虚拟机的 Ansible | Microsoft Docs
description: 了解如何在 Ubuntu、CentOS 和 SLES 上安装和配置 Ansible 以管理 Azure 资源
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: cynthn
ms.openlocfilehash: e7d57ead2caff87db07380582b9085b831844f1e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930063"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>安装和配置 Ansible 以管理 Azure 中的虚拟机

使用 Ansible 可以在环境中自动部署和配置资源。 可以在 Azure 中使用 Ansible 管理虚拟机 (VM)，管理其他任意资源也一样。 本文详细介绍如何为某些最常见的 Linux 发行版安装 Ansible 和所需的 Azure Python SDK 模块。 通过调整安装的程序包以适应特定的平台，在其他发行版上安装 Ansible。 若要安全地创建 Azure 资源，还需了解如何创建和定义用于 Ansible 的凭据。

有关其他平台的安装方法和步骤的详细信息，请参阅[Ansible 安装指南](https://docs.ansible.com/ansible/intro_installation.html)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="install-ansible"></a>安装 Ansible

Azure Cloud Shell 是一种基于浏览器的 shell 体验，用于管理和开发 Azure 资源，借助它可轻松地将 Ansible 与 Azure 一起使用，是实现二者结合的最简单方式之一。 Ansible 已预装在 Cloud Shell 中，因此可以跳过有关如何安装 Ansible 的说明并转到[创建 Azure 凭据](#create-azure-credentials)。 有关 Cloud Shell 中其他可用工具的列表，请参阅 [Azure Cloud Shell 中适用于 Bash 的功能和工具](../../cloud-shell/features.md#tools)。

以下说明演示如何创建适用于各种发行版的 Linux VM，然后安装 Ansible。 如果无需创建 Linux VM，请跳过第一步（创建 Azure 资源组）。 如果需要创建 VM，请先通过 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

现在，根据需要为创建 VM 的步骤选择以下一种发行版，然后安装 Ansible：

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

如有需要，请使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例创建一个名为 "myVMAnsible" 的 VM：

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
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

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

如有需要，请使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例创建一个名为 "myVMAnsible" 的 VM：

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
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
sudo pip install ansible[azure]
```

现在转到[创建 Azure 凭据](#create-azure-credentials)。

### <a name="sles-12-sp2"></a>SLES 12 SP2

如有需要，请使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例创建一个名为 "myVMAnsible" 的 VM：

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
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
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

现在转到[创建 Azure 凭据](#create-azure-credentials)。

## <a name="create-azure-credentials"></a>创建 Azure 凭据

Ansible 使用用户名和密码或服务主体来与 Azure 通信。 Azure 服务主体是可用于应用、服务和 Ansible 等自动化工具的安全标识。 由你控制和定义有关服务主体可以在 Azure 中执行哪些操作的权限。 为了提供比使用用户名和密码更高的安全性，本示例将创建一个基本的服务主体。

在主机计算机上或在 Azure Cloud Shell 中，使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 创建服务主体。 Ansible 所需凭据将输出到屏幕上：

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

前述命令的输出示例如下所示：

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

若要向 Azure 进行身份验证，还需使用 [az account show](/cli/azure/account#az-account-show) 获取 Azure 订阅 ID：

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

在下一步中使用这两个命令的输出。

## <a name="create-ansible-credentials-file"></a>创建 Ansible 凭据文件

若要向 Ansible 提供凭据，需定义环境变量，或创建本地凭据文件。 有关如何定义 Ansible 凭据的详细信息，请参阅[为 Azure 模块提供凭据](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)。

对于开发环境，请在主机 VM 上创建 Ansible 的凭据文件。 在上一步安装 Ansible 的 VM 上创建凭证文件：

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

凭据文件本身会将订阅 ID 与创建服务主体的输出相结合。 前面的 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 命令的输出与 client_id、secret和 tenant 所需的值相同。 以下示例凭据文件显示了与以前的输出相匹配的这些值。 按如下所示输入自己的值：

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

保存并关闭该文件。

## <a name="use-ansible-environment-variables"></a>使用 Ansible 环境变量

如果要使用 Ansible Tower 或 Jenkins 等工具，需要定义环境变量。 如果仅使用 Ansible 客户端和在上一步骤中创建的 Azure 凭据文件，则可跳过此步骤。 环境变量与 Azure 凭据文件定义的信息相同：

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>后续步骤

现在，Ansible 和所需的 Azure Python SDK 模块均已安装，用于 Ansible 的凭据也已定义。 了解如何[使用 Ansible 创建 VM](ansible-create-vm.md)。 还可以了解如何[通过 Ansible 创建完整的 Azure VM 和支持资源](ansible-create-complete-vm.md)。
