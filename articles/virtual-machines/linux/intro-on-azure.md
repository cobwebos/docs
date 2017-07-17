---
title: "Azure 中的 Linux 简介 | Microsoft Docs"
description: "了解如何在 Azure 上使用 Linux 虚拟机。"
services: virtual-machines-linux
documentationcenter: python
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: b13bf305-87bf-4df3-815e-e8f6337aa6ea
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: szark
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 7bd0c5549a2e1f592681760d5ef464b9570ca4ab
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017


---
# Azure 上的 Linux 简介
<a id="introduction-to-linux-on-azure" class="xliff"></a>
本主题概述了在 Azure 云中使用 Linux 虚拟机的某些方面。 在使用库中的映像时，部署 Linux 虚拟机是一个非常简单的过程。

## 身份验证：用户名、密码和 SSH 密钥
<a id="authentication-usernames-passwords-and-ssh-keys" class="xliff"></a>
使用 Azure 门户创建 Linux 虚拟机时，系统会要求提供用户名和密码或要求提供 SSH 公钥。 在 Azure 上部署 Linux 虚拟机时，用户名的选择受到以下限制：不允许使用虚拟机中已经存在的系统帐户 (UID <100) 的名称，例如，“根”。

* 请参阅[创建运行 Linux 的虚拟机](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* 请参阅[如何在 Azure 上将 SSH 用于 Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## 使用 `sudo` 获取超级用户特权
<a id="obtaining-superuser-privileges-using-sudo" class="xliff"></a>
在 Azure 上部署虚拟机实例的过程中指定的用户帐户是特权帐户。 此帐户由 Azure Linux 代理配置为能够使用 `sudo` 实用工具提升根（超级用户帐户）的特权。 使用此用户帐户登录后，你将能够使用以下命令语法以 root 用户身份运行命令：

    # sudo <COMMAND>

可以选择使用 **sudo -s** 获取 root shell。

* 请参阅[在 Azure 中的 Linux 虚拟机上使用 root 权限](use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## 防火墙配置
<a id="firewall-configuration" class="xliff"></a>
Azure 提供了一个入站数据包筛选器，用于限制到在 Azure 门户中指定的端口的连接。 默认情况下，唯一允许的端口为 SSH。 通过在 Azure 门户中配置终结点，可以启用对 Linux 虚拟机上的其他端口的访问：

* 请参阅[如何设置虚拟机的终结点](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

默认情况下，Azure 库中的 Linux 映像不支持 *iptables* 防火墙。 如果需要，可以将该防火墙配置为提供附加筛选。

## 主机名更改
<a id="hostname-changes" class="xliff"></a>
在初始部署 Linux 映像的实例时，您需要提供虚拟机的主机名。 运行虚拟机后，此主机名将发布到平台 DNS 服务器，以便多个相互连接的虚拟机能够使用主机名执行 IP 地址查找。

如果在部署虚拟机后需要更改主机名，请使用命令

    # sudo hostname <newname>

Azure Linux 代理包含自动检测此名称更改的功能，并会相应地配置虚拟机以保留此更改以及将此更改发布到平台 DNS 服务器。

* [Azure Linux 代理用户指南](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### Cloud-Init
<a id="cloud-init" class="xliff"></a>
**Ubuntu** 和 **CoreOS** 映像利用 Azure 上的 cloud-init 为启动虚拟机提供附加功能。

* [如何插入自定义数据](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Microsoft Azure 上的自定义数据和 Cloud-Init](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
* [使用 Cloud-Init 创建 Azure 交换分区](https://wiki.ubuntu.com/AzureSwapPartitions)
* [如何在 Azure 上使用 CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

## 虚拟机映像捕获
<a id="virtual-machine-image-capture" class="xliff"></a>
利用 Azure，可以将现有虚拟机的状态捕获到映像中，该映像随后可用于部署其他虚拟机实例。 Azure Linux 代理可用于回滚在设置过程中执行的某些自定义。 您可以按照下面的步骤操作来将虚拟机作为映像捕获：

1. 运行 **waagent -deprovision**，撤消预配自定义项。 （可选）或运行 **waagent -deprovision+user**，删除预配期间指定的用户帐户和所有关联的数据。
2. 关闭虚拟机。
3. 在 Azure 门户中单击“捕获”或者使用 PowerShell 或 CLI 工具将虚拟机作为映像捕获。
   
   * 请参阅：[如何捕获用作模板的 Linux 虚拟机](classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## 附加磁盘
<a id="attaching-disks" class="xliff"></a>
每个虚拟机都附加有一个临时的本地*资源磁盘*。 由于资源磁盘上的数据可能不能在重新启动后持久存在，因此它通常由在虚拟机中运行的应用程序和进程用于数据的短暂和**临时**存储。 它还用来为操作系统存储页面文件或交换文件。

在 Linux 上，资源磁盘通常由 Azure Linux 代理管理并且自动装载到 **/mnt/resource**（或 Ubuntu 映像上的 **/mnt**）。

> [!NOTE]
> 请注意，资源磁盘是**临时**磁盘，并可能在重新启动 VM 时被删除或重新格式化。
> 
> 

在 Linux 上，数据磁盘可能由内核命名为 `/dev/sdc`，并且用户需要对该资源进行分区、格式化和装载。 在[如何将数据磁盘附加到虚拟机](../windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)的教程中对此进行了分步说明。

* **另请参阅：**[在 Linux 上配置软件 RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) & [在 Azure 中的 Linux VM 上配置 LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


