---
title: 重置对 Azure Linux VM 的访问 | Microsoft Docs
description: 如何使用 VMAccess 扩展和 Azure CLI 2.0 在 Linux VM 上管理管理用户和重置访问权限
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: danis
ms.openlocfilehash: c023f226894d2fabb90736513e49a1ecca179d4f
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165785"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>配合使用 VMAccess 扩展和 Azure CLI 2.0 管理管理用户、SSH，并检查或修复 Linux VM 上的磁盘
## <a name="overview"></a>概述
Linux VM 上的磁盘显示错误。 不知道怎样重置 Linux VM 的 root 密码，或者不小心删除了 SSH 私钥。 如果在数据中心的时代发生这种情况，则需要开车到那里，并打开 KVM 访问服务器控制台。 请将 Azure VMAccess 扩展想像成该 KVM 交换机，它允许访问控制台以重置 Linux 访问或执行磁盘级维护。

本文介绍：如何在 Azure VMAccess Extension 作为 Azure Resource Manager 虚拟机运行时，检查或修复磁盘、重置用户访问权限、管理行政性用户帐户，或更新 Linux 上的 SSH 配置。 如果你需要管理经典虚拟机，可以按照[经典 VM 文档](../linux/classic/reset-access-classic.md)中的说明进行操作。 

## <a name="prerequisites"></a>先决条件
### <a name="operating-system"></a>操作系统

VM 访问扩展可以针对这些 Linux 分发运行：

| 分发 | 版本 |
|---|---|
| Ubuntu | 16.04 LTS、14.04 LTS 和 12.04 LTS |
| Debian | Debian 7.9+、8.2+ |
| RedHat | RHEL 6.7+、7.1+ |
| Oracle Linux | 6.4+、7.0+ |
| Suse | 11 和 12 |
| OpenSuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+、7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>使用 VMAccess 扩展的方法
可通过两种方法在 Linux VM 上使用 VMAccess 扩展：

* 使用 Azure CLI 2.0 和所需参数。
* [使用 VMAccess 要处理和操作的原始 JSON 文件](#use-json-files-and-the-vmaccess-extension)。

下面的示例使用 [az vm user](/cli/azure/vm/user) 命令。 若要执行这些步骤，需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。

## <a name="update-ssh-key"></a>更新 SSH 密钥
以下示例更新名为 `myVM` 的 VM 上用户 `azureuser` 的 SSH 密钥：

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> 注意：`az vm user update` 命令将新公钥文本附加到 VM 上管理员用户的 `~/.ssh/authorized_keys` 文件。 此操作不会替换或删除任何现有的 SSH 密钥。 这不会删除在部署时设置的先前密钥或通过 VMAccess 扩展进行的后续更新。

## <a name="reset-password"></a>重置密码
以下示例重置名为 `myVM` 的 VM 上用户 `azureuser` 的密码：

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>重启 SSH
下面的示例将在名为 `myVM` 的 VM 上重启 SSH 守护程序，并将 SSH 配置重置为默认值：

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>创建管理员/sudo 用户
以下示例创建名为 `myNewUser`、具有 sudo 权限的用户。 此帐户使用 SSH 密钥在名为 `myVM` 的 VM 上进行身份验证。 丢失或忘记当前凭据时，此方法有助于重新获取对 VM 的访问权限。 作为最佳做法，应限制具有 sudo 权限的帐户。

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>删除用户
以下示例将删除名为 `myVM` 的 VM 上名为 `myNewUser` 的用户：

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>使用 JSON 文件和 VMAccess 扩展
以下示例使用原始 JSON 文件。 然后使用 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) 调用 JSON 文件。 从 Azure 模板也可以调用这些 JSON 文件。 

### <a name="reset-user-access"></a>重置用户访问权限
如果已失去 Linux VM 的 root 访问权限，可以启动 VMAccess 脚本更新用户的 SSH 密钥或密码。

若要更新用户的 SSH 公钥，请创建名为 `update_ssh_key.json` 的文件并添加以下格式的设置。 用你自己的值替换 `username` 和 `ssh_key` 参数：

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

结合以下参数执行 VMAccess 脚本：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

若要重置用户密码，请创建名为 `reset_user_password.json` 的文件并添加以下格式的设置。 用你自己的值替换 `username` 和 `password` 参数：

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

结合以下参数执行 VMAccess 脚本：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>重启 SSH
若要重新启动 SSH 守护程序并将 SSH 配置重置为默认值，请创建名为 `reset_sshd.json` 的文件。 添加以下内容：

```json
{
  "reset_ssh": true
}
```

结合以下参数执行 VMAccess 脚本：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>管理管理用户

若要创建具有 sudo 权限且使用 SSH 密钥进行身份验证的用户，请创建名为 `create_new_user.json` 的文件并添加以下格式的设置。 用你自己的值替换 `username` 和 `ssh_key` 参数的值。 丢失或忘记当前凭据时，此方法有助于重新获取对 VM 的访问权限。 作为最佳做法，应限制具有 sudo 权限的帐户。

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

结合以下参数执行 VMAccess 脚本：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

若要删除用户，请创建名为 `delete_user.json` 的文件并添加以下内容。 用你自己的值替换 `remove_user` 参数：

```json
{
  "remove_user":"myNewUser"
}
```

结合以下参数执行 VMAccess 脚本：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>检查或修复磁盘
使用 VMAccess 还可以检查并修复添加到 Linux VM 的磁盘。

若要检查并修复磁盘，请创建名为 `disk_check_repair.json` 的文件并添加以下格式的设置。 用你自己的值替换 `repair_disk` 名称：

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

结合以下参数执行 VMAccess 脚本：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
