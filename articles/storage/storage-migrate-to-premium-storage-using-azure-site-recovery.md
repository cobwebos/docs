---
title: "使用 Azure Site Recovery 迁移到 Azure 高级存储 | Microsoft 文档"
description: "使用 Site Recovery 将现有虚拟机迁移到 Azure 高级存储。 高级存储为 Azure 虚拟机上运行的 I/O 密集型工作负荷提供高性能、低延迟的磁盘支持。"
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: <service per approved list>
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2016
ms.author: luywang
translationtype: Human Translation
ms.sourcegitcommit: 67b4861ac564565b2a36932ae15141a1e1f56035
ms.openlocfilehash: 0cf61b6bb9817bbf9508b10301b5e95b12ecea39
ms.lasthandoff: 02/23/2017


---
# <a name="migrating-to-premium-storage-using-azure-site-recovery"></a>使用 Azure Site Recovery 迁移到高级存储

[Azure 高级存储](storage-premium-storage.md)为运行 I/O 密集型工作负荷的虚拟机 (VM) 提供高性能、低延迟的磁盘支持。 本指南的目的是帮助用户使用 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 将其 VM 磁盘从标准存储帐户迁移到高级存储帐户。

Site Recovery 是一个 Azure 服务，可通过协调从本地物理服务器和 VM 到云 (Azure) 或辅助数据中心的复制，来为业务连续性和灾难恢复策略提供辅助。 当主要位置发生故障时，可以故障转移到辅助位置，使应用程序和工作负荷保持可用。 当主要位置恢复正常时，可以故障转移回到主要位置。 Site Recovery 提供测试故障转移，既能支持灾难恢复练习，又不会影响生产环境。 可以针对意外的灾难，以最低的数据丢失程度运行计划外故障转移（根据复制频率）。 在迁移到高级存储的方案中，可以使用 Azure Site Recovery 中的[计划外故障转移](../site-recovery/site-recovery-failover.md)将目标磁盘迁移到高级存储帐户。

之所以建议使用 Site Recovery 迁移到高级存储，是因为此选项造成的停机时间最短，并可以避免手动复制磁盘和创建新 VM。 在故障转移过程中，Site Recovery 将系统性地复制磁盘并创建新的 VM。 Site Recovery 支持多种类型的、停机时间极短或不造成停机的故障转移。 若要规划停机时间和评估数据丢失情况，请参阅 Site Recovery 中的[故障转移类型](../site-recovery/site-recovery-failover.md)表。 如果[已准备好在故障转移后连接到 Azure VM](../site-recovery/site-recovery-vmware-to-azure.md#prepare-vms-for-replication)，应该能够在故障转移后使用 RDP 连接到 Azure VM。

![][1]

## <a name="migration-scenario-components"></a>迁移方案组件

**此迁移方案中的相关 Site Recovery 组件：**

* **配置服务器**是用于协调通信以及管理数据复制和恢复过程的 Azure VM。 将在此 VM 上运行单个安装程序文件来安装配置服务器，以及一个称作进程服务器、用作复制网关的附加组件。 请阅读[配置服务器必备组件](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites)。 配置服务器只需配置一次，在迁移到同一区域的所有过程中都可以使用它。

* **进程服务器**是从源 VM 接收复制数据的复制网关，可以使用缓存、压缩和加密优化数据，并将其发送到存储帐户。 它还处理从移动服务到源 VM 的推送安装，执行源 VM 的自动发现。 默认的进程服务器安装在配置服务器上。 你可以部署更多的独立进程服务器以扩展部署。 请阅读[进程服务器部署的最佳做法](https://azure.microsoft.com/en-us/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/)和[部署其他进程服务器](../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers)。 进程服务器只需配置一次，在迁移到同一区域的所有过程中都可以使用它。

* **移动服务**是在想要复制的每个标准 VM 上部署的组件。 它可以捕获标准 VM 上的数据写入，并将其转发到进程服务器。 阅读[复制的计算机先决条件](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites)。

**Azure 基本要求**： 

* Azure 订阅
* 用于存储复制数据的 Azure 高级存储帐户
* 故障转移时创建的 VM 要连接到的 Azure 虚拟网络 (VNet)。 该 Azure VNet 必须位于 Site Recovery 运行所在的同一区域
* 要在其中存储复制日志的 Azure 标准存储帐户。 可以是要迁移的 VM 磁盘所在的同一存储帐户

下图显示了这些组件的交互方式。

![][15]

> [!NOTE]
> Site Recovery 不支持迁移存储空间磁盘。

有关其他方案的其他组件，请参阅[方案体系结构](../site-recovery/site-recovery-vmware-to-azure.md)。

## <a name="prerequisites"></a>先决条件

* 在上一部分中了解相关的迁移方案组件
* 通过 [Site Recovery 中的故障转移](../site-recovery/site-recovery-failover.md)了解计划外故障转移，规划停机时间

## <a name="setup-and-migration-steps"></a>设置和迁移步骤

可以使用 Site Recovery 在区域之间或者在同一区域内部迁移 Azure IaaS VM。 以下说明是在[将 VMware VM 或物理服务器复制到 Azure](../site-recovery/site-recovery-vmware-to-azure.md) 一文的基础上，专门针对此迁移方案编写的。 除了本文中所述的说明以外，请单击相关的链接了解详细步骤。

1. **创建恢复服务保管库**。 通过 [Azure 门户](https://portal.azure.com)创建和管理 Site Recovery 保管库。 单击“新建” > “管理” > “备份和 Site Recovery (OMS)”。 或者，可以单击“浏览” > “恢复服务保管库” > “添加”。 VM 将复制到在此步骤中指定的区域。 若要在同一区域中迁移，请选择源 VM 和源存储帐户所在的区域。 请注意，仅支持在 [Azure 门户](https://portal.azure.com)中迁移到高级存储帐户，而不能在[经典门户](https://manage.windowsazure.com)中迁移。

2. **选择保护目标**。 在想要安装配置服务器的 VM 上，打开 [Azure 门户](https://portal.azure.com)。 转到“恢复服务保管库” > “设置”。 在“设置”下，选择“Site Recovery”。 在“Site Recovery”下，选择“步骤 1: 准备基础结构”。 在“准备基础结构”下，选择“保护目标”。
  
  ![][2]
  
  在“保护目标”下，从第一个下拉列表中选择“到 Azure”。 在第二个下拉列表中，选择“未虚拟化/其他”，然后单击“确定”。
  
  ![][3]
  
3. **设置源环境（配置服务器）**。 转到“准备基础结构” > “准备源” > “添加服务器”边栏选项卡，下载 **Azure Site Recovery 统一安装程序**和**保管库注册密钥**。 需要使用保管库注册密钥来运行统一安装程序。 生成的密钥有效期为 5 天。
  
  ![][4]
  
  ![][5]
  
  在用作配置服务器的 VM 上，运行统一安装程序以安装配置服务器和进程服务器。 可以参考[此处](../site-recovery/site-recovery-vmware-to-azure.md#set-up-the-source-environment)提供的屏幕截图逐步完成安装。 可以参考以下屏幕截图了解针对此迁移方案指定的步骤。

  在“开始之前”中选择“安装配置服务器和进程服务器”。
  
  ![][6]

  在“注册”中，通过浏览查找并选择从保管库下载的注册密钥。
  
  ![][7]

  在“环境详细信息”中，选择是否要复制 VMware VM。 对于此迁移方案，请选择“否”。
  
  ![][8]
  
  安装完成后，将显示“Microsoft Azure Site Recovery 配置服务器”窗口。 使用“管理帐户”选项卡创建可供 Site Recovery 用于执行自动发现的帐户。 （在保护物理机的相关方案中，没有必要设置帐户，但至少要有一个帐户才能启用以下步骤之一。 在此情况下，可以指定任意帐户和密码。）使用“保管库注册”选项卡上上载保管库凭据文件。
  
  ![][9]

4. **设置目标环境**。 单击“准备基础结构” > “目标”，然后指定要在故障转移后用于 VM 的部署模型。 可以根据方案选择“经典”或“Resource Manager”。
  
  ![][10]

  Site Recovery 将检查是否有一个或多个兼容的 Azure 存储帐户和网络。 请注意，如果使用高级存储帐户保存复制的数据，则需要设置附加的标准存储帐户来存储复制日志。

5. **设置复制设置**。 请遵循[此处](../site-recovery/site-recovery-vmware-to-azure.md#set-up-replication-settings)提供的步骤，验证配置服务器是否已成功与创建的复制策略相关联。

6. **容量规划**。 使用 [Capacity Planner](../site-recovery/site-recovery-capacity-planner.md) 根据复制需要准确估算网络带宽、存储和其他要求。 完成后，请在“是否已完成容量规划?”中选择“是”。
  
  ![][11]

7. **安装移动服务并启用复制**。 可以选择向源 VM 执行[推送安装](../site-recovery/site-recovery-vmware-to-azure.md#prepare-for-automatic-discovery-and-push-installation)，或者在源 VM 上[手动安装移动服务](../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md)。 可在提供的链接中找到推送安装的要求以及手动安装程序的路径。 如果执行手动安装，可能需要使用内部 IP 地址来查找配置服务器。 
  
  ![][12]
  
  故障转移 VM 包含两个临时磁盘：一个磁盘来自主 VM，另一个磁盘是在恢复区域中预配 VM 期间创建的。 若要在复制之前排除临时磁盘，请在启用复制之前安装移动服务。 若要详细了解如何排除临时磁盘，请参阅[从复制中排除磁盘](../site-recovery/site-recovery-vmware-to-azure.md#exclude-disks-from-replication)。
**现在，请按如下所述启用复制：**
  * 单击“复制应用程序” > “源”。 首次启用复制后，请在保管库中单击“+复制”，对其他计算机启用复制。
  * 在步骤 1 中，将“源”设置为进程服务器。
  * 在步骤 2 中，指定故障转移后的部署模型、要迁移到的高级存储帐户、用于保存日志的标准存储帐户，以及要故障转移到的虚拟网络。 
  * 在步骤 3 中，按 IP 地址添加受保护的 VM（可能需要使用内部 IP 地址来查找这些 VM）。 
  * 在步骤 4 中，通过选择前面在进程服务器上设置的帐户来配置属性。 
  * 在步骤 5 中，选择前面创建的复制策略，并设置复制设置。 
  单击“确定”启用复制。
  
  > [!NOTE]
  > 解除分配并再次启动 Azure VM 后，无法保证它会获得相同的 IP 地址。 如果配置服务器/进程服务器或受保护 Azure VM 的 IP 地址发生变化，此方案中的复制可能无法正常工作。
  
  ![][13]
  
  设计 Azure 存储环境时，我们建议针对可用性集中的每个 VM 使用不同的存储帐户。 建议遵循 [Windows](../virtual-machines/virtual-machines-windows-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) 和 [Linux](../virtual-machines/virtual-machines-linux-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) VM 存储层的最佳做法。 将 VM 磁盘分配到多个存储帐户有助于改善存储可用性，以及在整个 Azure 存储基础结构中分配 I/O。 如果 VM 位于可用性集中，我们强烈建议分多次迁移多个 VM，而不要将所有 VM 的磁盘都复制到一个存储帐户，这是为了避免同一个可用性集中的 VM 共享单个存储帐户。 使用“启用复制”边栏选项卡为每个 VM 设置目标存储帐户，一次设置一个。 可以根据需要选择故障转移后的部署模型。 如果选择 Resource Manager (RM) 作为故障转移后的部署模型，可将 RM VM 故障转移到 RM VM，或者将经典 VM 故障转移到 RM VM。

8. **运行测试故障转移**。 若要检查复制是否完成，请单击“Site Recovery”，然后单击“设置” > “已复制的项”。 此时将显示复制过程的状态和完成百分比。 初始复制完成后，请运行测试故障转移来验证复制策略。 有关测试故障转移的详细步骤，请参阅 [Run a test failover in Site Recovery](../site-recovery/site-recovery-vmware-to-azure.md#run-a-test-failover)（在 Site Recovery 中运行测试故障转移）。 可在“设置” > “作业” > “YOUR_FAILOVER_PLAN_NAME”中查看测试故障转移的状态。 在边栏选项卡中，可以看到具体的步骤，以及成功/失败结果。 如果测试故障转移在执行任一步骤时失败，请单击该步骤查看错误消息。 运行计划外故障转移之前，请确保 VM 和复制策略满足要求。

9. **运行计划外故障转移**。 完成测试故障转移后，请运行计划外故障转移，将磁盘迁移到高级存储并复制 VM 实例。 请遵循 [Run an unplanned failover in Site Recovery](../site-recovery/site-recovery-failover.md)（在 Site Recovery 中运行计划外故障转移）中的详细步骤。 请务必选择“关闭 VM 并同步最新数据”，指定 Azure Site Recovery 应尝试关闭受保护的 VM 并同步数据，以便对最新版的数据进行故障转移。 如果不选择此选项或尝试不成功，系统将从 VM 的最近恢复点开始故障转移。 Site Recovery 将创建类型与支持高级存储的 VM 相同或类似的 VM 实例。 可以参阅 [Windows 虚拟机定价](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/windows/)或 [Linux 虚拟机定价](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/)了解各种 VM 实例的性能和价格。

## <a name="post-migration-steps"></a>迁移后的步骤

1. **将复制的 VM 配置到可用性集（如果适用）**。 Site Recovery 不支持连同可用性集一起迁移 VM。 根据复制的 VM 的部署，执行以下操作之一：
  * 对于使用经典部署模型创建的 VM：在 Azure 门户中将 VM 添加到可用性集。 有关详细步骤，请参阅[将现有虚拟机添加到可用性集](../virtual-machines/virtual-machines-windows-classic-configure-availability.md#a-idaddmachine-aoption-2-add-an-existing-virtual-machine-to-an-availability-set)。
  * 对于 Resource Manager 部署模型：保存 VM 的配置，然后在可用性集中删除再重新创建 VM。 为此，请使用 [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)（设置 Azure Resource Manager VM 可用性集）中所述的脚本。 运行此脚本之前，请检查此脚本的限制并规划好停机时间。

2. **删除旧 VM 和磁盘**。 在删除之前，请确保高级磁盘与源磁盘一致，并且新 VM 执行的功能与源 VM 相同。 在 Resource Manager (RM) 部署模型中，通过 Azure 门户删除源存储帐户中的 VM 和磁盘。 在经典部署模型中，可通过经典门户或 Azure 门户删除 VM 和磁盘。 如果出现了即使删除 VM 也无法删除磁盘的问题，请参阅 [Troubleshoot errors when you delete VHDs in an RM deployment](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)（排查在 RM 部署中删除 VHD 时遇到的错误）或 [Troubleshoot deleting VHDs in a classic deployment](storage-cannot-delete-storage-account-container-vhd.md)（排查在经典部署中删除 VHD 时遇到的错误）。

3. **清理 Azure Site Recovery 基础结构**。 如果不再需要 Site Recovery，可以通过删除已复制的项、配置服务器和恢复策略，然后删除 Azure Site Recovery 保管库，来清理其基础结构。

## <a name="troubleshooting"></a>故障排除

* [监视虚拟机和物理服务器的保护及其故障排除](../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>后续步骤

有关虚拟机迁移的特定方案，请参阅以下资源：

* [Migrate Azure Virtual Machines between Storage Accounts](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)（在存储帐户之间迁移 Azure 虚拟机）
* [创建 Windows Server VHD 并将其上传到 Azure。](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [创建并上传包含 Linux 操作系统的虚拟硬盘](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [将虚拟机从 Amazon AWS 迁移到 Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

另请参阅以下资源，以了解有关 Azure 存储和 Azure 虚拟机的详细信息：

* [Azure 存储](https://azure.microsoft.com/documentation/services/storage/)
* [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](storage-premium-storage.md)

[1]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png

