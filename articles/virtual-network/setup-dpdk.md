---
title: Azure Linux VM 中的 DPDK | Microsoft Docs
description: 了解如何在 Linux 虚拟机中设置 DPDK。
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: a03b72200f97c54bce188ec6a6ad8a06a43f26ae
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002573"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>在 Linux 虚拟机中设置 DPDK

Azure 上的数据平面开发工具包 (DPDK) 针对绕过虚拟机内核网络堆栈的性能密集型应用程序提供一个更快的用户空间数据包处理框架。

使用内核网络堆栈的典型数据包处理工作负荷由中断指令驱动。 每当网络接口收到传入的数据包时，某个内核中断会处理从内核空间到用户空间的数据包与上下文切换。 DPDK 使用轮询模式驱动程序来加速数据包的处理，通过用户空间实现来消除上下文切换和中断驱动式方法。

DPDK 包含一组用户空间库，这些库提供对硬件、逻辑核心、内存管理和网络接口卡轮询模式驱动程序的访问。

DPDK 可在 Azure 虚拟机中运行，支持多个操作系统分发版。 DPDK 在驱动网络功能虚拟化实现方面提供与众不同的性能，它的形式为网络虚拟设备 (NVA)，例如虚拟路由器、防火墙、VPN、负载均衡器、演进数据包核心网和拒绝服务 (DDoS) 应用程序。

## <a name="benefit"></a>优势

**更高的每秒数据包数 (PPS)**：绕过内核并控制用户空间中的数据包可以通过消除上下文切换来减少周期计数，并改善 Azure Linux 虚拟机中的每秒数据包处理速率。


## <a name="supported-operating-systems"></a>支持的操作系统

支持 Azure 库中的以下分发版：

| Linux OS     | 内核版本        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**自定义内核支持**

对于未列出的任何 Linux 内核版本，请参阅[用于生成 Azure 优化的 Linux 内核的修补程序](https://github.com/microsoft/azure-linux-kernel)；如需更多信息，请联系 [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)。 

## <a name="region-support"></a>区域支持

所有 Azure 区域都支持 DPDK。

## <a name="prerequisites"></a>先决条件

必须在 Linux 虚拟机上启用加速网络。 虚拟机应至少有两个网络接口，其中一个接口用于管理。 了解如何[创建启用加速网络的 Linux 虚拟机](create-vm-accelerated-networking-cli.md)。

## <a name="install-dpdk-dependencies"></a>安装 DPDK 依赖项

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel
```

### <a name="sles-15"></a>SLES 15

**Azure 内核**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**默认内核**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>设置虚拟机环境（一次性的操作）

1. [下载最新的 DPDK](https://core.dpdk.org/download)。 Azure 需要 18.02 或更高版本。
2. 首先使用 `make config T=x86_64-native-linuxapp-gcc` 生成默认配置。
3. 使用 `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` 在生成的配置中启用 Mellanox PMDs。
4. 使用 `make` 进行编译。
5. 使用 `make install DESTDIR=<output folder>` 进行安装。

# <a name="configure-runtime-environment"></a>配置运行时环境

重新启动后，运行以下命令一次：

1. 巨页

   * 针对所有 numa 节点运行以下命令一次，以配置巨页：

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  使用 `mkdir /mnt/huge` 创建用于装载的目录。
   *  使用 `mount -t hugetlbfs nodev /mnt/huge` 装载巨页。
   *  使用 `grep Huge /proc/meminfo` 检查巨页是否已保留。

     > [!NOTE]
     > 遵照 DPDK 的[说明](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment)，通过某种方法来修改 grub 文件，以便启动时保留巨页。 页面底部提供了说明。 在 Azure Linux 虚拟机中运行时，应修改 /etc/config/grub.d 下的文件，以便在重新启动后保留巨页。

2. MAC 和 IP 地址：使用 `ifconfig –a` 查看网络接口的 MAC 和 IP 地址。 *VF* 网络接口和 *NETVSC* 网络接口具有相同的 MAC 地址，但只有 *NETVSC* 网络接口具有 IP 地址。 VF 接口以 NETVSC 接口的从属接口的形式运行。

3. PCI 地址

   * 通过 `ethtool -i <vf interface name>` 找到 *VF* 使用的 PCI 地址。
   * 如果为 *eth0* 启用了加速网络，请确保 testpmd 不会意外接管 *eth0* 的 VF PCI 设备。 如果 DPDK 应用程序意外接管了管理网络接口并导致 SSH 连接断开，请使用串行控制台来终止 DPDK 应用程序，或者停止或启动虚拟机。

4. 每次重新启动后，使用 `modprobe -a ib_uverbs` 加载 *ibuverbs*。 （仅适用于 SLES 15）另外，使用 `modprobe -a mlx4_ib` 加载 *mlx4_ib*。

## <a name="failsafe-pmd"></a>防故障 PMD

DPDK 应用程序必须通过 Azure 中公开的防故障 PMD 运行。 如果应用程序直接通过 VF PMD 运行，则它不会收到发往 VM 的**所有**数据包，因为某些数据包通过合成接口显示。 通过防故障 PMD 运行可以保证应用程序收到发往 VM 的所有数据包，此外，即使在为主机提供服务时撤销了 VF，也能确保应用程序继续以 DPDK 模式运行。 有关防故障 PMD 的详细信息，请参阅[防故障轮询模式驱动程序库](http://doc.dpdk.org/guides/nics/fail_safe.html)。

## <a name="run-testpmd"></a>运行 testpmd

在 *testpmd* 命令的前面使用 `sudo` 可在根模式下运行。

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>基本：健全性检查、防故障适配器初始化

1. 运行以下命令启动单端口 testpmd 应用程序：

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. 运行以下命令启动双端口 testpmd 应用程序：

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   如果配合 2 个以上的 NIC 运行，则 `--vdev` 参数采用此模式：`net_vdev_netvsc<id>,iface=<vf’s pairing eth>`。

3.  启动后，运行 `show port info all` 来检查端口信息。 应会看到一个或两个值为 net_failsafe（不是 *net_mlx4*）的 DPDK 端口。
4.  使用 `start <port> /stop <port>` 启动流量。

上述命令在交互模式（建议的模式）下启动 *testpmd*，以体验某些 testpmd 命令。

### <a name="basic-single-sendersingle-receiver"></a>基本：单个发送端/单个接收端

以下命令定期列显每秒数据包数的统计信息：

1. 在 TX 端运行以下命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. 在 RX 端运行以下命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

在虚拟机上运行上述命令时，请在编译之前更改 `app/test-pmd/txonly.c` 中的 *IP_SRC_ADDR* 和 *IP_DST_ADDR*，以匹配虚拟机的实际 IP 地址。 否则，数据包在抵达接收端之前将被丢弃。

### <a name="advanced-single-sendersingle-forwarder"></a>高级：单个发送端/单个转发端
以下命令定期列显每秒数据包数的统计信息：

1. 在 TX 端运行以下命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. 在 FWD 端运行以下命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

在虚拟机上运行上述命令时，请在编译之前更改 `app/test-pmd/txonly.c` 中的 *IP_SRC_ADDR* 和 *IP_DST_ADDR*，以匹配虚拟机的实际 IP 地址。 否则，数据包在抵达转发端之前将被丢弃。 无法使用第三台计算机来接收转发的流量，因为除非做出一些代码更改，否则 *testpmd* 转发端不会修改第 3 层地址。

## <a name="references"></a>参考

* [EAL 选项](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd 命令](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
