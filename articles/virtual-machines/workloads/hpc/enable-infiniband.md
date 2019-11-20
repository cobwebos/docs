---
title: 使用 SR-IOV 启用 InifinBand-Azure 虚拟机 |Microsoft Docs
description: 了解如何使用 SR-IOV 启用 "不允许"。
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
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196767"
---
# <a name="enable-infiniband-with-sr-iov"></a>使用 SR-IOV 启用 "不允许"

Azure NC、ND 和 H 系列 Vm 均由专用的未受支持网络支持。 所有启用 RDMA 的大小都可以利用使用 Intel MPI 的网络。 某些 VM 系列已通过 SR-IOV 扩展了对所有 MPI 实现和 RDMA 谓词的支持。 支持 RDMA 的 Vm 包括[GPU 优化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)和[高性能计算（HPC）](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) vm。

## <a name="choose-your-installation-path"></a>选择安装路径

若要开始操作，最简单的方法是使用预先配置的、可在以下情况下使用的平台映像：

- **Hpc Iaas vm** –若要开始使用适用于 HPC 的 IaaS vm，最简单的解决方案是使用已配置了 "不占用" 的[CentOS-HPC 7.6 VM OS 映像](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)。 由于此映像已配置了无线处理，因此不需要手动配置。 对于兼容的 Windows 版本，请参阅[支持 WINDOWS RDMA 的实例](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)。

- **Gpu IaaS vm** –当前没有为 Gpu 优化 vm 预先配置平台映像， [CentOS-HPC 7.6 VM OS 映像](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)除外。 若要使用无限配置自定义映像，请参阅[手动安装 MELLANOX OFED](#manually-install-mellanox-ofed)。

如果你使用的是自定义 VM 映像或[GPU 优化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)VM，则应通过将 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 扩展添加到你的部署来对其进行配置。 了解如何在[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)中使用这些 VM 扩展。

## <a name="manually-install-mellanox-ofed"></a>手动安装 Mellanox OFED

若要使用 SR-IOV 手动配置可操作，请使用以下步骤。 这些步骤中的示例显示了 RHEL/CentOS 的语法，但步骤是一般的，可用于任何兼容的操作系统，如 Ubuntu （16.04、18.04 19.04）和 SLES （12 SP4 和15）。 收件箱驱动程序也工作正常，但 Mellanox OpenFabrics 驱动程序提供更多功能。

有关适用于 Mellanox 驱动程序的受支持的分发的详细信息，请参阅最新的[Mellanox OpenFabrics 驱动程序](https://www.mellanox.com/page/products_dyn?product_family=26)。 有关 Mellanox OpenFabrics 驱动程序的详细信息，请参阅[mellanox 用户指南](https://docs.mellanox.com/category/mlnxofedib)。

请参阅以下示例，了解如何在 Linux 上配置未实现的无限：

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

对于 Windows，请下载并安装[适用于 windows 的 MELLANOX OFED 驱动程序](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)。

## <a name="enable-ip-over-infiniband"></a>启用 IP 过度使用

使用以下命令启用 IP 而不通过使用。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>后续步骤

了解有关 Azure 上的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的详细信息。
