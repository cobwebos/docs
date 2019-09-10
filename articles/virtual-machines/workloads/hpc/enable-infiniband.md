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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858485"
---
# <a name="enable-infiniband-with-sr-iov"></a>使用 SR-IOV 启用 "不允许"

若要开始使用适用于 HPC 的 IaaS Vm，最简单的建议方法是使用 CentOS-HPC 7.6 VM OS 映像。 如果使用自定义 VM 映像，使用 "无线处理" （IB）对其进行配置的最简单方法是将 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 扩展添加到部署。
了解如何在[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)上使用这些 VM 扩展

若要在启用 SR-IOV 的 Vm （当前为 HB-ACCT-WC 和 HC 系列）上手动配置不允许，请执行以下步骤。 这些步骤仅适用于 RHEL/CentOS。 对于 Ubuntu （16.04 和18.04）和 SLES （12 SP4 和15），收件箱驱动程序可以正常工作。

## <a name="manually-install-ofed"></a>手动安装 OFED

安装适用于 MLNX_OFED 的最新驱动程序-5 从[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)。

对于 RHEL/CentOS （以下示例中为7.6）：

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

对于 Windows，请下载并安装适用于 WinOF 的 5-2 驱动程序，从[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>启用 IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>分配 IP 地址

使用以下任一方法将 IP 地址分配到 ib0 接口：

- 手动将 IP 地址分配到 ib0 接口（作为根）。

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

OR

- 使用 WALinuxAgent 分配 IP 地址，并使其保持不变。

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>后续步骤

了解有关 Azure 上的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的详细信息。
