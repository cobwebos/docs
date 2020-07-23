---
title: 创建并上传 Flatcar 容器 Linux VHD 以在 Azure 中使用
description: 了解如何创建和上载包含 Flatcar Container Linux 操作系统的 VHD。
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 2bdbe93d5d593d429c4f7073227684c1e361864d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531045"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>使用 Azure 的预生成的 Flatcar 映像

可以为每个 Flatcar 支持的通道下载 Flatcar 容器 Linux 的预构建的 Azure 虚拟硬盘映像：

- [稳定](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [全角字](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [底端](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

此映像已完全设置并经过优化，可在 Azure 上运行。 只需将它解压缩。

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>为 Azure 构建你自己的基于 Flatcar 的虚拟机

或者，你可以选择生成自己的 Flatcar 容器 Linux 映像。

在任何基于 linux 的计算机上，按照[Flatcar 容器 linux 开发人员 SDK 指南](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)中的说明进行操作。 运行脚本时 `image_to_vm.sh` ，请确保传递 `--format=azure` 以创建 Azure 虚拟硬盘。

## <a name="next-steps"></a>后续步骤

获得 .vhd 文件后，可以使用生成的文件在 Azure 中创建新的虚拟机。 如果这是你第一次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 LINUX VM](upload-vhd.md#option-1-upload-a-vhd)。
