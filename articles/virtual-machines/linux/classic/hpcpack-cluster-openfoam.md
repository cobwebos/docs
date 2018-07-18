---
title: 在 Linux VM 上运行 OpenFOAM 与 HPC Pack | Microsoft Docs
description: 在 Azure 上部署 Microsoft HPC Pack 群集，并在多个 Linux 计算节点上跨 RDMA 网络运行 OpenFOAM 作业。
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
ms.openlocfilehash: 73ad78fc73a7605f8feaf114ebdfac5023cc91b6
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342414"
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>在 Azure 中的 Linux RDMA 群集上运行 OpenFoam 和 Microsoft HPC Pack
本文介绍在 Azure 虚拟机中运行 OpenFoam 的一种方法。 此处，会在 Azure 上部署一个具有 Linux 计算节点的 Microsoft HPC Pack 群集，并使用 Intel MPI 来运行 [OpenFoam](http://openfoam.com/) 作业。 可以使用支持 RDMA 的 Azure VM 作为计算节点，使计算节点能够通过 Azure RDMA 网络进行通信。 在 Azure 中运行 OpenFoam 的其他方法包括使用市场中提供的经过完全配置的商用映像（例如 UberCloud 的 [OpenFoam 2.3 on CentOS 6](https://azuremarketplace.microsoft.com/marketplace/apps/cfd-direct.cfd-direct-from-the-cloud)），以及在 [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) 上运行该产品。 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM（Open Field Operation and Manipulation 的缩写）是一个开源计算流体动力学 (CFD) 软件包，广泛用于商业和学术组织的工程和科学项目中。 它包括各种网格工具，最主要的工具是 snappyHexMesh，这是一个并行处理式网格器，适用于复杂的 CAD 几何以及预处理和后处理。 几乎所有进程都是并行运行的，因此用户可以根据自己的需要充分利用计算机硬件。  

Microsoft HPC Pack 可提供在 Microsoft Azure 虚拟机群集上运行大型 HPC 和并行应用程序的功能，包括 MPI 应用程序。 HPC Pack 还支持在 HPC Pack 群集中部署的 Linux 计算节点 VM 上运行 Linux HPC 应用程序。 有关将 Linux 计算节点与 HPC Pack 一起使用的简介，请参阅 [Azure 的 HPC Pack 群集中的 Linux 计算节点入门](hpcpack-cluster.md)。

> [!NOTE]
> 本文演示如何使用 HPC Pack 运行 Linux MPI 工作负荷。 本文假设读者已对 Linux 系统管理以及如何在 Linux 群集上运行 MPI 工作负荷有所了解。 如果使用的 MPI 和 OpenFOAM 版本与本文中提到的版本不同，则可能需要修改一些安装和配置步骤。 
> 
> 

## <a name="prerequisites"></a>先决条件
* **包含支持 RDMA 的 Linux 计算节点的 HPC Pack 群集** - 使用 [Azure 资源管理器模板](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)或 [Azure PowerShell 脚本](hpcpack-cluster-powershell-script.md)，部署包含 A8、A9、H16r 或 H16rm 大小 Linux 计算节点的 HPC Pack 群集。 有关任一选项的先决条件和步骤，请参阅 [Get started with Linux compute nodes in an HPC Pack cluster in Azure](hpcpack-cluster.md)（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）。 如果选择 Powershell 脚本部署选项，请参阅本文末尾的示例文件中的示例配置文件。 使用此配置可以部署基于 Azure 的 HPC Pack 群集，群集中包含一个 A8 大小的 Windows Server 2012 R2 头节点和两个 A8 大小的 SUSE Linux Enterprise Server 12 计算节点。 请将订阅和服务名称替换为相应值。 
  
  **其他须知项**
  
  * 有关 Azure 中 Linux RDMA 网络的先决条件，请参阅[高性能计算 VM 大小](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
  * 如果使用 Powershell 脚本部署选项，请将所有 Linux 计算节点部署在一个云服务中，以使用 RDMA 网络连接。
  * 部署 Linux 节点后，请通过 SSH 建立连接，执行其他任何管理任务。 可以在 Azure 门户中找到每个 Linux VM 的 SSH 连接详细信息。  
* **Intel MPI** - 若要在 Azure 中的 SLES 12 HPC 计算节点上运行 OpenFOAM，需要从 [Intel.com 站点](https://software.intel.com/en-us/intel-mpi-library/)安装 Intel MPI Library 5 运行时。 （基于 CentOS 的 HPC 映像中已预装 Intel MPI 5）。在后面的步骤中，可以根据需要在 Linux 计算节点上安装 Intel MPI。 为此，向 Intel 注册后，请点击确认电子邮件中的链接转到相关网页。 然后，复制相应 Intel MPI 版本对应的 .tgz 文件的下载链接。 本文基于 Intel MPI 版本 5.0.3.048。
* **OpenFOAM Source Pack** - 从 [OpenFOAM Foundation 站点](http://openfoam.org/download/2-3-1-source/)下载 Linux 版 OpenFOAM Source Pack 软件。 本文基于 2.3.1 版的 Source Pack（以 OpenFOAM-2.3.1.tgz 形式供用户下载）。 请按本文后面的说明，在 Linux 计算节点上对 OpenFOAM 进行解包和编译。
* **EnSight**（可选）- 若要查看 OpenFOAM 模拟结果，请下载并安装 [EnSight](https://www.ceisoftware.com/download/) 可视化和分析程序。 EnSight 站点提供了许可和下载信息。

## <a name="set-up-mutual-trust-between-compute-nodes"></a>在计算节点之间建立互信关系
在多个 Linux 节点上运行跨节点作业需要节点彼此信任（通过 **rsh** 或 **ssh**）。 使用 Microsoft HPC Pack IaaS 部署脚本创建 HPC Pack 群集时，此脚本会自动为指定的管理员帐户建立永久性互信关系。 对于在群集域中创建的非管理员用户，分配作业时必须在节点之间建立临时互信关系，并在作业完成后销毁互信关系。 若要为每个用户建立信任，请向群集提供一个 HPC Pack 用来建立信任关系的 RSA 密钥对。

### <a name="generate-an-rsa-key-pair"></a>生成一个 RSA 密钥对
通过运行 Linux **ssh keygen** 命令，可以很容易生成一个 RSA 密钥对，其中包含一个公钥和一个私钥。

1. 登录到 Linux 计算机。
2. 运行以下命令：
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > 按 **Enter** 使用默认设置，直至命令完成。 请勿在此处输入密码；系统提示输入密码时，只需按 **Enter** 即可。
   > 
   > 
   
   ![生成一个 RSA 密钥对][keygen]
3. 将目录切换到 ~/.ssh 目录。 私钥存储在 id_rsa 中，公钥存储在 id_rsa.pub 中。
   
   ![私钥和公钥][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>将密钥对添加到 HPC Pack 群集中
1. 使用 HPC Pack 管理员帐户（运行部署脚本时设置的管理员帐户），以远程桌面连接方式连接至头节点。
2. 使用 Windows Server 标准程序，在群集的 Active Directory 域中创建一个域用户帐户。 例如，在头节点上使用 Active Directory 用户和计算机工具。 本文中的示例假设你创建了一个名为 hpclab\hpcuser 域用户。
3. 创建一个名为 C:\cred.xml 的文件，将 RSA 密钥数据复制到此文件中。 本文末尾提供了示例 cred.xml 文件。
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. 打开命令提示符，输入以下命令，为 hpclab\hpcuser 帐户设置凭据数据。 使用 **extendeddata** 参数传递针对关键数据创建的 C:\cred.xml 文件的名称。
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   此命令成功完成后，没有输出。 为需要运行作业的用户帐户设置凭据后，请将 cred.xml 文件存储在安全位置，或者删除 cred.xml 文件。
5. 如果在一个 Linux 节点上生成 RSA 密钥对，请记住在使用完成后删除这些密钥。 如果找到现有的 id_rsa 文件或 id_rsa.pub 文件，HPC Pack 不会建立互信关系。

> [!IMPORTANT]
> 我们不建议在共享群集上以群集管理员的身份运行 Linux 作业，因为由管理员提交的作业会在 Linux 节点的根帐户下运行。 但是，由非管理员用户提交的作业会在本地 Linux 用户帐户（名称与作业用户相同）下运行。 在这种情况下，HPC Pack 会在该作业分配到的所有节点中为这位 Linux 用户建立互信关系。 在运行作业之前，可以在 Linux 节点上手动设置 Linux 用户，也可以在提交作业时由 HPC Pack 自动创建用户。 如果 HPC Pack 创建了一个用户，则作业完成后 HPC Pack 会删除此用户。 为了减少安全威胁，HPC Pack 会在完成作业后删除密钥。
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>为 Linux 节点设置文件共享
现在，在头节点上对某个文件夹设置标准 SMB 共享。 若要允许 Linux 节点使用通用路径访问应用程序文件，请在 Linux 节点上装入共享文件夹。 可以根据需要使用其他文件共享选项，例如 Azure 文件共享（建议用于多种方案）或 NFS 共享。 请参阅 [Get started with Linux compute nodes in an HPC Pack Cluster in Azure](hpcpack-cluster.md)（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）中的文件共享信息和详细步骤。

1. 在头节点上创建一个文件夹，并通过设置读/写权限将其与所有人共享。 例如，在头节点上将 C:\OpenFOAM 共享为 \\\\SUSE12RDMA-HN\OpenFOAM。 在此处，*SUSE12RDMA-HN* 是头节点的主机名。
2. 打开 Windows PowerShell 窗口并运行以下命令：
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

第一个命令在 LinuxNodes 组中的所有节点上创建名为 /openfoam 的文件夹。 第二个命令将共享文件夹 //SUSE12RDMA-HN/OpenFOAM 装载到 dir_mode 和 file_mode 位设置为 777 的 Linux 节点上。 该命令中的*用户名*和*密码*应是头节点上的用户的凭据。

> [!NOTE]
> 第二个命令中的“\`”符号是 PowerShell 的转义符。 “\`,” 表示 “,”（逗号）是命令的一部分。
> 
> 

## <a name="install-mpi-and-openfoam"></a>安装 MPI 和 OpenFOAM
要在 RDMA 网络上以 MPI 作业的形式运行 OpenFOAM，需要使用 Intel MPI 库编译 OpenFOAM。 

首先，运行多个 **clusrun** 命令，在 Linux 节点上安装 Intel MPI 库和 OpenFOAM（如果尚未安装）。 使用以前配置的头节点共享在 Linux 节点中共享安装文件。

> [!IMPORTANT]
> 这些安装和编译步骤均为示例。 需要对 Linux 系统管理有一定的了解，以确保正确安装相关的编译器和库。 可能需要根据 Intel MPI 和 OpenFOAM 的版本修改某些环境变量或其他设置。 有关详细信息，请参阅适用于环境的 [Intel MPI Library for Linux Installation Guide](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)（Intel MPI Library for Linux 安装指南）和 [OpenFOAM Source Pack Installation](http://openfoam.org/download/2-3-1-source/)（OpenFOAM Source Pack 安装）。
> 
> 

### <a name="install-intel-mpi"></a>安装 Intel MPI
将下载的 Intel MPI 安装包（在此示例中为 l_mpi_p_5.0.3.048.tgz）保存到头节点上的 C:\OpenFoam 中，使得 Linux 节点能够从 /openfoam 访问此文件。 然后，运行 **clusrun** 在所有 Linux 节点上安装 Intel MPI 库。

1. 以下命令复制安装包并将其解压缩到每个节点上的 /opt/intel。
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. 若要以无提示方式安装 Intel MPI Library，请使用 silent.cfg 文件。 可以在本文末尾的示例文件中找到一个示例。 将此文件放在共享文件夹 /openfoam 中。 有关 silent.cfg 文件的详细信息，请参阅 [Intel MPI Library for Linux Installation Guide - Silent Installation](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall)（Intel MPI Library for Linux 安装指南 - 无提示安装）。
   
   > [!TIP]
   > 请确保将 silent.cfg 文件另存为带有 Linux 换行（仅 LF，而不是 CR LF）的文本文件。 此步骤可确保其在 Linux 节点上正常运行。
   > 
   > 
3. 在静默模式下安装 Intel MPI Library。
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>配置 MPI
测试时，应将以下行添加到每个 Linux 节点的 /etc/security/limits.conf 中：

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


更新 limits.conf 文件之后，请重新启动 Linux 节点。 例如，使用以下 **clusrun** 命令。

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

重新启动之后，请确保将共享文件夹以 /openfoam 形式装入。

### <a name="compile-and-install-openfoam"></a>编译并安装 OpenFOAM
将下载的 OpenFOAM Source Pack 安装包（在此示例中为 OpenFOAM-2.3.1.tgz）保存到头节点上的 C:\OpenFoam 中，使得 Linux 节点能够从 /openfoam 访问此文件。 然后，运行 **clusrun** 命令在所有 Linux 节点上编译 OpenFOAM。

1. 在每个 Linux 节点上创建 /opt/OpenFOAM 文件夹，将源包复制到该文件夹中，并解压缩。
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. 若要通过 Intel MPI Library 编译 OpenFOAM，请先针对 Intel MPI 和 OpenFOAM 设置某些环境变量。 使用名为 settings.sh 的 bash 脚本设置变量。 可以在本文末尾的示例文件中找到一个示例。 将此文件（保存时带有 Linux 换行）置于共享文件夹 /openfoam 中。 此文件还包含可以随后用来运行 OpenFOAM 作业的 MPI 和 OpenFOAM 运行时设置。
3. 安装编译 OpenFOAM 所需的相关程序包。 可能需要添加存储库，具体取决于 Linux 分发。 运行类似于下面的 **clusrun** 命令：
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    必要时可对每个 Linux 节点使用 SSH，以便运行相关命令来确认这些命令是否正常运行。
4. 运行以下命令以编译 OpenFOAM。 编译过程需一段时间才能完成，并且会生成大量可供标准输出的日志信息，因此请使用 **/interleaved** 选项来交错显示输出。
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > 命令中的“\`”符号是 PowerShell 的转义符。 “\`&”表示“&”是命令的一部分。
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>准备运行 OpenFOAM 作业
现在，请准备好在两个 Linux 节点上运行名为 sloshingTank3D 的 MPI 作业，该作业是 OpenFoam 示例之一。 

### <a name="set-up-the-runtime-environment"></a>设置运行时环境
若要在所有 Linux 节点上设置 MPI 和 OpenFOAM 的运行时环境，请在头节点的 Windows PowerShell 窗口运行以下命令。 （此命令仅适用于 SUSE Linux。）

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>准备示例数据
使用以前配置的头节点共享在 Linux 节点中共享文件（装载为 /openfoam）。

1. 通过 SSH 连接到 Linux 计算节点之一。
2. 如果尚未执行此操作，请运行以下命令以设置 OpenFOAM 运行时环境。
   
   ```
   $ source /openfoam/settings.sh
   ```
3. 将 sloshingTank3D 示例复制到共享文件夹，并导航到该文件夹。
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. 使用此示例的默认参数时，可能需要数十分钟或更长的运行时间，因此可能需要修改部分参数，使其运行速度加快。 一种简单的方法是修改 system/controlDict 文件中的时间步长变量 deltaT 和 writeInterval。 此文件存储与时间控制以及解决方案数据的读取和写入相关的所有输入数据。 例如，可以将 deltaT 的值从 0.05 更改为 0.5，将 writeInterval 的值从 0.05 更改为 0.5。
   
   ![修改步骤变量][step_variables]
5. 在 system/decomposeParDict 文件中指定变量的所需值。 此示例使用两个 Linux 节点，每个节点 8 个内核，因此可将 numberOfSubdomains 设置为 16，将 hierarchicalCoeffs 的 n 设置为 (1 1 16)，这表示使用 16 个流程并行运行 OpenFOAM。 有关详细信息，请参阅 [OpenFOAM User Guide: 3.4 Running applications in parallel](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4)（OpenFOAM 用户指南：3.4 并行运行应用程序）。
   
   ![对过程进行分解][decompose]
6. 从 sloshingTank3D 目录运行以下命令，对示例数据进行准备。
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. 在头节点上，会看到示例数据文件已复制到 C:\OpenFoam\sloshingTank3D 中。 （C:\OpenFoam 是头节点上的共享文件夹。）
   
   ![头节点上的数据文件][data_files]

### <a name="host-file-for-mpirun"></a>mpirun 的主机文件
在此步骤中，将创建一个可供 **mpirun** 命令使用的主机文件（计算节点的列表）。

1. 在其中一个 Linux 节点的 /openfoam 下创建名为 hostfile 的文件，使用户可以在所有 Linux 节点的 /openfoam/hostfile 位置访问此文件。
2. 将 Linux 节点名称写入此文件中。 在此示例中，该文件包含以下名称：
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > 还可以在头节点的 C:\OpenFoam\hostfile 中创建此文件。 如果选择此选项，可以将脚本另存为带有 Linux 换行（仅 LF，而不是 CR LF）的文本文件。 这可确保其在 Linux 节点上正常运行。
   > 
   > 
   
   **Bash 脚本包装**
   
   如果有多个 Linux 节点，并且希望作业仅在部分节点上运行，则最好不要使用固定的主机文件，因为不知道哪些节点会分配给作业。 在这种情况下，可为 **mpirun** 编写一个 bash 脚本包装，以便自动创建主机文件。 可以在本文末尾找到一个名为 hpcimpirun.sh 的示例 bash 脚本包装，请将它另存为 /openfoam/hpcimpirun.sh。此示例脚本执行以下任务：
   
   1. 设置 **mpirun** 的环境变量以及其他命令参数，通过 RDMA 网络运行 MPI 作业。 在本例中，将设置以下变量：
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. 根据环境变量 $CCP_NODES_CORES 创建主机文件，该变量是在激活作业时由 HPC 头节点设置的。
      
      $CCP_NODES_CORES 的格式遵循以下模式：
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      其中
      
      * `<Number of nodes>`：分配给此作业的节点数。  
      * `<Name of node_n_...>`：分配给此作业的每个节点的名称。
      * `<Cores of node_n_...>`：分配给此作业的节点上的内核数。
      
      例如，如果作业需要两个节点才能运行，则 $CCP_NODES_CORES 将类似于
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. 调用 **mpirun** 命令并将两个参数追加到命令行。
      
      * `--hostfile <hostfilepath>: <hostfilepath>` - 该脚本创建的主机文件的路径
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` - HPC Pack 头节点设置的环境变量，用于存储分配给此作业的内核的总数。 在本示例中，它指定 **mpirun** 的进程数。

## <a name="submit-an-openfoam-job"></a>提交 OpenFOAM 作业
现在，可以在 HPC 群集管理器中提交作业。 部分作业任务需要将脚本 hpcimpirun.sh 传递到命令行中。

1. 连接到群集头节点，并启动 HPC 群集管理器。
2. 在“资源管理”中，确保 Linux 计算节点处于“联机”状态。 如果节点未处于联机状态，请选择它们并单击“联机”。
3. 在“作业管理”中，单击“新作业”。
4. 为作业输入一个名称，如 *sloshingTank3D*。
   
   ![作业详细信息][job_details]
5. 在“作业资源”中，将资源类型选为“节点”，并将“最小数量”设置为 2。 在本示例中，此配置会在两个 Linux 节点（每个节点有八个核心）上运行该作业。
   
   ![作业资源][job_resources]
6. 在左侧导航栏中单击“编辑任务”，并单击“添加”将任务添加到作业。 使用以下命令行和设置将四个任务添加到作业。
   
   > [!NOTE]
   > 运行 `source /openfoam/settings.sh` 将设置 OpenFOAM 和 MPI 运行时环境，因此下述每个任务会在调用 OpenFOAM 命令之前调用它。
   > 
   > 
   
   * **任务 1**。 运行 **decomposePar**，生成并行运行 **interDyMFoam** 所需的数据文件。
     
     * 为任务分配一个节点
     * **命令行** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **工作目录** - /openfoam/sloshingTank3D
     
     请参阅下图。 以相似的方式配置剩余任务。
     
     ![任务 1 详细信息][task_details1]
   * **任务 2**。 并行运行 **interDyMFoam** 以计算示例。
     
     * 为任务分配两个节点
     * **命令行** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **工作目录** - /openfoam/sloshingTank3D
   * **任务 3**。 运行 **reconstructPar**，将每个 processor_N_ 目录的时间目录集合并到单个集内。
     
     * 为任务分配一个节点
     * **命令行** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **工作目录** - /openfoam/sloshingTank3D
   * **任务 4**。 并行运行 **foamToEnsight**，将 OpenFOAM 结果文件转换成 EnSight 格式，并将 EnSight 文件置于示例目录的名为 Ensight 的目录中。
     
     * 为任务分配两个节点
     * **命令行** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **工作目录** - /openfoam/sloshingTank3D
7. 按任务顺序的升序将依赖项添加到这些任务中。
   
   ![任务依赖项][task_dependencies]
8. 单击“提交”运行此作业。
   
   默认情况下，HPC Pack 以当前登录的用户帐户提交作业。 单击“提交”后，可能会看到一个对话框，提示输入用户名和密码。
   
   ![作业凭据][creds]
   
   在某些情况下，HPC Pack 会记住以前输入的用户信息，并不会显示此对话框。 为了使 HPC Pack 再次显示此对话框，在命令提示符下输入以下命令，并提交作业。
   
   ```
   hpccred delcreds
   ```
9. 根据你为示例设置的参数的不同，此作业可能需要数十分钟到数小时的时间才能完成。 在热度地图中，可以看到该作业在 Linux 节点上运行。 
   
   ![热度地图][heat_map]
   
   每个节点上启动了八个进程。
   
   ![Linux 进程][linux_processes]
10. 作业结束时，可在 C:\OpenFoam\sloshingTank3D 下的文件夹中找到作业结果，在 C:\OpenFoam 中找到日志文件。

## <a name="view-results-in-ensight"></a>在 EnSight 中查看结果
（可选）使用 [EnSight](https://www.ceisoftware.com/) 将 OpenFOAM 作业的结果可视化并对其进行分析。 有关在 EnSight 中实现可视化和动画效果的详细信息，请参阅此[视频指南](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html)。

1. 在头节点上安装 EnSight 后，启动它。
2. 打开 C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case。
   
   可在查看器中看到一个罐。
   
   ![EnSight 中的罐][tank]
3. 从 **internalMesh** 创建**等值面**，并选择变量 **alpha_water**。
   
   ![创建等值面][isosurface]
4. 为前一步骤中创建的 **Isosurface_part** 设置颜色。 例如，将其设置为水蓝色。
   
   ![编辑等值面颜色][isosurface_color]
5. 从“罐壁”创建“等值体”，方法是：在“部件”面板中选择“罐壁”，并单击工具栏中的“等值面”按钮。
6. 在对话框中，选择“等值体”作为“类型”，并将“等值体范围”的最小值设置为 0.5。 若要创建等值体，请单击“使用所选部件创建”。
7. 为前一步骤中创建的 **Iso_volume_part** 设置颜色。 例如，将其设置为深水蓝色。
8. 设置“罐壁”的颜色。 例如，将其设置为透明的白色。
9. 现在，请单击“播放”查看模拟结果。
   
    ![罐的结果][tank_result]

## <a name="sample-files"></a>示例文件
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>使用 PowerShell 脚本部署群集时可用的示例 XML 配置文件
 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>示例 cred.xml 文件
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>用于安装 MPI 的示例 silent.cfg 文件
```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>示例 settings.sh 脚本
```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


### <a name="sample-hpcimpirunsh-script"></a>实例 hpcimpirun.sh 脚本
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]:media/hpcpack-cluster-openfoam/keygen.png
[keys]:media/hpcpack-cluster-openfoam/keys.png
[step_variables]:media/hpcpack-cluster-openfoam/step_variables.png
[data_files]:media/hpcpack-cluster-openfoam/data_files.png
[decompose]:media/hpcpack-cluster-openfoam/decompose.png
[job_details]:media/hpcpack-cluster-openfoam/job_details.png
[job_resources]:media/hpcpack-cluster-openfoam/job_resources.png
[task_details1]:media/hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]:media/hpcpack-cluster-openfoam/task_dependencies.png
[creds]:media/hpcpack-cluster-openfoam/creds.png
[heat_map]:media/hpcpack-cluster-openfoam/heat_map.png
[tank]:media/hpcpack-cluster-openfoam/tank.png
[tank_result]:media/hpcpack-cluster-openfoam/tank_result.png
[isosurface]:media/hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]:media/hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]:media/hpcpack-cluster-openfoam/linux_processes.png
