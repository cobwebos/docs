---
title: "Azure 上的 FreeBSD 简介 | Microsoft Docs"
description: "了解如何在 Azure 上使用 FreeBSD 虚拟机"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: e47a00a8375659f9112da57f9e7a4747f10f8f16
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="introduction-to-freebsd-on-azure"></a>Azure 上的 FreeBSD 简介
本主题提供在 Azure 中运行 FreeBSD 虚拟机的概述。

## <a name="overview"></a>概述
适用于 Microsoft Azure 的 FreeBSD 是一套先进的计算机操作系统，可为新型服务器、台式机和嵌入式平台提供技术支持。

Microsoft Corporation 在 Azure 上提供预先配置了 [Azure VM 来宾代理](https://github.com/Azure/WALinuxAgent/)的 FreeBSD 映像。 目前，以下 FreeBSD 版本由 Microsoft 以映像形式提供：

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE
- FreeBSD 11.1-RELEASE

进行首次使用时的 VM 预配（用户名、密码或 SSH 密钥、主机名等）以及为选择性 VM 扩展启用相关功能等操作时，该代理负责在 FreeBSD VM 和 Azure 结构之间进行通信。

至于未来版本的 FreeBSD，所采用的策略是始终进行更新，确保在 FreeBSD 版本工程团队发布最新版本后很快就可以使用这些版本。

## <a name="deploying-a-freebsd-virtual-machine"></a>部署 FreeBSD 虚拟机
在 Azure 门户中使用来自 Azure Marketplace 的映像部署 FreeBSD 虚拟机是一个非常简单的过程：

- [Azure Marketplace 中的 FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [Azure Marketplace 中的 FreeBSD 11.0](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)
- [Azure Marketplace 中的 FreeBSD 11.1](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>通过 FreeBSD 上的 Azure CLI 2.0 创建 FreeBSD VM
首先需要通过以下命令在 FreeBSD 计算机上安装 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

如果 FreeBSD 计算机上未安装 bash，请在安装前运行以下命令。 

```bash
sudo pkg install bash
```

如果 FreeBSD 计算机上未安装 python，请在安装前运行以下命令。 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

安装期间，系统将询问 `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`。 如果回答 `y` 并输入 `/etc/rc.conf` 作为 `a path to an rc file to update`，则可能会出现问题 `ERROR: [Errno 13] Permission denied`。 为了解决该问题，应针对文件 `etc/rc.conf` 向当前用户授予写入权限。

现在可登录 Azure 并创建 FreeBSD VM。 以下是创建 FreeBSD 11.0 VM 的一个示例。 也可以为新创建的公共 IP 添加具有全局唯一 DNS 名称的 `--public-ip-address-dns-name` 参数。 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

然后可通过上述部署输出中打印的 IP 地址登录到 FreeBSD VM。 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>FreeBSD 的 VM 扩展
下面是 FreeBSD VM 中支持的 VM 扩展。

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) 扩展可以：

* 重置原始 sudo 用户的密码。
* 创建新的 sudo 用户并指定密码。
* 使用给定的密钥设置公共主机密钥。
* 重置 VM 预配期间提供的公共主机密钥（如果未提供主机密钥）。
* 如果 reset_ssh 设置为 true，则打开 SSH 端口 (22) 并还原 sshd_config。
* 删除现有用户。
* 检查磁盘。
* 修复添加的磁盘。

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) 扩展可以：

* 从 Azure 存储或外部公共存储（例如 GitHub）下载自定义的脚本（如果已提供）。
* 运行入口点脚本。
* 支持内联命令。
* 自动转换 shell 和 Python 脚本中的 Windows 样式换行符。
* 自动删除 shell 和 Python 脚本中的 BOM。
* 保护 CommandToExecute 中的敏感数据。

> [!NOTE]
> 目前 FreeBSD VM 仅支持 CustomScript 1.x。  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>身份验证：用户名、密码和 SSH 密钥
使用 Azure 门户创建 FreeBSD 虚拟机时，必须提供用户名、密码或 SSH 公钥。
在 Azure 上部署 FreeBSD 虚拟机时，用户名必须与已经存在于虚拟机中的系统帐户 (UID <100) 的名称（例如“root”）相符。
目前仅支持 RSA SSH 密钥。 多行 SSH 密钥必须以 `---- BEGIN SSH2 PUBLIC KEY ----` 开头，以 `---- END SSH2 PUBLIC KEY ----` 结尾。

## <a name="obtaining-superuser-privileges"></a>获取超级用户权限
在 Azure 上部署虚拟机实例的过程中指定的用户帐户是特权帐户。 sudo 的包已安装在所发布的 FreeBSD 映像中。
通过此用户帐户登录后，即可使用命令语法以 root 身份运行命令。

```
$ sudo <COMMAND>
```

可以选择使用 `sudo -s` 获取 root shell。

## <a name="known-issues"></a>已知问题
[Azure VM 来宾代理](https://github.com/Azure/WALinuxAgent/) 2.2.2 存在 [已知问题] (https://github.com/Azure/WALinuxAgent/pull/517)，此问题导致 Azure 上的 FreeBSD VM 预配失败。 [Azure VM 来宾代理](https://github.com/Azure/WALinuxAgent/) 2.2.3 及更高版本已修复此问题。 

## <a name="next-steps"></a>后续步骤
* 转到 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) 创建 FreeBSD VM。
