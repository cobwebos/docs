---
title: "使用 Azure CLI 2.0 创建 Linux VM | Microsoft Azure"
description: "使用 Azure CLI 2.0 创建 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建 Linux VM
本文说明如何在 Azure CLI 2.0 中使用 [az vm create](/cli/azure/vm#create) 命令在 Azure 上快速部署 Linux 虚拟机 (VM)，使用托管磁盘以及本机存储帐户中的磁盘。 还可以使用 [Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。

若要创建 VM，需要： 

* 一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）
* 安装 [Azure CLI 2.0](/cli/azure/install-az-cli2)
* 登录到 Azure 帐户（键入 [az login](/cli/azure/#login)）

（也可使用 [Azure 门户](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)部署 Linux VM。）

以下示例演示如何部署 Debian VM 并使用安全外壳 (SSH) 密钥与其进行连接。 你的参数可能有所不同；如果需要其他映像，[可搜索一个](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="using-managed-disks"></a>使用托管磁盘

若要使用 Azure 托管磁盘，必须使用支持它们的区域。 首先，键入 [az group create](/cli/azure/group#create) 创建包含所有已部署资源的资源组：

```azurecli
 az group create -n myResourceGroup -l westus
```

输出如下所示（如果希望看到其他格式，可以指定一个不同的 `--output` 选项）：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>创建 VM 
现在，可以创建 VM 及其环境。 请记得将 `--public-ip-address-dns-name` 值替换为唯一值；下面的值可能已被使用。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


输出如下所示。 请注意通过 **ssh** 连接到 VM 时使用的 `publicIpAddress` 或 `fqdn` 值。


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

使用输出中列出的公共 IP 地址或完全限定域名 (FQDN) 登录到 VM。

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

根据所选的分发版，应会显示类似于下面的输出：

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

请参阅[后续步骤](#next-steps)，了解可以使用托管磁盘针对新 VM 执行的其他事项。

## <a name="using-unmanaged-disks"></a>使用非托管磁盘 

使用非托管存储磁盘的虚拟机具有非托管存储帐户。 首先，键入 [az group create](/cli/azure/group#create) 创建资源组来包含所有已部署资源：

```azurecli
az group create --name nativedisks --location westus
```

输出如下所示（如果需要，可以选择一个不同的 `--output` 选项）：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>创建 VM 

现在，可以创建 VM 及其环境。 使用 `--use-unmanaged-disk` 标志来创建具有非托管磁盘的 VM。 还将创建一个非托管存储帐户。 请记得将 `--public-ip-address-dns-name` 值替换为唯一值；下面的值可能已被使用。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

输出如下所示。 请注意通过 **ssh** 连接到 VM 时使用的 `publicIpAddress` 或 `fqdn` 值。

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

使用上述输出中列出的公共 IP 地址或完全限定域名 (FQDN) 登录到 VM。

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

根据所选的分发版，应会显示类似于下面的输出：

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>后续步骤
`az vm create` 命令是一种快速部署 VM 的方法，让你可以登录到 bash shell 并开始使用。 但是，使用 `az vm create` 不会提供进一步的控制，也不能使你创建更复杂的环境。  若要部署针对你的基础结构自定义的 Linux VM，可以遵循以下任一文章操作：

* [使用 Azure Resource Manager 模板创建特定部署](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建受 SSH 保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

还可以[将 `docker-machine` Azure 驱动程序与各种命令配合使用来快速创建 Linux VM 作为 docker 主机](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)；如果使用 Java，请尝试 [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) 方法。


