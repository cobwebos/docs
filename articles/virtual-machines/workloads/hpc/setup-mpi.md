---
title: 为 HPC-Azure 虚拟机设置消息传递接口 |Microsoft Docs
description: 了解如何设置 Azure 上的 HPC MPI。
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
ms.openlocfilehash: 1717c0d95c00e13ae0d87ab920bc88cd4f0df978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809837"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>为 HPC 设置消息传递接口

消息传递接口 (MPI) 工作负荷是传统的 HPC 工作负荷的重要组成部分。 在 Azure 上的已启用的 VM 大小允许 MPI 使用几乎任何风格 SR IOV。 

在 Vm 上运行 MPI 作业需要跨租户的设置的分区键 （p 键）。 按照中的步骤[发现分区键](#discover-partition-keys)有关确定 p 键值的详细信息部分。

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx)提供 IB 且适用于 MPICH 和 OpenMPI 的最佳性能。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

安装 UCX 按前面所述。

```bash
sudo yum install –y openmpi
```

生成 OpenMPI。

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

运行 OpenMPI。

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

检查分区键，如前面所述。

## <a name="mpich"></a>MPICH

安装 UCX 按前面所述。

生成 MPICH。

```bash
wget http://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

运行 MPICH。

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

检查分区键，如前面所述。

## <a name="mvapich2"></a>MVAPICH2

生成 MVAPICH2。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

运行 MVAPICH2。

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>平台 MPI 社区版

为平台 MPI 安装所需的包。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

按照安装过程。

## <a name="intel-mpi"></a>Intel MPI

[下载 Intel MPI](https://software.intel.com/mpi-library/choose-download)。

更改 I_MPI_FABRICS 环境变量，具体取决于版本。 对于 Intel MPI 2018，请使用`I_MPI_FABRICS=shm:ofa`; 对于 2019，请使用`I_MPI_FABRICS=shm:ofi`。

进程固定正常工作的 15、 30 和 60 PPN 默认情况下。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基准

[下载 OSU MPI 基准][ http://mvapich.cse.ohio-state.edu/benchmarks/ ](http://mvapich.cse.ohio-state.edu/benchmarks/)和解压缩。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

生成基准使用特定的 MPI 库：

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 基准正在`mpi/`文件夹。


## <a name="discover-partition-keys"></a>发现分区键

与其他 Vm 通信发现分区键 （p 键）。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

较大的两个是对于 MPI 应使用的租户密钥。 示例：如果以下为 p 键，则应使用 MPI 使用 0x800b。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

使用非默认 (0x7fff) 分区键的分区。 UCX 要求 p 键来清除的 MSB。 例如，作为为 0x800b 0x000b 设置 UCX_IB_PKEY。


## <a name="set-up-user-limits-for-mpi"></a>为 MPI 设置用户限制

为 MPI 设置用户的限制。

```bash
cat << EOF >> /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>为 MPI 设置 SSH 密钥

设置 SSH 密钥进行需要它的 MPI 类型。

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

上述语法假定共享主目录，必须将其他.ssh 目录复制到每个节点。

## <a name="next-steps"></a>后续步骤

详细了解如何[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
