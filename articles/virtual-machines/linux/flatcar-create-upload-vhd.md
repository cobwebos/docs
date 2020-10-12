---
title: 创建 Flatcar Container Linux VHD 并将其上传，以供在 Azure 中使用
description: 了解如何创建和上传包含 Flatcar Container Linux 操作系统的 VHD。
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 555e53899ed78a5200009d04659e974f8157057e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87268233"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>为 Azure 使用预生成的 Flatcar 映像

可以为每个 Flatcar 支持的通道下载 Flatcar Container Linux 的预生成的 Azure 虚拟硬盘映像：

- [stable](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

此映像已经过完全设置并优化，可在 Azure 上运行。 只需将它解压缩。

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>为 Azure 生成你自己的基于 Flatcar 的虚拟机

或者，可以选择生成自己的 Flatcar Container Linux 映像。

在任何基于 linux 的计算机上，请遵循 [Flatcar Container Linux 开发者 SDK 指南](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)中的详细说明进行操作。 运行 `image_to_vm.sh` 脚本时，请确保传递 `--format=azure` 以创建 Azure 虚拟硬盘。

## <a name="next-steps"></a>后续步骤

获得 .vhd 文件后，可以使用生成的文件在 Azure 中创建新的虚拟机。 如果是首次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。
