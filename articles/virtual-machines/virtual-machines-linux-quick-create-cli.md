---
title: "使用 Azure CLI 2.0（预览版）创建 Linux VM | Microsoft Azure"
description: "使用 Azure CLI 2.0（预览版）创建 Linux VM。"
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>使用 Azure CLI 2.0 预览版创建 Linux VM (az.py)
本文说明如何在 Azure CLI 2.0（预览版）中使用 [az vm create](/cli/azure/vm#create) 命令在 Azure 上快速部署 Linux 虚拟机 (VM)。 

> [!NOTE] 
> Azure CLI 2.0 预览版是下一代的多平台 CLI。 [试用。](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> 剩余的文档使用现有 Azure CLI。 若要使用现有 Azure CLI 1.0 而不是 CLI 2.0 预览版创建 VM，请参阅 [Create a VM with the Azure CLI](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（使用 Azure CLI 创建 VM）。

若要创建 VM，需要： 

* 一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）
* 已安装 [Azure CLI v.2.0（预览版）](/cli/azure/install-az-cli2)
* 登录到 Azure 帐户（键入 [az login](/cli/azure/#login)）

（也可以使用 [Azure 门户](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)快速部署 Linux VM。）

以下示例演示如何部署 Debian VM 和附加安全外壳 (SSH) 密钥（你的参数可能与此不同；如果需要不同的映像，[可以搜索映像](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。

## <a name="create-a-resource-group"></a>创建资源组

首先，键入 [az group create](/cli/azure/group#create) 创建包含所有已部署资源的资源组：

```azurecli
az group create -n myResourceGroup -l westus
```

输出如下所示（如果需要，可以选择一个不同的 `--output` 选项）：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>使用最新的 Debian 映像创建 VM

现在，可以创建 VM 及其环境。 请记得将 `----public-ip-address-dns-name` 值替换为唯一值；下面的值可能已被使用。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


输出如下所示。 请注意通过 **ssh** 连接到 VM 时使用的 `publicIpAddress` 或 `fqdn` 值。


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

使用在输出中列出的公共 IP 地址登录到 VM。 你也可以使用列出的完全限定域名称 (FQDN)。

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

根据所选的分发版，应会显示类似于下面的输出：

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>后续步骤
`az vm create` 命令是一种快速部署 VM 的方法，让你可以登录到 bash shell 并开始使用。 但是，使用 `az vm create` 不会提供进一步的控制，也不能使你创建更复杂的环境。  若要部署针对你的基础结构自定义的 Linux VM，可以遵循以下任一文章操作：

* [使用 Azure Resource Manager 模板创建特定部署](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建受 SSH 保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

还可以[将 `docker-machine` Azure 驱动程序与各种命令配合使用来快速创建 Linux VM 作为 docker 主机](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)；如果使用 Java，请尝试 [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) 方法。




<!--HONumber=Jan17_HO1-->


