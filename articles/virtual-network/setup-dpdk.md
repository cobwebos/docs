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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c5cb840035c5d0d5694982324c7237c58001e689
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993872"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>在 Linux 虚拟机中设置 DPDK

Azure 中的数据平面开发工具包 (DPDK) 提供了更快速的用户空间包处理框架，适用于性能密集型应用程序。 此框架绕过虚拟机的内核网络堆栈。

在使用内核网络堆栈的典型包处理中，进程是由中断指令驱动。 当网络接口收到传入的包时，不仅有内核中断指令会处理包，还有上下文切换会从内核空间切换到用户空间。 DPDK 消除了上下文切换和中断指令驱动方法，而是实现用户空间，以使用轮询模式驱动程序来加速包处理。

DPDK 由多组用户空间库构成，这些库提供对较低级别资源的访问权限。 这些资源包括硬件、逻辑核心、内存管理和网络接口卡的轮询模式驱动程序。

DPDK 可以在支持多个操作系统分发版的 Azure 虚拟机中运行。 DPDK 在驱动网络功能虚拟化实现方面提供与众不同的关键性能。 这些实现可采用网络虚拟设备 (NVA) 的形式，如虚拟路由器、防火墙、VPN、负载均衡器、演进包核心和拒绝服务 (DDoS) 应用程序。

## <a name="benefit"></a>优势

**较高每秒数据包数 (PPS)**:跳过内核和控制用户空间中的数据包的上下文切换，从而减少周期计数。 同时，这还会提高 Azure Linux 虚拟机中每秒处理的包比率。


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

对于未列出的任何 Linux 内核版本，请参阅[用于生成 Azure 优化 Linux 内核的修补程序](https://github.com/microsoft/azure-linux-kernel)。 有关详细信息，还可以联系 [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)。 

## <a name="region-support"></a>区域支持

所有 Azure 区域都支持 DPDK。

## <a name="prerequisites"></a>必备组件

必须在 Linux 虚拟机上启用加速网络。 虚拟机应至少有两个网络接口，其中一个接口用于管理。 了解如何[创建启用加速网络的 Linux 虚拟机](create-vm-accelerated-networking-cli.md)。

## <a name="install-dpdk-dependencies"></a>安装 DPDK 依赖项

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
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

## <a name="set-up-the-virtual-machine-environment-once"></a>设置虚拟机环境（一次性操作）

1. [下载最新的 DPDK](https://core.dpdk.org/download)。 Azure 需要 18.02 或更高版本。
2. 运行 `make config T=x86_64-native-linuxapp-gcc` 生成默认配置。
3. 使用 `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` 在生成的配置中启用 Mellanox PMDs。
4. 使用 `make` 进行编译。
5. 使用 `make install DESTDIR=<output folder>` 进行安装。

## <a name="configure-the-runtime-environment"></a>配置运行时环境

重启后，运行下面的命令一次：

1. 巨页

   * 针对所有 numa 节点运行以下命令一次，以配置巨页：

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * 使用 `mkdir /mnt/huge` 创建用于装载的目录。
   * 使用 `mount -t hugetlbfs nodev /mnt/huge` 装载巨页。
   * 运行 `grep Huge /proc/meminfo` 检查巨页是否已保留。

     > [!NOTE]
     > 可以将 grub 文件修改为，在启动时保留巨页，具体是按照适用于 DPDK 的[说明](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment)操作。 页面底部提供了这些说明。 如果使用的是 Azure Linux 虚拟机，请改为将 /etc/config/grub.d 下的文件修改为跨重启保留巨页。

2. MAC 和 IP 地址：使用`ifconfig –a`若要查看网络接口的 MAC 和 IP 地址。 *VF* 网络接口和 *NETVSC* 网络接口具有相同的 MAC 地址，但只有 *NETVSC* 网络接口具有 IP 地址。 VF 接口以 NETVSC 接口的从属接口形式运行。

3. PCI 地址

   * 运行 `ethtool -i <vf interface name>` 确定对 VF 使用哪个 PCI 地址。
   * 如果 eth0 已启用加速网络，请确保 testpmd 不会意外接管 eth0 的 VF PCI 设备。 如果 DPDK 应用程序意外接管管理网络接口，并导致 SSH 连接断开，请使用串行控制台来停止 DPDK 应用程序。 串行控制台还可用于停止或启动虚拟机。

4. 每次重新启动后，使用 `modprobe -a ib_uverbs` 加载 *ibuverbs*。 （仅适用于 SLES 15）另外，使用 `modprobe -a mlx4_ib` 加载 *mlx4_ib*。

## <a name="failsafe-pmd"></a>防故障 PMD

DPDK 应用程序必须通过 Azure 中公开的防故障 PMD 运行。 如果应用程序直接通过 VF PMD 运行，它不会收到发往 VM 的所有包，因为一些包通过综合接口显示。 

通过防故障 PMD 运行 DPDK 应用程序，可保证应用程序收到发往 VM 的所有包。 此外，还能确保应用程序继续以 DPDK 模式运行，即使在为主机提供服务时撤销了 VF，也不例外。 若要详细了解防故障 PMD，请参阅[防故障轮询模式驱动程序库](https://doc.dpdk.org/guides/nics/fail_safe.html)。

## <a name="run-testpmd"></a>运行 testpmd

若要在根模式下运行 testpmd，请在 testpmd 命令前面使用 `sudo`。

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>基本：健全性检查，防故障适配器初始化

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

   若要运行包含超过 2 个 NIC 的 testpmd，`--vdev` 参数采用以下模式：`net_vdev_netvsc<id>,iface=<vf’s pairing eth>`。

3.  启动后，运行 `show port info all` 检查端口信息。 应会看到一个或两个值为 net_failsafe（不是 *net_mlx4*）的 DPDK 端口。
4.  使用 `start <port> /stop <port>` 启动流量。

上面的命令在交互模式下启动 testpmd，这是建议用于试用 testpmd 命令的模式。

### <a name="basic-single-sendersingle-receiver"></a>基本：单个发件人/单一接收方

以下命令定期列显每秒数据包数的统计信息：

1. 在 TX 端运行以下命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
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
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

若要在虚拟机上运行上面的命令，请先将 `app/test-pmd/txonly.c` 中的 IP_SRC_ADDR 和 IP_DST_ADDR 更改为与虚拟机的实际 IP 地址一致，再进行编译。 否则，数据包在抵达接收端之前将被丢弃。

### <a name="advanced-single-sendersingle-forwarder"></a>高级：单个发件人/单转发器
以下命令定期列显每秒数据包数的统计信息：

1. 在 TX 端运行以下命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
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

若要在虚拟机上运行上面的命令，请先将 `app/test-pmd/txonly.c` 中的 IP_SRC_ADDR 和 IP_DST_ADDR 更改为与虚拟机的实际 IP 地址一致，再进行编译。 否则，数据包在抵达转发端之前将被丢弃。 无法使用第三台计算机来接收转发的流量，因为除非做出一些代码更改，否则 testpmd 转发器不会修改第 3 层地址。

## <a name="references"></a>参考

* [EAL 选项](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd 命令](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
