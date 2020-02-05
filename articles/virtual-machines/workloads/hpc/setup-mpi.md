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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023984"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>设置 HPC 的消息传递接口

消息传递接口（MPI）工作负荷是传统 HPC 工作负荷的重要组成部分。 在 Azure 上启用 SR-IOV 的 VM 大小几乎可以使用任何适用于 MPI 的风格。 

如果在 Vm 上运行 MPI 作业，则需要在租户中设置分区键（p 键）。 按照 "[发现分区密钥](#discover-partition-keys)" 部分中的步骤，了解有关如何确定 p 键值的详细信息。

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx)为 IB 提供最佳性能，并与 MPICH 和 OpenMPI 结合使用。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

如前文所述安装 UCX。

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

按如上所述检查分区键。

## <a name="mpich"></a>MPICH

如前文所述安装 UCX。

生成 MPICH。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

运行 MPICH。

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

按如上所述检查分区键。

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

安装平台 MPI 所需的包。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

按照安装过程操作。

## <a name="intel-mpi"></a>Intel MPI

[下载 INTEL MPI](https://software.intel.com/mpi-library/choose-download)。

根据版本更改 I_MPI_FABRICS 环境变量。 对于 Intel MPI 2018，请使用 `I_MPI_FABRICS=shm:ofa`，对于2019，请使用 `I_MPI_FABRICS=shm:ofi`。

默认情况下，进程固定适用于15、30和 60 PPN。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基准

[下载 OSU MPI 基准](http://mvapich.cse.ohio-state.edu/benchmarks/)和解压缩。

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

MPI 基准在 `mpi/` 文件夹下。


## <a name="discover-partition-keys"></a>发现分区键

发现用于与同一租户中的其他 Vm （可用性集或 VM 规模集）进行通信的分区键（p 键）。

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

使用除 default （0x7fff）分区键以外的分区。 UCX 要求清除 p 键的 MSB。 例如，将 UCX_IB_PKEY 设置为0x800b 的0x000b。

另请注意，只要租户（AVSet 或 VMSS）存在，PKEYs 将保持不变。 即使添加/删除节点也是如此。 新租户获取不同的 PKEYs。


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

了解有关 Azure 上的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的详细信息。
