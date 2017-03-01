---
title: "Azure 上的 FreeBSD 简介 | Microsoft Docs"
description: "了解如何在 Azure 上使用 FreeBSD 虚拟机"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 71ad04b10bc49500197db6fecdcc0305a1ea0dd2
ms.openlocfilehash: ecb1c385de6c1b12674326afe7d5a0ebf6cd9ad0


---
# <a name="introduction-to-freebsd-on-azure"></a>Azure 上的 FreeBSD 简介
本主题提供在 Azure 中运行 FreeBSD 虚拟机的概述。

## <a name="overview"></a>概述
适用于 Microsoft Azure 的 FreeBSD 是一套先进的计算机操作系统，可为新型服务器、台式机和嵌入式平台提供技术支持。

Microsoft Corporation 在 Azure 上提供预先配置了 [Azure VM 来宾代理](https://github.com/Azure/WALinuxAgent/)的 FreeBSD 映像。 目前，以下 FreeBSD 版本由 Microsoft 以映像形式提供：

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE

进行首次使用时的 VM 预配（用户名、密码或 SSH 密钥、主机名等）以及为选择性 VM 扩展启用相关功能等操作时，该代理负责在 FreeBSD VM 和 Azure 结构之间进行通信。

至于未来版本的 FreeBSD，所采用的策略是始终进行更新，确保在 FreeBSD 版本工程团队发布最新版本后很快就可以使用这些版本。

## <a name="deploying-a-freebsd-virtual-machine"></a>部署 FreeBSD 虚拟机
在使用 Azure 应用商店中的映像时，部署 FreeBSD 虚拟机是一个非常简单的过程：

- [Azure 应用商店中的 FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [Azure 应用商店中的 FreeBSD 11.0](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)

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

[!NOTE]目前 FreeBSD VM 仅支持 CustomScript 1.x。  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>身份验证：用户名、密码和 SSH 密钥
使用 Azure 门户创建 FreeBSD 虚拟机时，必须提供用户名、密码或 SSH 公钥。
在 Azure 上部署 FreeBSD 虚拟机时，用户名必须与已经存在于虚拟机中的系统帐户 (UID <100) 的名称（例如“root”）相符。
目前仅支持 RSA SSH 密钥。 多行 SSH 密钥必须以 `---- BEGIN SSH2 PUBLIC KEY ----` 开头，以 `---- END SSH2 PUBLIC KEY ----` 结尾。

## <a name="obtaining-superuser-privileges"></a>获取超级用户特权
在 Azure 上部署虚拟机实例的过程中指定的用户帐户是特权帐户。 sudo 的包已安装在所发布的 FreeBSD 映像中。
通过此用户帐户登录后，即可使用命令语法以 root 用户身份运行命令。

    $ sudo <COMMAND>

可以选择使用 `sudo -s` 获取 root shell。

## <a name="known-issues"></a>已知问题
1. 目前在基于 Hyper-V（以及 Azure）的 FreeBSD 11.0 上存在一个尚待解决的问题，如果操作系统使用 `freebsd-update` 进行修补，该问题可能导致 VM 无法启动。 [建议的修补程序](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=212721)包含在 Azure 应用商店上的 FreeBSD 映像中。 但是，FreeBSD 团队尚未将此修补程序合并到上游，因此运行 `freebsd-update` 以便将内核替换为未修补的内核。 建议 Azure 用户在 FreeBSD 11.0 修补程序作为 ERRATA 发布后再安装该修补程序。

2. [Azure VM 来宾代理](https://github.com/Azure/WALinuxAgent/) 2.2.2 存在 [已知问题] (https://github.com/Azure/WALinuxAgent/pull/517)，此问题导致 Azure 上的 FreeBSD VM 预配失败。 建议 Azure 上的 FreeBSD VM 用户使用 2.2.1 或更早版本。 [Azure VM 来宾代理](https://github.com/Azure/WALinuxAgent/) 2.2.3 将修复此问题。 

## <a name="next-steps"></a>后续步骤
* 转到 [Azure 应用商店](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)创建 FreeBSD VM。
* 如果要将自己的 FreeBSD 放入 Azure，请参阅 [Create and upload a FreeBSD VHD to Azure](./virtual-machines-linux-classic-freebsd-create-upload-vhd.md)（创建 FreeBSD VHD 并将其上载到 Azure）。



<!--HONumber=Jan17_HO2-->


