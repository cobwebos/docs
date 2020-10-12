---
title: 设置 HPC 的消息传递接口-Azure 虚拟机 |Microsoft Docs
description: 了解如何在 Azure 上为 HPC 设置 MPI。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 210b2935cd2df81b0ff079c9a1c945fe770933f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87926512"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>设置 HPC 的消息传递接口

[ (MPI) 的消息传递接口](https://en.wikipedia.org/wiki/Message_Passing_Interface)是开放库，而分布式内存并行化的标准是事实。 它通常用于许多 HPC 工作负荷。 [支持 RDMA](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)虚拟机上的 HPC 工作负荷可以使用 MPI 通过低延迟和高带宽负载网络进行通信。

Azure (HBv2、HB-ACCT-WC、HC、NCv3、NDv2) 上启用了 SR-IOV 的 VM 大小，几乎可以使用任何适用于 Mellanox OFED 的 MPI 的任何风格。 在非 SR-IOV 启用 Vm 上，支持的 MPI 实现使用 Microsoft Network Direct (ND) 接口在 Vm 之间进行通信。 因此，只支持 Microsoft MPI (MS-MPI) 2012 R2 或更高版本和 Intel MPI 1.x 版本。  (2017，2018) 的 Intel MPI 运行时库的更高版本可能与 Azure RDMA 驱动程序兼容或不兼容。

对于启用了 SR-IOV 的支持 [rdma 的 vm](../../sizes-hpc.md#rdma-capable-instances)，Marketplace 中的 [CentOS 版本7.6 或更高](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 版本 vm 映像经过了优化，并预先加载了用于 rdma 的 OFED 驱动程序以及各种常用的 MPI 库和科学计算包，并且是入门的最简单的方法。

尽管此处的示例适用于 RHEL/CentOS，但步骤是一般的，可用于任何兼容的 Linux 操作系统，如 Ubuntu (16.04、18.04 19.04、20.04) 和 SLES (12 SP4 和 15) 。 有关在其他发行版上设置其他 MPI 实现的更多示例，请查看 [azhpc](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)存储库。

> [!NOTE]
> 若要在启用 SR-IOV 的虚拟机上运行 MPI 作业，需要在租户中设置分区键 () ，以实现隔离和安全性。 按照 " [发现分区密钥](#discover-partition-keys) " 部分中的步骤，了解有关如何确定 p 值并为 MPI 作业正确设置这些值的详细信息。

## <a name="ucx"></a>UCX

[统一通信 X (UCX) ](https://github.com/openucx/ucx) 是适用于 HPC 的通信 api 的框架。 它经过优化，可针对不符合的 MPI 通信进行优化，并可与 OpenMPI 和 MPICH 之类的许多 MPI 实现一起使用。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

[HPC X 软件工具包](https://www.mellanox.com/products/hpc-x-toolkit)包含 UCX 和 HCOLL。

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

运行 HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

如上所述安装 UCX。 HCOLL 是 [HPC-X 软件工具包](https://www.mellanox.com/products/hpc-x-toolkit) 的一部分，无需特殊安装。

从存储库中提供的包中安装 OpenMPI。

```bash
sudo yum install –y openmpi
```

生成 OpenMPI。

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

运行 OpenMPI。

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

按如上所述检查分区键。

## <a name="intel-mpi"></a>Intel MPI

下载你选择的 [INTEL MPI](https://software.intel.com/mpi-library/choose-download)版本。 根据版本更改 I_MPI_FABRICS 环境变量。 对于 Intel MPI 2018，使用 `I_MPI_FABRICS=shm:ofa` 适用于2019的，使用 `I_MPI_FABRICS=shm:ofi` 。

### <a name="non-sr-iov-vms"></a>非 SR-IOV Vm
对于非 SR-IOV Vm，下载4.x 运行时 [免费评估版本](https://registrationcenter.intel.com/en/forms/?productid=1740) 的示例如下所示：
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
有关安装步骤，请参阅 [Intel MPI 库安装指南](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)。
或者，您可能想要为 (的非根非调试器进程启用 ptrace，这是最新版本的 Intel MPI) 所必需的。
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
对于 SUSE Linux Enterprise Server VM 映像版本-SLES 12 SP3 for hpc、SLES 12 SP3 for hpc (Premium) 、SLES 12 SP1 for hpc、SLES 12 SP1 for HPC (Premium) 、SLES 12 SP4 和 SLES 15，安装了 RDMA 驱动程序，并在 VM 上分发 Intel MPI 包。 通过运行以下命令安装 Intel MPI：
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH

如上所述安装 UCX。 生成 MPICH。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

运行 MPICH。

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

按如上所述检查分区键。

## <a name="mvapich2"></a>MVAPICH2

生成 MVAPICH2。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

运行 MVAPICH2。

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>平台 MPI 社区版

安装平台 MPI 所需的包。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

按照安装过程操作。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基准

下载 [OSU MPI 基准](http://mvapich.cse.ohio-state.edu/benchmarks/) 和解压缩。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

使用特定的 MPI 库生成基准：

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 基准位于 `mpi/` 文件夹下。


## <a name="discover-partition-keys"></a>发现分区键

发现分区键 (p 键) 以便与同一租户 (可用性集或虚拟机规模集) 中的其他 Vm 进行通信。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

这二者中较大的一个是应与 MPI 一起使用的租户密钥。 示例：如果以下是 p 键，则应将0x800b 与 MPI 一起使用。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

使用 () 分区键以外的其他分区。 UCX 要求清除 p 键的 MSB。 例如，将 UCX_IB_PKEY 设置为0x800b 的0x000b。

另请注意，只要租户 (可用性集或虚拟机规模集) 存在，PKEYs 将保持不变。 即使添加/删除节点也是如此。 新租户获取不同的 PKEYs。


## <a name="set-up-user-limits-for-mpi"></a>为 MPI 设置用户限制

为 MPI 设置用户限制。

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>为 MPI 设置 SSH 密钥

为需要它的 MPI 类型设置 SSH 密钥。

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

上述语法采用共享主目录，否则，必须将 ssh 目录复制到每个节点。

## <a name="next-steps"></a>后续步骤

- 了解如何 [启用不允许](../../sizes-hpc.md#rdma-capable-instances)的 [H 系列](../../sizes-hpc.md) 和 [N 系列](../../sizes-gpu.md) vm
- 查看 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)，以了解如何对工作负载进行优化配置以提高性能和可伸缩性。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
