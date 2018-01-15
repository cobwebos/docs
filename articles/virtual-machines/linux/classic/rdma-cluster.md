---
title: "设置 Linux RDMA 群集以运行 MPI 应用程序 | Microsoft 文档"
description: "创建 H16r、H16mr、A8 或 A9 大小 VM 的 Linux 群集，以便使用 Azure RDMA 网络运行 MPI 应用"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: e09b472a53c02b39bcf7ad06d228049b0a392452
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2018
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>设置 Linux RDMA 群集以运行 MPI 应用程序
了解如何在 Azure 中使用[高性能计算 VM 大小](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)设置 Linux RDMA 群集，以运行并行消息传递接口 (MPI) 应用程序。 本文提供准备 Linux HPC 映像以便在群集上运行 Intel MPI 的步骤。 准备映像后，使用此映像和支持 RDMA 的 Azure VM 大小（当前为 H16r、H16mr、A8 或 A9）之一来部署 VM 群集。 可以使用该群集来运行通过基于远程直接内存访问 (RDMA) 技术的低延迟、高吞吐量网络有效进行通信的 MPI 应用程序。

> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Azure 资源管理器](../../../resource-manager-deployment-model.md)和经典。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

## <a name="cluster-deployment-options"></a>群集部署选项
以下是可用于创建 Linux RDMA 群集的方法（使用或不使用作业计划程序）。

* **Azure CLI 脚本**：如本文后面所示，使用 [Azure 命令行接口](../../../cli-install-nodejs.md) (CLI) 为支持 RDMA 的 VM 群集部署编写脚本。 服务管理模式下的 CLI 会在经典部署模型中连续创建群集节点，因此，如果要部署许多计算节点，可能需要几分钟。 使用经典部署模型时，要启用 RDMA 网络连接，请将 VM 部署在相同的云服务中。
* **Azure 资源管理器模板**：也可以使用资源管理器部署模型部署连接到 RDMA 网络且支持 RDMA 的 VM 群集。 可以[创建自己的模板](../../../resource-group-authoring-templates.md)，或查看 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)，获取 Microsoft 或社区贡献的模板来部署所需的解决方案。 Resource Manager 模板可以提供快速可靠的方式来部署 Linux 群集。 使用 Resource Manager 部署模型时，要启用 RDMA 网络连接，请将 VM 部署在相同的可用性集中。
* **HPC Pack**：在 Azure 中创建 Microsoft HPC Pack 群集，并添加运行受支持 Linux 分发版且支持 RDMA 的计算节点，以便访问 RDMA 网络。 有关详细信息，请参阅 [Azure 的 HPC Pack 群集中的 Linux 计算节点入门](hpcpack-cluster.md)。

## <a name="sample-deployment-steps-in-the-classic-model"></a>经典模型中的示例部署步骤
下列步骤显示如何使用 Azure CLI 从 Azure Marketplace 部署 SUSE Linux Enterprise Server (SLES) 12 SP1 HPC VM、将它自定义，并创建自定义 VM 映像。 然后，可以使用该映像为支持 RDMA 的 VM 群集部署编写脚本。

> [!TIP]
> 使用类似的步骤，根据 Azure Marketplace 中基于 CentOS 的 HPC 映像来部署支持 RDMA 的 VM 群集。 如前所述，某些步骤稍有不同。 
>
>

### <a name="prerequisites"></a>系统必备
* **客户端计算机**：需要 Mac、Linux 或 Windows 客户端计算机，以便与 Azure 通信。 这些步骤假定你使用的是 Linux 客户端。
* **Azure 订阅**：如果没有订阅，只需要花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/free/)。 对于较大的群集，请考虑即用即付订阅或其他购买选项。
* **VM 大小可用性**：以下实例大小支持 RDMA：H16r、H16mr、A8 和 A9。 有关各 Azure 区域推出的产品，请查看 [Products available by region](https://azure.microsoft.com/regions/services/)（按区域提供的产品）。
* **核心配额**：可能需要提高核心配额才能部署计算密集型 VM 群集。 例如，如果要按本文所示部署 8 个 A9 VM，则至少需要 128 个核心。 订阅可能也会限制可在特定 VM 大小系列（包括 H 系列）中部署的核心数目。 若要请求提高配额，可免费[提出在线客户支持请求](../../../azure-supportability/how-to-create-azure-support-request.md)。
* **Azure CLI**：[安装](../../../cli-install-nodejs.md) Azure CLI 并从客户端计算机[连接到 Azure 订阅](/cli/azure/authenticate-azure-cli)。

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>预配 SLES 12 SP1 HPC VM
使用 Azure CLI 登录到 Azure 后，运行 `azure config list` 确认输出显示服务管理模式。 如果未显示，请通过运行以下命令设置模式：

    azure config mode asm


键入以下命令列出有权使用的所有订阅：

    azure account list

通过将 `Current` 设为 `true` 来标识当前的活动订阅。 如果此订阅不是用于创建群集的订阅，请将相应的订阅 ID 设为活动订阅：

    azure account set <subscription-Id>

若要查看 Azure 中公开提供的 SLES 12 SP1 HPC 映像，请运行如下命令（假设 shell 环境支持 **grep**）：

    azure vm image list | grep "suse.*hpc"

运行如下命令，使用 SLES 12 SP1 HPC 映像预配支持 RDMA 的 VM：

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

其中：

* 大小（本示例中为 A9）是支持 RDMA 的 VM 大小之一。
* 外部 SSH 端口号（在此示例中为 SSH 默认值 22）是任何有效的端口号。 内部 SSH 端口号设置为 22。
* 在按位置指定的 Azure 区域中创建新的云服务。 指定支持所选 VM 大小的位置。
* 如需 SUSE 优先支持（会产生附加费用），SLES 12 SP1 映像名称当前可以是以下两个选项之一： 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>自定义 VM
在 VM 完成预配后，使用 VM 的外部 IP 地址（或 DNS 名称）和已配置的外部端口号通过 SSH 登录到 VM，并对其进行自定义。 有关连接详细信息，请参阅[如何登录到运行 Linux 的虚拟机](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 必须以 VM 上配置的用户身份执行命令，除非必须要根访问权限才能完成步骤。

> [!IMPORTANT]
> Microsoft Azure 不提供对 Linux VM 的根访问权限。 若要在以用户身份连接至 VM 时获得管理访问权限，请使用 `sudo` 运行命令。
>
>

* **更新**：使用 zypper 安装更新。 可能想要安装 NFS 实用工具。

  > [!IMPORTANT]
  > 在 SLES 12 SP1 HPC VM 中，建议不要应用内核更新，否则可能会在使用 Linux RDMA 驱动程序时导致问题。
  >
  >
* **Intel MPI**：在 SLES 12 SP1 HPC VM 上运行以下命令，完成 Intel MPI 的安装：

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **锁定内存**：若要使 MPI 代码锁定可用于 RDMA 的内存，请在 /etc/security/limits.conf 文件中添加或更改以下设置。 需要根访问权限才能编辑该文件。

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > 出于测试目的，也可以将 memlock 设置为不受限制。 例如：`<User or group name>    hard    memlock unlimited`。 有关详细信息，请参阅 [Best known methods for setting locked memory size](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size)（设置锁定内存大小的最佳已知方法）。
  >
  >
* **SLES VM 的 SSH 密钥**：生成 SSH 密钥，以便运行 MPI 作业时，在 SLES 群集的计算节点上为用户帐户建立信任。 如果部署了基于 CentOS 的 HPC VM，请不要遵循此步骤。 请参阅本文稍后的说明，以便在捕获映像并部署群集之后，在群集节点之间设置无密码 SSH 信任。

    若要创建 SSH 密钥，请运行以下命令。 系统提示输入时，选择 **Enter** 即可在默认位置生成密钥，而无需设置密码。

        ssh-keygen

    对于已知的公钥，将公钥附加到 authorized_keys 文件。

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    在 ~/.ssh 目录中编辑或创建 config 文件。 提供计划在 Azure 中使用的专用网络的 IP 地址范围（本例中为 10.32.0.0/16）：

        host 10.32.0.*
        StrictHostKeyChecking no

    或者，按以下方式列出群集中每个 VM 的专用网络 IP 地址：

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > 如果未指定特定 IP 地址或范围，配置 `StrictHostKeyChecking no` 就会产生潜在的安全风险。
  >
  >
* **应用程序**：在捕获映像之前，请安装所需的所有应用程序或执行其他自定义设置。

### <a name="capture-the-image"></a>捕获映像
若要捕获映像，首先在 Linux VM 中运行以下命令。 此命令会取消预配 VM，但会维护所设置的用户帐户和 SSH 密钥。

```
sudo waagent -deprovision
```

从客户端计算机运行以下 Azure CLI 命令，以捕获映像。 有关详细信息，请参阅[如何捕获用作映像的经典 Linux 虚拟机](capture-image.md)。  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

运行这些命令之后，将捕获 VM 映像供你使用并删除 VM。 现已准备好用于部署群集的自定义映像。

### <a name="deploy-a-cluster-with-the-image"></a>使用映像部署群集
使用环境的相应值修改以下 Bash 脚本，并从客户端计算机运行它。 由于 Azure 在经典部署模型中依序部署 VM，因此将需要几分钟时间才能部署此脚本中建议的八个 A9 VM。

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>CentOS HPC 群集注意事项
如果想要根据 Azure Marketplace 中基于 CentOS 的 HPC 映像之一（而不是 SLES 12 for HPC）来设置群集，请根据上一部分中的大致步骤操作。 预配和配置 VM 时，请注意以下差异：

- Intel MPI 已在从基于 CentOS 的 HPC 映像预配的 VM 上安装。
- 锁定内存设置已在 VM 的 /etc/security/limits.conf 文件中添加。
- 不要在针对捕获预配的 VM 上生成 SSH 密钥。 而是建议在部署群集后设置基于用户的身份验证。 有关详细信息，请参阅以下部分。  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>在群集上设置无密码 SSH 信任
在基于 CentOS 的 HPC 群集上，可通过两种方法在计算节点之间建立信任：基于主机的身份验证和基于用户的身份验证。 基于主机的身份验证超出了本文的范围，通常必须在部署期间通过扩展脚本来完成。 基于用户的身份验证便于在部署之后建立信任，并且需要在群集中的计算节点之间生成和共享 SSH 密钥。 此方法通常称为无密码 SSH 登录，在运行 MPI 作业时必须使用此方法。

[GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) 中提供了社区所贡献的示例脚本，以便在基于 CentOS 的 HPC 群集上启用方便的用户身份验证。 可以使用以下步骤来下载和使用此脚本。 还可以修改此脚本，或使用任何其他方法在群集计算节点之间建立无密码 SSH 身份验证。

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh

若要运行此脚本，需要知道子网 IP 地址的前缀。 可以通过在某个群集节点上运行以下命令来获取此前缀。 输出看起来应类似于 10.1.3.5，其中的 10.1.3 部分就是前缀。

    ifconfig eth0 | grep -w inet | awk '{print $2}'

现在，请使用三个参数运行脚本：计算节点上的公用用户名、该用户在计算节点上的公用密码，以及从上一个命令返回的子网前缀。

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

此脚本执行以下任务：

* 在主机节点上创建名为 .ssh 的目录，进行无密码登录时需要用到该目录。
* 在 .ssh 目录中创建一个配置文件，用于指示无密码登录允许从群集中的任何节点登录。
* 创建包含群集中所有节点的节点名称与节点 IP 地址的文件。 运行脚本后，将保留这些文件供以后参考。
* 为每个群集节点（包括主机节点）创建私钥和公钥对，并在 authorized_keys 文件中创建相关条目。

> [!WARNING]
> 运行此脚本可能会造成潜在的安全风险。 请确保不要分发 ~/.ssh 中的公钥信息。
>
>

## <a name="configure-intel-mpi"></a>配置 Intel MPI
要在 Azure Linux RDMA 上运行 MPI 应用程序，需要配置特定于 Intel MPI 的某些环境变量。 下面是用于配置运行应用程序时所需变量的示例 Bash 脚本。 根据 Intel MPI 安装需要来更改 mpivars.sh 的路径。

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

主机文件的格式如下所示。 针对群集中的每个节点添加一行。 指定前面定义的虚拟网络中的专用 IP 地址，而不是 DNS 名称。 例如，对于两台 IP 地址为 10.32.0.1 和 10.32.0.2 的主机，文件包含以下信息：

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>在基本双节点群集上运行 MPI
如果尚未这样做，先为 Intel MPI 设置环境。

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>运行 MPI 命令
在其中一个计算节点上运行 MPI 命令，以显示 MPI 已正确安装并且可以在至少两个计算节点之间通信。 以下 **mpirun** 命令在两个节点上运行 **hostname** 命令。

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
输出应列出你传递作为 `-hosts` 输入的所有节点的名称。 例如，使用两个节点的 **mpirun** 命令将返回类似如下内容的输出：

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>运行 MPI 基准
以下 Intel MPI 命令运行 pingpong 基准来验证群集配置和 RDMA 网络连接。

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

在包含两个节点的工作群集上，应会看到类似如下内容的输出。 在 Azure RDMA 网络上，不超过 512 字节的消息可能会延迟 3 微秒或 3 微秒以下。

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>后续步骤
* 在 Linux 群集上部署和运行 Linux MPI 应用程序。
* 有关 Intel MPI 的指南，请参阅 [Intel MPI 库文档](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/)。
* 尝试使用基于 CentOS 的 HPC 映像通过[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos)创建 Intel Lustre 群集。 有关详细信息，请参阅[在 Microsoft Azure 上部署用于 Lustre 的 Intel 云版本](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/)。
