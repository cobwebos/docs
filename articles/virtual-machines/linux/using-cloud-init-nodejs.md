---
title: "在创建期间使用 cloud-init 在 Azure 中自定义 Linux VM | Microsoft 文档"
description: "如何通过 Azure CLI 1.0 使用 cloud-init 在创建期间自定义 Linux VM"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0b6150bca333188666935b3c9aa02c4b33690db9
ms.contentlocale: zh-cn
ms.lasthandoff: 04/03/2017

---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>通过 Azure CLI 1.0 使用 cloud-init 在创建期间自定义 Linux VM
本文说明如何制作 cloud-init 脚本来设置主机名、更新已安装的包及管理用户帐户。  在 VM 创建期间可以从 Azure CLI 调用 cloud-init 脚本。  本文需要以下条件：

* 一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）。
* 已使用 `azure login` 登录 [Azure CLI](../../cli-install-nodejs.md)。
* Azure CLI *必须处于*Azure Resource Manager 模式`azure config mode arm`。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#quick-commands) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于资源管理部署模型的下一代 CLI

## <a name="quick-commands"></a>快速命令
创建 cloud-init.txt 脚本，用于设置主机名、更新所有包，并将 sudo 用户添加到 Linux。

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
创建要在其中启动 VM 的资源组。

```azurecli
azure group create myResourceGroup westus
```

使用 cloud-init 创建要在启动过程中进行配置的 Linux VM。

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>详细演练
### <a name="introduction"></a>介绍
启动新 Linux VM 时，将获得一个未经过任何自定义或者不能够现成地满足需求的标准 Linux VM。 [Cloud-init](https://cloudinit.readthedocs.org) 是在首次启动 Linux VM 时在其中注入脚本或配置设置的标准方法。

Azure 有三种不同的方法可在部署或启动 Linux VM 时对其进行更改。

* 使用 cloud-init 注入脚本。
* 使用 Azure [VMAccess 扩展](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)注入脚本。
* 使用 cloud-init 的 Azure 模板。
* 使用 [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 的 Azure 模板。

若要在启动后随时注入脚本，请执行以下操作：

* 直接通过 SSH 运行命令
* 以命令方式或在 Azure 模板中使用 Azure [VMAccess 扩展](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)注入脚本
* Ansible、Salt、Chef 和 Puppet 等配置管理工具。

> [!NOTE]
> : VMAccess 扩展以使用 SSH 可以进行的相同方式以 root 身份执行脚本。  但是，使用 VM 扩展可以启用 Azure 提供的几项功能，这些功能可以很有用，具体取决于用户的方案。
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Azure VM 上的 Cloud-init 可用性快速创建映像别名：
| 别名 | 发布者 | 产品 | SKU | 版本 | cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |否 |
| CoreOS |CoreOS |CoreOS |Stable |最新 |是 |
| Debian |credativ |Debian |8 |最新 |否 |
| openSUSE |SUSE |openSUSE |13.2 |最新 |否 |
| RHEL |Redhat |RHEL |7.2 |最新 |否 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |是 |

Microsoft 正在与合作伙伴协作将 cloud-init 包含在用户向 Azure 提供的映像中并让它在其中正常工作。

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>将 cloud-init 脚本添加使用 Azure CLI 创建 VM 的操作中
在 Azure 中创建 VM 时，若要启动 cloud-init 脚本，请使用 Azure CLI `--custom-data` 开关来指定 cloud-init 文件。

创建要在其中启动 VM 的资源组。

```azurecli
azure group create myResourceGroup westus
```

使用 cloud-init 创建要在启动过程中进行配置的 Linux VM。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>创建 cloud-init 脚本以设置 Linux VM 的主机名
对任何 Linux VM 而言，其中一个最简单且最重要的设置就是主机名。 使用 cloud-init 和此脚本就可以轻松设置此项。  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>名为 `cloud_config_hostname.txt` 的示例 cloud-init 脚本。
```sh
#cloud-config
hostname: myservername
```

初始启动 VM 期间，此 cloud-init 脚本将主机名设置为 `myservername`。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

登录并验证新 VM 的主机名。

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>创建 cloud-init 脚本以更新 Linux
为了安全，用户希望 Ubuntu VM 在首次启动时更新。  根据所用的 Linux 分发版，我们可以使用 cloud-init 和以下脚本执行此操作。

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>适用于 Debian 系列的示例 cloud-init 脚本 `cloud_config_apt_upgrade.txt`
```sh
#cloud-config
apt_upgrade: true
```

启动 Linux 后，所有已安装的包将通过 `apt-get` 进行更新。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

登录并验证所有包是否都已更新。

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>创建 cloud-init 脚本以将用户添加到 Linux
任何新 Linux VM 的首要任务之一，就是为自己添加用户或避免使用 `root`。 对于安全性和易用性来说，SSH 密钥是最佳做法，可以使用此 cloud-init 脚本将它们添加到 `~/.ssh/authorized_keys` 文件。

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>适用于 Debian 系列的示例 cloud-init 脚本 `cloud_config_add_users.txt`
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Linux 启动后，所有列出的用户都已创建并添加到 sudo 组。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

登录并验证新建的用户。

```bash
ssh myVM
cat /etc/group
```

输出

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>后续步骤
Cloud-init 正在成为一种在 Linux VM 启动时对其进行修改的标准方法。 Azure 还提供 VM 扩展，使用这些扩展可以在 LinuxVM 启动或运行时对其进行修改。 例如，可以使用 Azure VMAccessExtension 在 VM 运行时重置 SSH 或用户信息。 使用 cloud-init，需要重新启动才能重置密码。

[关于虚拟机扩展和功能](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[管理用户、SSH，并使用 VMAccess 扩展检查或修复 Azure Linux VM 上的磁盘](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


