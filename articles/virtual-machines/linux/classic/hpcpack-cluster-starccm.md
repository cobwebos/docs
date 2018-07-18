---
title: 在 Linux VM 上运行 STAR-CCM+ 与 HPC Pack | Microsoft Docs
description: 在 Azure 上部署 Microsoft HPC Pack 群集，并在多个 Linux 计算节点上跨 RDMA 网络运行 STAR-CCM+ 作业。
services: virtual-machines-linux
documentationcenter: ''
author: xpillons
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: 8689d7abfd5ab45277df3b5672a1f6e7e874d88e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841481"
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>在 Azure 中的 Linux RDMA 群集上运行 STAR-CCM+ 和 Microsoft HPC Pack
本文说明如何在 Azure 上部署 Microsoft HPC Pack 群集，并在与 InfiniBand 互连的多个 Linux 计算节点上运行 [CD-adapco STAR-CCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) 作业。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack 可提供在 Microsoft Azure 虚拟机群集上运行各种大型 HPC 和并行应用程序的功能，包括 MPI 应用程序。 HPC Pack 还支持在 HPC Pack 群集中部署的 Linux 计算节点 VM 上运行 Linux HPC 应用程序。 有关将 Linux 计算节点与 HPC Pack 配合使用的简介，请参阅 [Get started with Linux compute nodes in an HPC Pack cluster in Azure](hpcpack-cluster.md)（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）。

## <a name="set-up-an-hpc-pack-cluster"></a>设置 HPC Pack 群集
从[下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=44949)下载 HPC Pack IaaS 部署脚本，并在本地将其解压缩。

Azure PowerShell 是必备组件。 如果未在本地计算机上配置 PowerShell，请阅读 [How to install and configure Azure PowerShell](/powershell/azure/overview)（如何安装和配置 Azure PowerShell）一文。

编写本文时，Azure Marketplace 中的 Linux 映像（包含 Azure 的 InfiniBand 驱动程序）适用于 SLES 12、CentOS 6.5 和 CentOS 7.1。 本文假设使用的是 SLES 12。 若要检索 Marketplace 中支持 HPC 的所有 Linux 映像的名称，可以运行以下 PowerShell 命令：

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

输出将列出提供这些映像的位置以及稍后要在部署模板中使用的映像名称 (**ImageName**)。

在部署群集之前，必须构建 HPC Pack 部署模板文件。 由于我们的目标是小型群集，因此头节点是域控制器并托管本地 SQL 数据库。

以下模板将部署此类头节点，创建名为 **MyCluster.xml** 的 XML 文件，并将 **SubscriptionId**、**StorageAccount**、**Location**、**VMName** 和 **ServiceName** 的值替换为自己的值。

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

在权限提升的命令提示符下运行 PowerShell 命令，以开始创建头节点：

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

20 到 30 分钟后，头节点应准备就绪。 可以在 Azure 门户中单击虚拟机的“连接”图标连接到头节点。

最终可能需要修复 DNS 转发器。 为此，请启动 DNS 管理器。

1. 在 DNS 管理器中右键单击服务器名称，选择“属性”，并单击“转发器”选项卡。
2. 单击“编辑”按钮删除所有转发器，并单击“确定”。
3. 确保已选中“如果没有转发器可用，请使用根提示”复选框，并单击“确定”。

## <a name="set-up-linux-compute-nodes"></a>设置 Linux 计算节点
使用用于创建头节点的同一部署模板来部署 Linux 计算节点。

将文件“MyCluster.xml”从本地计算机复制到头节点，并使用要部署的节点数 (<=20) 更新“NodeCount”标记。 请确保在 Azure 配额中保留足够的可用核心，因为每个 A9 实例将占用订阅中的 16 个核心。 在预算不变的情况下，若要使用更多的 VM，可以使用 A8 实例（8 核心）而不要使用 A9。

在头节点上，复制 HPC Pack IaaS 部署脚本。

在权限提升的命令提示符下运行以下 Azure PowerShell 命令：

1. 运行 **Add-AzureAccount** 连接到 Azure 订阅。
2. 如果有多个订阅，请运行 **Get-AzureSubscription** 列出这些订阅。
3. 运行 **Select-AzureSubscription -SubscriptionName xxxx -Default** 命令设置默认订阅。
4. 运行 **.\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** 开始部署 Linux 计算节点。
   
   ![头节点部署动态][hndeploy]

打开 HPC Pack 群集管理器工具。 几分钟后，Linux 计算节点会规则地显示在群集计算节点列表中。 使用经典部署模式可依序创建 IaaS VM。 因此，如果节点数目十分重要，则部署所有节点可能需要很长时间。

![HPC Pack 群集管理器中的 Linux 节点][clustermanager]

现在，群集中的所有节点都已启动并运行，接下来可进行其他基础结构设置。

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>为 Windows 和 Linux 节点设置 Azure 文件共享
可以使用 Azure 文件服务来存储脚本、应用程序包和数据文件。 Azure 文件在用作持久性存储的 Azure Blob 存储顶层提供 CIFS 功能。 请注意，这不是缩放性最高的解决方案，但它是最简单且不需要专用 VM 的解决方案。

遵循 [Get started with Azure File storage on Windows](../../../storage/files/storage-dotnet-how-to-use-files.md)（在 Windows 上开始使用 Azure 文件存储）一文中的说明创建 Azure 文件共享。

将存储帐户名保留为 **saname**，将文件共享名保留为 **sharename**，将存储帐户密钥保留为 **sakey**。

### <a name="mount-the-azure-file-share-on-the-head-node"></a>在头节点上装载 Azure 文件共享
打开权限提升的命令提示符，然后运行以下命令，将凭据存储在本地计算机保管库中：

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

然后，要装载 Azure 文件共享，请运行：

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>在 Linux 计算节点上装载 Azure 文件共享
HPC Pack 随附的一个有用工具是 clusrun 工具。 可以使用此命令行工具，在一组计算节点上同时运行同一个命令。 在本例中，将使用该工具装载 Azure 文件共享，然后持久保存该文件共享以避免重新启动。
在头节点上的权限提升的命令提示符下运行以下命令。

创建装载目录：

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

装载 Azure 文件共享：

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

持久保存装载共享：

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>安装 STAR-CCM+
Azure VM 实例 A8 和 A9 提供 InfiniBand 支持和 RDMA 功能。 启用这些功能的内核驱动程序适用于 Azure Marketplace 中的 Windows Server 2012 R2、SUSE 12、CentOS 6.5 和 CentOS 7.1 映像。 Microsoft MPI 和 Intel MPI（5.x 版）是 Azure 中可支持这些驱动程序的两个 MPI 库。

CD-adapco STAR-CCM+ 11.x 和更高版本与 Intel MPI 版本5.x 捆绑在一起，因此包含 Azure 的 InfiniBand 支持。

从 [CD-adapco 门户](https://steve.cd-adapco.com)获取 Linux64 STAR-CCM+ 包。 本例使用了混合精度的 11.02.010 版本。

在头节点上的 **/hpcdata** Azure 文件共享中，创建包含以下内容的名为 **setupstarccm.sh** 的 shell 脚本。 此脚本会在每个计算节点上运行，以便在本地设置 STAR-CCM+。

#### <a name="sample-setupstarcmsh-script"></a>示例 setupstarcm.sh 脚本
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
现在，若要在所有 Linux 计算节点上设置 STAR-CCM+，请打开权限提升的命令提示符并运行以下命令：

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

运行该命令时，可以使用群集管理器的热度地图来监视 CPU 使用率。 几分钟后，所有节点应已正确设置。

## <a name="run-star-ccm-jobs"></a>运行 STAR-CCM+ 作业
将 HPC Pack 与其作业计划程序功能配合使用可以运行 STAR-CCM+ 作业。 为此，我们需要支持几个用于启动作业和运行 STAR-CCM+ 的脚本。 为方便起见，我们首先将输入数据保留在 Azure 文件共享上。

以下 PowerShell 脚本用于将 STAR-CCM+ 作业排入队列。 该脚本使用了三个参数：

* 模型名称
* 要使用的节点数
* 要在每个节点上使用的核心数

由于 STAR-CCM+ 可能会占用内存带宽，因此，通常更好的做法是在每个计算节点上使用较少的核心和添加新节点。 每个节点的确切核心数取决于处理器系列和互连速度。

节点是专门为该作业分配的，不能与其他作业共享。 该作业不会直接作为 MPI 作业启动。 **runstarccm.sh** shell 脚本将启动 MPI 启动器。

输入模型和 **runstarccm.sh** 脚本存储在前面装载的 **/hpcdata** 共享中。

日志文件以作业 ID 命名，并连同 STAR-CCM+ 输出文件一起存储在 **/hpcdata 共享**中。

#### <a name="sample-submitstarccmjobps1-script"></a>示例 SubmitStarccmJob.ps1 脚本
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
将 **runner.java** 替换为首选的 STAR-CCM+ Java 模型启动器和日志记录代码。

#### <a name="sample-runstarccmsh-script"></a>示例 runstarccm.sh 脚本
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

在测试中，我们使用了 Power-On-Demand 许可证令牌。 对于该令牌，必须在命令行的 **-podkey** 选项中将 **$CDLMD_LICENSE_FILE** 环境变量设置为 **1999@flex.cd-adapco.com** 和密钥。

在完成一些初始化之后，该脚本将从 HPC Pack 设置的 **$CCP_NODES_CORES** 环境变量中提取节点列表，构建 MPI 启动器使用的 hostfile。 此 hostfile 将包含作业使用的计算节点名称列表（每行包含一个名称）。

**$CCP_NODES_CORES** 的格式遵循以下模式：

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

其中：

* `<Number of nodes>` 是分配给此作业的节点数。
* `<Name of node_n_...>` 是分配给此作业的每个节点的名称。
* `<Cores of node_n_...>` 是分配给此作业的节点上的核心数。

核心数目 (**$NBCORES**) 也是根据节点数目 (**$NBNODES**) 和每个节点的核心数目（以 **$NBCORESPERNODE** 参数的形式提供）计算得出的。

在 Azure 上配合 Intel MPI 使用的 MPI 选项如下：

* `-mpi intel`：指定 Intel MPI。
* `-fabric UDAPL`：使用 Azure InfiniBand 谓词。
* `-cpubind bandwidth,v`：使用 STAR-CCM+ 优化 MPI 的带宽。
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`：让 Intel MPI 与 Azure InfiniBand 配合使用，以及设置每个节点的所需核心数。
* `-batch`：在批处理模式下（不使用 UI）启动 STAR-CCM+。

最后，若要启动作业，请确保节点已启动并运行，并已在群集管理器中联机。 然后在 PowerShell 命令提示符下运行以下命令：

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>停止节点
稍后在完成测试后，可以使用以下 HPC Pack PowerShell 命令来停止和启动节点：

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>后续步骤
尝试运行其他 Linux 工作负荷。 有关示例，请参阅：

* [在 Azure 中的 Linux 计算节点上使用 Microsoft HPC Pack 运行 NAMD](hpcpack-cluster-namd.md)
* [在 Azure 中的 Linux RDMA 群集上运行 OpenFoam 和 Microsoft HPC Pack](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png
