---
title: "将 Linux 映像添加到 Azure 堆栈"
description: "了解如何向 Azure 堆栈中添加 Linux 映像。"
services: azure-stack
documentationcenter: 
author: anjayajodha
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
ms.openlocfilehash: a8763c01cba4e5a9eaa3b7842b627d6eb9661a95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="add-linux-images-to-azure-stack"></a>将 Linux 映像添加到 Azure 堆栈

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包* 

可以通过将基于 Linux 的映像添加到 Azure 堆栈应用商店来部署 Azure 堆栈上的 Linux 虚拟机。 将 Linux 映像添加到 Azure 堆栈的最简单方法是通过应用商店管理。

## <a name="marketplace-management"></a>应用商店管理

若要从 Azure 应用商店下载的 Linux 映像，请在下面的文章使用过程。 选择你想要提供用户 Azure 堆栈上的 Linux 映像。

[从 Azure 应用商店项下载到 Azure 堆栈](azure-stack-download-azure-marketplace-item.md)。

## <a name="download-an-image"></a>下载图像

你可以下载并提取堆栈兼容的 Azure Linux 映像使用以下链接：


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. 如有必要提取映像 VHD 和[将映像添加到应用商店](azure-stack-add-vm-image.md)。 请确保`OSType`参数设置为`Linux`。
2. 你添加到应用商店映像后，将创建一个应用商店项和用户可以部署 Linux 虚拟机。

## <a name="prepare-your-own-image"></a>准备你自己的映像

你可以准备你自己的 Linux 映像使用以下说明之一：
   
   * [基于 CentOS 的分发版](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES 和 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. 下载并安装[Azure Linux 代理](https://github.com/Azure/WALinuxAgent/)。
   
    Azure Linux 代理版本 2.1.3 或更高版本需要设置你的 Linux VM Azure 堆栈上。 列出的发行版本的许多以前已包含此版本的代理或更高版本以包的形式存储在其存储库 (通常称为`WALinuxAgent`或`walinuxagent`)。 但是，Azure 代理包的版本是否小于 2.1.3 （例如，2.0.18 或更低），则必须手动安装代理。 可以确定已安装的版本，从包名称或通过运行`/usr/sbin/waagent -version`VM 上。
   
    按照下面的说明手动-安装 Azure Linux 代理
   
   a. 首先，下载最新的 Azure Linux 代理从[GitHub](https://github.com/Azure/WALinuxAgent/releases)，示例：
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. 解压缩 Azure 代理：
     
            # tar -vzxf v2.2.16.tar.gz
   c. 安装 python setuptools
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d.单击“下一步”。 安装 Azure 代理：
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     系统使用 Python 2.x 和 Python 3.x 安装-并行安装可能需要运行以下命令：
     
         sudo python3 setup.py install --register-service
     有关详细信息，请参阅 Azure Linux 代理[自述文件](https://github.com/Azure/WALinuxAgent/blob/master/README.md)。
2. [将映像添加到应用商店](azure-stack-add-vm-image.md)。 请确保`OSType`参数设置为`Linux`。
3. 你添加到应用商店映像后，将创建一个应用商店项和用户可以部署 Linux 虚拟机。

## <a name="next-steps"></a>后续步骤
[提供 Azure 堆栈中的服务的概述](azure-stack-offer-services-overview.md)

