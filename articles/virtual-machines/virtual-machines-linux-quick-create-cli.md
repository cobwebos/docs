<properties
   pageTitle="使用 CLI 在 Azure 上创建 Linux VM | Microsoft Azure"
   description="使用 CLI 在 Azure 上创建 Linux VM。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/08/2016"
   ms.author="v-livech"/>


# 使用 CLI 在 Azure 上创建 Linux VM

本文说明如何使用 Azure 命令行接口 (LI ) 中的 `azure vm quick-create` 命令在 Azure 上快速部署 Linux 虚拟机 (VM)。`quick-create` 命令可将 VM 部署在基本且安全的基础结构内，可让你快速构建原型或测试概念。在本文中要求

- 一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）

- 使用 `azure login` 登录的 [Azure CLI](../xplat-cli-install.md)。

- Azure CLI _必须处于_ Azure Resource Manager 模式 `azure config mode arm`。

你也可以使用 [Azure 门户](virtual-machines-linux-quick-create-portal.md)快速部署 Linux VM。

## 快速命令

以下示例演示如何部署 CoreOS VM 并附加安全外壳 (SSH) 密钥（参数可能会不同）。

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

以下部分使用 Ubuntu Server 14.04 LTS 作为 Linux 分布，说明命令及其要求。

## VM quick-create 别名

使用映射到最常见的 OS 分布的 Azure CLI 别名，可以快速选择分布。下表列出了别名（从 Azure CLI 0.10 版起）。所有使用 `quick-create` 部署的 VM 默认为受固态驱动器 (SSD) 存储支持，它可提供更快的预配和高性能磁盘访问。（这些别名代表 Azure 上可用分布的一小部分。通过[搜索映像](virtual-machines-linux-cli-ps-findimage.md)以在 Azure 应用商店查找更多映像，或[上载你自己的自定义映像](virtual-machines-linux-create-upload-generic.md)。）

| 别名 | 发布者 | 产品 | SKU | 版本 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | CentOS | 7\.2 | 最新 |
| CoreOS | CoreOS | CoreOS | Stable | 最新 |
| Debian | credativ | Debian | 8 | 最新 |
| openSUSE | SUSE | openSUSE | 13\.2 | 最新 |
| RHEL | Red Hat | RHEL | 7\.2 | 最新 |
| UbuntuLTS | Canonical | Ubuntu Server | 14\.04.4-LTS | 最新 |

以下部分使用 **ImageURN** 选项 (`-Q`) 的 `UbuntuLTS` 别名来部署 Ubuntu 14.04.4 LTS 服务器。

## 详细演练

上一个 `quick-create` 示例仅调出 `-M` 标志标识 SSH 公钥来上载，且同时禁用 SSH 密码，因此系统会提示你输入

- 资源组名称（任何字符串通常都适用于第一个 Azure 资源组）
- VM 名称
- 位置（westus 或 westeurope 是不错的默认值）
- linux（让 Azure 知道你需要的 OS）
- username

以下会指定所有值，因此不会再有进一步的提示。只要具有 `~/.ssh/id_rsa.pub` 作为 ssh rsa 格式公钥文件，它会如期运行。

```bash
azure vm quick-create \
-g exampleResourceGroup \
-n exampleVMName \
-l westus \
-y Linux \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub \
-Q UbuntuLTS
```

输出应类似于以下输出块。

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "exampleVMName"
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
+ Creating VM "exampleVMName"
+ Looking up the VM "exampleVMName"
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
data:      Computer Name                 :exampleVMName
data:      User Name                     :exampleAdminUser
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

使用在输出中列出的公共 IP 地址登录到 VM。你也可以使用列出的完全限定域名称 (FQDN)。

```bash
ssh -i ~/.ssh/id_rsa.pub exampleAdminUser@138.91.247.29
```

登录过程应如下所示：

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

exampleAdminUser@exampleVMName:~$
```

## 后续步骤

`azure vm quick-create` 命令是一种快速部署 VM 的方法，让你可以登录到 bash shell 并开始使用。但是，使用 `vm quick-create` 不会提供进一步的控制，也不能使你创建更复杂的环境。若要部署针对你的基础结构自定义的 Linux VM，可以遵循以下任一文章操作：

- [使用 Azure Resource Manager 模板创建特定部署](virtual-machines-linux-cli-deploy-templates.md)
- [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](virtual-machines-linux-create-cli-complete.md)
- [使用模板在 Azure 上创建受 SSH 保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

还可以[搭配使用 `docker-machine` Azure 驱动程序与各种命令，以快速创建作为 docker 主机的 Linux VM](virtual-machines-linux-docker-machine.md)。

<!---HONumber=AcomDC_0921_2016-->