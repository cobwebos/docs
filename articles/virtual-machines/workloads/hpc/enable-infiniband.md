---
title: 使用 SR-IOV 启用 InifinBand - Azure 虚拟机 |微软文档
description: 了解如何使用 SR-IOV 启用英菲尼班。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196767"
---
# <a name="enable-infiniband-with-sr-iov"></a>使用 SR-IOV 启用英菲尼班

Azure NC、ND 和 H 系列 VM 都由专用的 InfiniBand 网络支持。 所有支持 RDMA 的尺寸都能够使用英特尔 MPI 利用该网络。 某些 VM 系列通过 SR-IOV 扩展了对所有 MPI 实现和 RDMA 谓词的支持。 支持 RDMA 的 VM 包括[GPU 优化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)和[高性能计算 （HPC）](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) VM。

## <a name="choose-your-installation-path"></a>选择安装路径

要开始，最简单的选项是使用为 InfiniBand 预配置的平台映像（如果可用）：

- **HPC IaaS VM** – 要开始使用适用于 HPC 的 IaaS VM，最简单的解决方案是使用[CentOS-HPC 7.6 VM OS 映像](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)，该映像已配置 InfiniBand。 由于此映像已配置 InfiniBand，因此无需手动配置它。 有关兼容的 Windows 版本，请参阅[支持 Windows RDMA 的实例](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)。

- **GPU IaaS VM** – 除了[CentOS-HPC 7.6 VM OS 映像](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)外，目前没有为 GPU 优化 VM 预配置任何平台映像。 要使用 InfiniBand 配置自定义映像，请参阅[手动安装 Mellanox OFED](#manually-install-mellanox-ofed)。

如果您使用的是自定义 VM 映像或[GPU 优化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)的 VM，则应通过将 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 扩展添加到部署，使用 InfiniBand.Drive.Windows VM 扩展来配置它。 了解如何将这些 VM 扩展与[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)一起使用。

## <a name="manually-install-mellanox-ofed"></a>手动安装梅拉诺克斯

要使用 SR-IOV 手动配置 InfiniBand，请使用以下步骤。 这些步骤中的示例显示了 RHEL/CentOS 的语法，但这些步骤是常规的，可用于任何兼容的操作系统，如 Ubuntu （16.04、 18.04 19.04） 和 SLES （12 SP4 和 15）。 收件箱驱动程序也工作，但 Mellanox OpenFabrics 驱动程序提供了更多功能。

有关梅拉诺克斯驱动程序支持的分布的详细信息，请参阅最新的[Mellanox OpenFabrics 驱动程序](https://www.mellanox.com/page/products_dyn?product_family=26)。 有关 Mellanox OpenFabrics 驱动程序的详细信息，请参阅[梅拉诺克斯用户指南](https://docs.mellanox.com/category/mlnxofedib)。

有关如何在 Linux 上配置 InfiniBand，请参阅以下示例：

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

对于 Windows，下载并安装[适用于 Windows 驱动程序的 Mellanox OFED。](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ip-over-infiniband"></a>通过英菲尼班启用 IP

使用以下命令通过 InfiniBand 启用 IP。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>后续步骤

在 Azure 上了解有关[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多详细信息。
