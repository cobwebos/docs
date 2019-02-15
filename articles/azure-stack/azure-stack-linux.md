---
title: 将 Linux 映像添加到 Azure Stack
description: 了解如何将 Linux 映像添加到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 8e30edcc7a600088693de57264665f5ffff3842a
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300725"
---
# <a name="add-linux-images-to-azure-stack"></a>将 Linux 映像添加到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以通过将基于 Linux 的映像添加到 Azure Stack 市场，在 Azure Stack 上部署 Linux 虚拟机 (VM)。 将 Linux 映像添加到 Azure Stack 的最简单方法是通过市场管理。 这些映像已准备好，并已针对与 Azure Stack 的兼容性进行测试。

## <a name="marketplace-management"></a>市场管理

若要从 Azure 市场下载 Linux 映像，请使用[将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md) 一文中的过程。 选择要在 Azure Stack 上提供给用户的 Linux 映像。 

请注意，这些映像频繁更新，因此请经常查看市场管理以保持最新。

## <a name="prepare-your-own-image"></a>准备自己的映像

只要有可能，请下载通过市场管理提供的映像，这些映像已针对 Azure Stack 进行了准备和测试。

Azure Linux 代理 (通常称为`WALinuxAgent`或`walinuxagent`) 是必需的并不是所有版本的 Azure Stack 上的代理工作。 应使用版本 2.2.20 或更高版本创建你自己的映像。 请注意，目前 Azure Stack 不支持 [cloud-init](https://cloud-init.io/)。

可以按照以下说明准备自己的 Linux 映像：

* [基于 CentOS 的分发版](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES 和 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>将映像添加到市场

按照[将映像添加到市场](azure-stack-add-vm-image.md)进行操作。 请确保 `OSType` 参数已设置为 `Linux`。

将映像添加到市场 后，便会创建市场项，用户就可以部署 Linux 虚拟机了。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Azure Stack 市场概述](azure-stack-marketplace.md)
