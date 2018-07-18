---
title: 将 Linux 映像添加到 Azure Stack
description: 了解如何将 Linux 映像添加到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935698"
---
# <a name="add-linux-images-to-azure-stack"></a>将 Linux 映像添加到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以通过将基于 Linux 的映像添加到 Azure Stack Marketplace，在 Azure Stack 上部署 Linux 虚拟机 (VM)。 将 Linux 映像添加到 Azure Stack 的最简单方法是通过 Marketplace 管理。 这些映像已准备好，并已针对与 Azure Stack 的兼容性进行测试。

## <a name="marketplace-management"></a>Marketplace 管理

若要从 Azure Marketplace 下载 Linux 映像，请使用以下文章中的过程。 选择要在 Azure Stack 上提供给用户的 Linux 映像。 

[将 Marketplace 项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)。

请注意，有频繁地更新这些映像，所以请查看应用商店管理通常以保持最新。

## <a name="prepare-your-own-image"></a>准备自己的映像

 只要有可能，下载通过应用商店管理的已准备好并测试 Azure 堆栈可用的映像。 
 
 Azure Linux 代理 (通常称为`WALinuxAgent`或`walinuxagent`) 是必需的并不是所有版本的代理将在 Azure 堆栈上都工作。 你应使用版本 2.2.18 或更高版本如果创建自己的映像。 请注意，[的 cloud-init](https://cloud-init.io/)不支持 Azure 堆栈这次。

 你可以准备你自己的 Linux 映像使用以下说明：

   * [基于 CentOS 的分发版](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES 和 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>将你的映像添加到应用商店
 
请按照[将映像添加到应用商店](azure-stack-add-vm-image.md)。 请确保 `OSType` 参数已设置为 `Linux`。

将映像添加到 Marketplace 后，便会创建 Marketplace 项，用户就可以部署 Linux 虚拟机了。
