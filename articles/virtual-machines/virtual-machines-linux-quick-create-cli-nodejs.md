---
title: "使用 Azure CLI 创建 Linux VM | Microsoft 文档"
description: "使用用于 NodeJs 的 Azure CLI 在 Azure 上创建 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2c37d5186e169789881a4b29597cb37cd6f7ede9
ms.openlocfilehash: dc5c575873ceb2acc8321b949031276d9a8f8cc8


---
# <a name="create-a-linux-vm-using-the-azure-cli"></a>Create a Linux VM using the Azure CLI（使用 Azure CLI 创建 Linux VM）

本文说明如何使用 Azure 命令行接口 (LI ) 中的 `azure vm quick-create` 命令在 Azure 上快速部署 Linux 虚拟机 (VM)。 `quick-create` 命令可将 VM 部署在基本且安全的基础结构内，可让你快速构建原型或测试概念。

> [!NOTE]
若要使用 Azure CLI 2.0（预览版）创建 VM，请参阅[使用 Azure CLI 创建 VM](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

本文需要以下条件：

- [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 公钥和私钥文件](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


你也可以使用 [Azure 门户](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)快速部署 Linux VM。

## <a name="quick-commands"></a>快速命令

以下示例演示如何部署 CoreOS VM 并附加安全外壳 (SSH) 密钥（参数可能会不同）：

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>详细演练

以下演练一步步地部署 UbuntuLTS VM，并说明每步的具体操作。

## <a name="vm-quick-create-aliases"></a>VM quick-create 别名

选择分发的便捷方法是使用映射到最常见 OS 分发的 Azure CLI 别名。 下表列出了别名（截止到 Azure 0.10 版）。 使用 `quick-create` 的所有部署默认为部署到由固态硬盘 (SSD) 存储提供支持的 VM，这些 VM 提供更快的预配性能和高性能磁盘访问。 （这些别名表示 Azure 上的一小部分可用分发。 在 Azure 应用商店中查找更多映像（可以[在 PowerShell 中搜索映像](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，或者[在 Web](https://azure.microsoft.com/marketplace/virtual-machines/) 上搜索），或者[上载自己的自定义映像](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。）

| 别名 | 发布者 | 产品 | SKU | 版本 |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7.2 |最新 |
| CoreOS |CoreOS |CoreOS |Stable |最新 |
| Debian |credativ |Debian |8 |最新 |
| openSUSE |SUSE |openSUSE |13.2 |最新 |
| RHEL |Red Hat |RHEL |7.2 |最新 |
| UbuntuLTS |Canonical |Ubuntu Server |14.04.4-LTS |最新 |

以下各节对 **ImageURN** 选项 (`-Q`) 使用 `UbuntuLTS` 别名来部署 Ubuntu 14.04.4 LTS Server。

上一个 `quick-create` 示例在禁用 SSH 密码时仅调出 `-M` 标志来标识要上载的 SSH 公钥，因此系统将提示输入以下参数：

* 资源组名称（任何字符串通常都适用于第一个 Azure 资源组）
* VM 名称
* 位置（`westus` 或 `westeurope` 都是很好的默认值）
* linux（让 Azure 知道你需要的 OS）
* username

以下示例会指定所有值，因此不会再有进一步的提示。 只要具有 `~/.ssh/id_rsa.pub` 作为 ssh rsa 格式公钥文件，它会如期运行：

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

输出应类似于以下输出块：

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>登录到新的 VM
使用在输出中列出的公共 IP 地址登录到 VM。 也可以使用列出的完全限定域名 (FQDN)：

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

登录过程应如以下输出块所示：

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>后续步骤
`azure vm quick-create` 命令是一种快速部署 VM 的方法，让你可以登录到 bash shell 并开始使用。 但是，使用 `vm quick-create` 不会提供进一步的控制，也不能使你创建更复杂的环境。  若要部署针对你的基础结构自定义的 Linux VM，可以遵循以下任一文章操作：

* [使用 Azure Resource Manager 模板创建特定部署](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建受 SSH 保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

还可以[将 `docker-machine` Azure 驱动程序与各种命令配合使用，快速创建 Linux VM 作为 docker 主机](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。



<!--HONumber=Dec16_HO3-->


