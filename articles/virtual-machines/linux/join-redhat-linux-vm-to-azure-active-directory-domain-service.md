---
title: "将 RedHat Linux VM 加入 Azure Active Directory DS | Microsoft Docs"
description: "如何将现有的 RedHat Enterprise Linux 7 VM 加入 Azure Active Directory 域服务。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 2e46a0f3c9bdbe267d121b4bf62e25d5d411fcc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>将 RedHat Linux VM 加入 Azure Active Directory 域服务

本文说明如何将 Red Hat Enterprise Linux (RHEL) 7 虚拟机加入 Azure Active Directory 域服务 (AADDS) 托管域。  要求如下：

- [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 公钥和私钥文件](mac-create-ssh-keys.md)

- [Azure Active Directory 域服务 DC](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>快速命令

_将所有示例替换为自己的设置。_

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>将 azure-cli 切换到经典部署模式

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>搜索 RHEL 版本和映像

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>创建 Redhat Linux VM

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>SSH 到 VM

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>更新 YUM 包

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>安装所需的包

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

在 Linux 虚拟机上安装所需的包后，下一个任务是将虚拟机加入托管域。

### <a name="discover-the-aad-domain-services-managed-domain"></a>发现 AAD 域服务托管域

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>初始化 kerberos

请确保指定属于“AAD DC 管理员”组的用户。 只有这些用户可将计算机加入托管域。

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>将计算机加入域

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>验证计算机是否已加入域

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>后续步骤

* [用于 Azure 中按需 Red Hat Enterprise Linux VM 的 Red Hat 更新基础结构 (RHUI)](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [在 Azure Resource Manager 中为虚拟机设置密钥保管库](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure Resource Manager 模板和 Azure CLI 部署和管理虚拟机](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
