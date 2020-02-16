---
title: 使用 Azure Migrate 将计算机作为物理服务器迁移到 Azure。
description: 本文介绍如何使用 Azure Migrate 将物理计算机迁移到 Azure。
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: MVC
ms.openlocfilehash: 908a5915cbb7f5aeb9f641da18024d5dbf497707
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134940"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>将计算机作为物理服务器迁移到 Azure

本文介绍如何使用 Azure Migrate:服务器迁移工具将计算机作为物理服务器迁移到 Azure。 通过将计算机视为物理服务器来迁移计算机在许多情况下很有用：

- 迁移本地物理服务器。
- 迁移 Xen、KVM 等平台虚拟化的 VM。
- 迁移 Hyper-V 或 VMware VM（如果由于某种原因无法使用标准迁移过程进行 [Hyper-V](tutorial-migrate-hyper-v.md) 或 [VMware](server-migrate-overview.md) 迁移）。
- 迁移私有云中运行的 VM。
- 迁移 Amazon Web Services (AWS) 或 Google Cloud Platform (GCP) 等公有云中运行的 VM。


[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与云 VM 实例的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。


在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 准备 Azure 以使用 Azure Migrate 服务器迁移工具进行迁移。
> * 检查要迁移的计算机的要求，并准备 Azure Migrate 复制设备的计算机，用于发现计算机并将其迁移到 Azure。
> * 在 Azure Migrate 中心添加 Azure Migrate 服务器迁移工具。
> * 设置复制设备。
> * 在要迁移的计算机上安装移动服务。
> * 启用复制。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行到 Azure 的完整迁移。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看 Azure Migrate 的操作指南。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>必备条件

开始学习本教程之前，应做好以下准备：

1. [查看](migrate-architecture.md)迁移体系结构。
2. 确保为 Azure 帐户分配“虚拟机参与者”角色，以便你有权执行以下操作：

    - 在所选资源组中创建 VM。
    - 在所选虚拟网络中创建 VM。
    - 写入 Azure 托管磁盘。 

3. [设置 Azure 网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 复制到 Azure 时，将创建 Azure VM 并将其加入到设置迁移时指定的 Azure 网络。


## <a name="prepare-azure"></a>准备 Azure

在使用 Azure Migrate 服务器迁移工具迁移之前设置 Azure 权限。

- **创建项目**：你的 Azure 帐户需要有权创建 Azure Migrate 项目。 

### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

## <a name="prepare-for-migration"></a>准备迁移

### <a name="check-machine-requirements-for-migration"></a>检查要迁移的计算机的要求

确保计算机符合迁移到 Azure 的要求。 

> [!NOTE]
> 使用 Azure Migrate 服务器迁移的基于代理的迁移与 Azure Site Recovery 服务的基于代理的灾难恢复功能具有相同的复制体系结构，并且使用的某些组件共享相同的代码库。 某些要求可能提供了 Site Recovery 文档的链接。

1. [验证](migrate-support-matrix-physical-migration.md#physical-server-requirements)物理服务器要求。
2. 验证 VM 设置。 复制到 Azure 的本地计算机必须符合 [Azure VM 要求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。


### <a name="prepare-a-machine-for-the-replication-appliance"></a>为复制设备准备计算机

Azure Migrate 服务器迁移使用复制设备将计算机复制到 Azure。 复制设备运行以下组件。

- **配置服务器**：配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- **进程服务器**：进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 中的缓存存储帐户。 

在开始之前，需要准备一台用于托管复制设备的 Windows Server 2016 计算机。 该计算机应符合[这些要求](migrate-replication-appliance.md)。 该设备不应安装在要保护的源计算机上。


## <a name="add-the-azure-migrate-server-migration-tool"></a>添加 Azure Migration 服务器迁移工具

设置一个 Azure Migrate 项目，并将 Azure Migrate 服务器迁移工具添加到其中。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。 
2. 在“服务”下选择“Azure Migrate”。  
3. 在“概述”中，单击“评估和迁移服务器”。  
4. 在“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。  

    ![发现和评估服务器](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. 在“发现、评估和迁移服务器”中，单击“添加工具”。  
6. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。 
7. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置，然后单击“下一步”。  

    ![创建 Azure Migrate 项目](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    可在下述任一地理位置创建 Azure Migrate 项目。

    **地域** | **区域**
    --- | ---
    亚洲 | 东南亚
    欧洲 | 欧洲北部或欧洲西部
    United States | 美国东部或美国中西部

    为项目指定的地理位置仅用于存储从本地 VM 中收集的元数据。 可为实际迁移选择任一目标区域。
8. 在“选择评估工具”中，选择“暂时跳过添加评估工具” > “下一步”。   
9. 在“选择迁移工具”中，选择“Azure Migrate:   服务器迁移” > “下一步”。 
10. 在“检查 + 添加工具”中检查设置，然后单击“添加工具”。  
11. 添加工具后，它会显示在 Azure Migrate 项目的“服务器” > “迁移工具”中。  

## <a name="set-up-the-replication-appliance"></a>设置复制设备

第一个迁移步骤是设置复制设备。 下载该设备的安装程序文件，并在[准备好的计算机](#prepare-a-machine-for-the-replication-appliance)上运行该文件。 安装设备后，将其注册到 Azure Migrate 服务器迁移。


### <a name="download-the-replication-appliance-installer"></a>下载复制设备安装程序

1. 在 Azure Migrate 项目中选择“服务器”，在“Azure Migrate:   服务器迁移”中，单击“发现”。 

    ![发现 VM](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“未虚拟化/其他”。   
4. 在“目标区域”中，选择要将计算机迁移到的 Azure 区域。 
5. 选择“确认迁移的目标区域为 <区域名称>”。 
6. 单击“创建资源”。  随即会在后台创建一个 Azure Site Recovery 保管库。
    - 如果已设置使用 Azure Migrate 服务器迁移进行迁移，则无法配置目标选项，因为之前已设置了资源。
    - 单击此按钮后，无法更改此项目的目标区域。
    - 所有后续迁移的目标都是此区域。

7. 在“是否安装新的复制设备?”中，选择“安装复制设备”。  
9. 在“下载并安装复制设备软件”中，下载设备安装程序和注册密钥。  需要使用该密钥来注册设备。 下载的密钥有效期为 5 天。

    ![下载提供程序](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. 将设备安装程序文件和密钥文件复制到为该设备创建的 Windows Server 2016 计算机。
11. 如以下过程中所述，运行复制设备安装程序文件。
12. 设备已安装并重启后，在“发现计算机”中的“选择配置服务器”内选择新设备，然后单击“完成注册”。    “完成注册”步骤会执行最终的几个任务来准备复制设备。

    ![完成注册](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

完成注册后，可能需要一些时间才能将发现的计算机显示在 Azure Migrate 服务器迁移中。 随着 VM 的发现，“已发现的服务器”计数会不断增大。 

![已发现的服务器](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>安装移动服务

在要迁移的计算机上，需要安装移动服务代理。 复制设备上已提供代理安装程序。 请找到适当的安装程序，并在要迁移的每台计算机上安装该代理。 请按如下所述执行此操作：

1. 登录到复制设备。
2. 导航到 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**。
3. 找到适用于计算机操作系统和版本的安装程序。 查看[支持的操作系统](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)。 
4. 将安装程序文件复制到要迁移的计算机。
5. 确保已准备好在部署设备时生成的通行短语。
    - 将该通行短语存储在计算机上的某个临时文本文件中。
    - 可在复制设备上获取通行短语。 在命令行中，运行 **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** 查看当前的通行短语。
    - 不要重新生成通行短语。 否则会中断连接，并需要重新注册复制设备。


### <a name="install-on-windows"></a>在 Windows 上安装

1. 将安装程序文件的内容解压缩到计算机上的某个本地文件夹（例如 C:\Temp），如下所示：

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. 运行移动服务安装程序：
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. 将代理注册到复制设备：
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>在 Linux 上安装

1. 将安装程序 tarball 的内容解压缩到计算机上的某个本地文件夹（例如 /tmp/MobSvcInstaller），如下所示：
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. 运行安装程序脚本：
    ```
    sudo ./install -r MS -q
    ```
3. 将代理注册到复制设备：
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>复制计算机

现在，选择要迁移的 计算机。 

> [!NOTE]
> 最多可以同时复制 10 台计算机。 如果需要复制更多，则可以每批 10 台同时复制它们。

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate:   服务器迁移”，然后单击“复制”。 

    ![复制 VM](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. 在“复制”>“源设置” > “你的计算机是否已虚拟化?”中，选择“未虚化/其他”     。
3. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称。 
4. 在“进程服务器”中，选择复制设备的名称。 
6. 在“来宾凭据”中，指定用于推送安装移动服务的 VM 管理员帐户。  在本教程中，我们将手动安装移动服务，以便可以添加任何虚构帐户。 然后单击“下一页:  虚拟机”。

    ![复制 VM](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. 在“虚拟机”中的“从评估中导入迁移设置?”内，保留默认设置“否，我将手动指定迁移设置”。   
8. 检查要迁移的每个 VM。 然后单击“下一页:  目标设置”。

    ![选择 VM](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. 在“目标设置”中，选择订阅以及要迁移到的目标区域，并指定迁移之后 Azure VM 所在的资源组。 
10. 在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。 
11. 在“Azure 混合权益”中： 

    - 如果你不想要应用 Azure 混合权益，请选择“否”。  然后单击“下一步”  。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。  然后单击“下一步”  。

    ![目标设置](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性集。  VM 必须符合 [Azure 要求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。

    - **VM 大小**：默认情况下，Azure Migrate 服务器迁移会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。  
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 
    - **可用性集**：如果迁移后 VM 应位于某个 Azure 可用性集中，请指定该集。 该集必须位于为迁移指定的目标资源组中。

    ![计算设置](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。  然后单击“下一步”  。
    - 可以从复制中排除磁盘。
    - 如果排除了磁盘，迁移后，这些磁盘将不会出现在 Azure VM 中。 

    ![磁盘设置](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。  

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。   开始复制后无法更改设置。



## <a name="track-and-monitor"></a>跟踪和监视

- 单击“复制”会启动一个“开始复制”作业。  
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。


可以在门户通知中跟踪作业状态。

可以通过单击“Azure Migrate: 服务器迁移”中的“正在复制服务器”   来监视复制状态。
![监视复制](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>运行测试迁移


增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 我们强烈建议在迁移之前，针对每台计算机至少执行此操作一次。

- 运行测试迁移可以检查迁移是否按预期进行，而不会影响本地计算机，它们仍可保持正常运行并继续复制。 
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：


1. 在“迁移目标” > “服务器” > “Azure Migrate:    服务器迁移”中，单击“测试已迁移的服务器”。 

     ![测试已迁移的服务器](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。 

    ![测试迁移](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure VNet。  建议使用非生产 VNet。
4. “测试迁移”作业随即启动。  在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。  计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。  

    ![清理迁移](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>迁移 VM

确认测试迁移按预期方式进行后，可以迁移本地计算机。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate:   服务器迁移”，然后单击“复制服务器”。 

    ![复制服务器](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。  
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。    
    - 默认情况下，Azure Migrate 将关闭本地 VM，并运行按需复制，以同步自上次复制发生以来发生的任何 VM 更改。 这可以确保不会丢失数据。
    - 如果你不想要关闭 VM，请选择“否” 
4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。 

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止迁移”。  这会停止本地计算机的复制，并清理 VM 的复制状态信息。
2. 在已迁移的计算机上安装 Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 代理。
3. 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
4. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
5. 将流量交接到已迁移的 Azure VM 实例。
6. 从本地 VM 清单中删除本地 VM。
7. 从本地备份中删除本地 VM。
8. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。 

## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 为提高恢复能力，请执行以下操作：
    - 使用 Azure 备份服务备份 Azure VM 以保证数据安全。 [了解详细信息](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。 [了解详细信息](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)锁定和限制入站流量访问。
    - 使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
    - 考虑部署[Azure 成本管理](https://docs.microsoft.com/azure/cost-management/overview)以监视资源使用率和支出。


## <a name="next-steps"></a>后续步骤

在 Azure 云采用框架中调查[云迁移旅程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)。
