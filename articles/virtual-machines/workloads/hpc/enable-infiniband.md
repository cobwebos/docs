---
title: 启用 SR-IOV 的 Azure 虚拟机与 InifinBand |Microsoft Docs
description: 了解如何启用 SR-IOV 与 InfiniBand。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 879b1eed7bf4778d4d49f6f991d6d74214d33823
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537660"
---
# <a name="enable-infiniband-with-sr-iov"></a>启用与 SR-IOV InfiniBand


最简单的推荐方式是，若要配置自定义 VM 映像与 InfiniBand (IB) 是将 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 扩展添加到你的部署。
了解如何使用与这些 VM 扩展[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

SR-IOV 上手动配置 InfiniBand 启用的 Vm （当前 HB 和 HC 系列），请执行以下步骤。 这些步骤仅适用于 RHEL/CentOS。 对于 Ubuntu （16.04 和 18.04） 和 SLES （12 SP4 和 15），收件箱驱动程序能正常工作。 对于 Ubuntu， 


## <a name="manually-install-ofed"></a>手动安装 OFED

安装最新的 MLNX_OFED drivers for ConnectX-5 从[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)。

对于 RHEL/CentOS （下面的 7.6 示例）：
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

对于 Windows，下载并安装 WinOF 2 drivers for ConnectX-5 从[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

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

## <a name="assign-an-ip-address"></a>分配的 IP 地址

将 IP 地址分配到 ib0 接口，使用：

- 手动将 IP 地址分配给 ib0 接口，（以 root 身份）。

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

或

- 使用 WALinuxAgent 分配 IP 地址，并使其保留。

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

详细了解如何[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
