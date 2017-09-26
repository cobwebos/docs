---
title: "使用 Azure Site Recovery 迁移到 Azure 高级存储 | Microsoft Docs"
description: "使用 Site Recovery 将现有虚拟机迁移到 Azure 高级存储。 高级存储为 Azure 虚拟机上运行的 I/O 密集型工作负荷提供高性能、低延迟的磁盘支持。"
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 45d89cb9b51e7091f8921d3f58d3a4594851ca7f
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>使用 Azure Site Recovery 迁移到高级存储

[Azure 高级存储](storage-premium-storage.md)为运行 I/O 密集型工作负荷的虚拟机 (VM) 提供高性能、低延迟的磁盘支持。 本指南帮助用户使用 [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) 将其 VM 磁盘从标准存储帐户迁移到高级存储帐户。

Site Recovery 是一个 Azure 服务，可通过协调从本地物理服务器和 VM 到云 (Azure) 或辅助数据中心的复制，来为业务连续性和灾难恢复策略提供辅助。 当主要位置发生故障时，可以故障转移到辅助位置，使应用程序和工作负荷保持可用。 当主要位置恢复正常时，可以故障转移回到主要位置。 

Site Recovery 提供测试故障转移，既能支持灾难恢复练习，又不会影响生产环境。 可针对意外灾难以最低的数据丢失程度（取决于复制频率）运行故障转移。 在迁移到高级存储的方案中，可以使用 [Site Recovery 中的故障转移](../../site-recovery/site-recovery-failover.md)，将目标磁盘迁移到高级存储帐户。

之所以建议使用 Site Recovery 迁移到高级存储，是因为此选项造成的停机时间最短。 它还可以避免手动复制磁盘和创建新 VM。 在故障转移过程中，Site Recovery 将系统性地复制磁盘并创建新的 VM。 

Site Recovery 支持多种类型的、停机时间极短或不造成停机的故障转移。 若要规划停机时间和评估数据丢失情况，请参阅 [Site Recovery 中的故障转移类型](../../site-recovery/site-recovery-failover.md)。 如果[已准备好在故障转移后连接到 Azure VM](../../site-recovery/vmware-walkthrough-overview.md)，应该能够在故障转移后使用 RDP 连接到 Azure VM。

![灾难恢复关系图][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery 组件

这些是与此迁移方案相关的 Site Recovery 组件。

* **配置服务器**是用于协调通信以及管理数据复制和恢复过程的 Azure VM。 在此 VM 上运行单个安装程序文件来安装配置服务器，以及一个称作进程服务器、用作复制网关的附加组件。 请阅读[配置服务器必备组件](../../site-recovery/vmware-walkthrough-overview.md)。 配置服务器只需设置一次，在迁移到同一区域的所有过程中都可以使用它。

* 进程服务器是一种复制网关，其职能包括： 

  1. 接收来自源 VM 的复制数据。
  2. 通过缓存、压缩和解密来优化数据。
  3. 向存储帐户发送数据。 

  它还处理从移动服务到源 VM 的推送安装，执行源 VM 的自动发现。 默认的进程服务器安装在配置服务器上。 可以部署更多的独立进程服务器以扩展部署。 请阅读[进程服务器部署的最佳做法](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/)和[部署其他进程服务器](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers)。 进程服务器只需配置一次，在迁移到同一区域的所有过程中都可以使用它。

* 移动服务是在想要复制的每个标准 VM 上部署的组件。 它可以捕获标准 VM 上的数据写入，并将其转发到进程服务器。 阅读[复制的计算机先决条件](../../site-recovery/vmware-walkthrough-overview.md)。

此图显示了这些组件的交互方式：

![Site Recovery 组件的交互][15]

> [!NOTE]
> Site Recovery 不支持迁移存储空间磁盘。

有关其他方案的其他组件，请参阅[方案体系结构](../../site-recovery/vmware-walkthrough-overview.md)。

## <a name="azure-essentials"></a>Azure 概要

这些是此迁移方案的 Azure 要求：

* Azure 订阅。
* 用于存储复制数据的 Azure 高级存储帐户。
* 故障转移时创建的 VM 要连接到的 Azure 虚拟网络。 Azure 虚拟网络必须位于 Site Recovery 运行所在的同一区域。
* 存储复制日志的 Azure 标准存储帐户。 可以是要迁移的 VM 磁盘的同一存储帐户。

## <a name="prerequisites"></a>先决条件

* 在上一部分中了解相关的迁移方案组件。
* 了解 [Site Recovery 中的故障转移](../../site-recovery/site-recovery-failover.md)，规划停机时间。

## <a name="setup-and-migration-steps"></a>设置和迁移步骤

可以使用 Site Recovery 在区域之间或者在同一区域内部迁移 Azure IaaS VM。 以下说明是在[将 VMware VM 或物理服务器复制到 Azure](../../site-recovery/vmware-walkthrough-overview.md) 一文的基础上，专门针对此迁移方案编写的。 除了本文中所述的说明以外，请单击相关的链接了解详细步骤。

### <a name="step-1-create-a-recovery-services-vault"></a>步骤 1：创建恢复服务保管库

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 选择“新建” > “管理” > “备份和 Site Recovery (OMS)”。 或者，可以选择“浏览” > “恢复服务保管库” > “添加”。 
3. 指定将 VM 复制到的区域。 若要在同一区域中迁移，请选择源 VM 和源存储帐户所在的区域。 

### <a name="step-2-choose-your-protection-goals"></a>步骤 2：选择保护目标 

1. 在想要安装配置服务器的 VM 上，打开 [Azure 门户](https://portal.azure.com)。
2. 转到“恢复服务保管库” > “设置” > “Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。

   ![浏览到“保护目标”窗格][2]

3. 在“保护目标”下，从第一个下拉列表中选择“到 Azure”。 在第二个下拉列表中，选择“未虚拟化/其他”，并选择“确定”。

   ![带有填写框的“保护目标”窗格][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>步骤 3：设置源环境（配置服务器）

1. 转到“准备基础结构” > “准备源” > “添加服务器”窗格，下载 Azure Site Recovery 统一安装程序和保管库注册密钥。 
 
   需要使用保管库注册密钥来运行统一安装程序。 生成的密钥有效期为 5 天。

   ![浏览到“添加服务器”窗格][4]

2. 在“添加服务器”窗格中添加配置服务器。

   ![选定“配置服务器”的“添加服务器”窗格][5]

3. 在用作配置服务器的 VM 上，运行统一安装程序以安装配置服务器和进程服务器。 可以[参考提供的屏幕截图逐步完成安装](../../site-recovery/vmware-walkthrough-overview.md)。 可以参考以下屏幕截图了解针对此迁移方案指定的步骤。

   1. 在“开始之前”中，选择“安装配置服务器和进程服务器”。

      ![“准备工作”页][6]

   2. 在“注册”中，通过浏览查找并选择从保管库下载的注册密钥。

      ![注册页][7]

   3. 在“环境详细信息”中，选择是否要复制 VMware VM。 对于此迁移方案，请选择“否”。

      ![环境详细信息页][8]

4. 安装完成后，请在“Microsoft Azure Site Recovery 配置服务器”窗口中执行以下操作：
 
   1. 使用“管理帐户”选项卡创建可供 Site Recovery 用于执行自动发现的帐户。 （在保护物理计算机的相关方案中，没有必要设置帐户，但至少要有一个帐户才能启用以下步骤之一。 在此情况下，可以指定任意帐户和密码。） 
   2. 使用“保管库注册”选项卡上上传保管库凭据文件。

      ![“保管库注册”选项卡][9]

### <a name="step-4-set-up-the-target-environment"></a>步骤 4：设置目标环境

选择“准备基础结构” > “目标”，并指定要在故障转移后用于 VM 的部署模型。 可以根据方案选择“经典”或“Resource Manager”。

![“目标”窗格][10]

Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。 

> [!NOTE]
> 如果使用高级存储帐户保存复制的数据，则需要设置附加的标准存储帐户来存储复制日志。

### <a name="step-5-set-up-replication-settings"></a>步骤 5：设置复制设置

要验证配置服务器是否已成功与创建的复制策略相关联，请遵循[设置复制设置](../../site-recovery/vmware-walkthrough-overview.md)。

### <a name="step-6-plan-capacity"></a>步骤 6：规划容量

1. 使用 [Capacity Planner](../../site-recovery/site-recovery-capacity-planner.md) 根据复制需要准确估算网络带宽、存储和其他要求。 
2. 完成后，请在“是否已完成容量规划?”中选择“是，已完成”。

   ![确认是否已完成容量规划的对话框][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>步骤 7：安装移动服务并启用复制

1. 可以选择向源 VM 执行[推送安装](../../site-recovery/vmware-walkthrough-overview.md)，或者在源 VM 上[手动安装移动服务](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md)。 可在提供的链接中找到推送安装的要求以及手动安装程序的路径。 如果执行手动安装，可能需要使用内部 IP 地址来查找配置服务器。

   ![“配置服务器详细信息”页][12]

   故障转移 VM 包含两个临时磁盘：一个磁盘来自主 VM，另一个磁盘是在恢复区域中预配 VM 期间创建的。 若要在复制之前排除临时磁盘，请在启用复制之前安装移动服务。 若要详细了解如何排除临时磁盘，请参阅[从复制中排除磁盘](../../site-recovery/vmware-walkthrough-overview.md)。

2. 请按如下所述启用复制：
   1. 选择“复制应用程序” > “源”。 首次启用复制后，请在保管库中选择“+复制”，对其他计算机启用复制。
   2. 在步骤 1 中，将“源”设置为进程服务器。
   3. 在步骤 2 中，指定故障转移后的部署模型、要迁移到的高级存储帐户、用于保存日志的标准存储帐户，以及要故障转移到的虚拟网络。
   4. 在步骤 3 中，按 IP 地址添加受保护的 VM。 （要找到它们可能需要用到内部 IP 地址。）
   5. 在步骤 4 中，通过选择前面在进程服务器上设置的帐户来配置属性。
   6. 在步骤 5 中，选择前面在“步骤 5：设置复制设置”中创建的复制策略。
   7. 选择“确定”。

   > [!NOTE]
   > 解除分配并再次启动 Azure VM 后，无法保证它会获得相同的 IP 地址。 如果配置服务器/进程服务器或受保护 Azure VM 的 IP 地址发生变化，此方案中的复制可能无法正常工作。

   ![选定了“源”的“启用复制”窗格][13]

设计 Azure 存储环境时，我们建议针对可用性集中的每个 VM 使用不同的存储帐户。 我们建议按照存储层中的最佳做法[为每个可用性集使用多个存储帐户](../../virtual-machines/windows/manage-availability.md)。 将 VM 磁盘分配到多个存储帐户有助于改善存储可用性，以及在整个 Azure 存储基础结构中分配 I/O。

如果 VM 位于可用性集中，我们强烈建议分多次迁移多个 VM，而不要将所有 VM 的磁盘都复制到一个存储帐户。 这是为了避免同一个可用性集中的 VM 共享单个存储帐户。 使用“启用复制”窗格为每个 VM 设置目标存储帐户，一次设置一个。
 
可以根据需要选择故障转移后的部署模型。 如果选择 Azure 资源管理器作为故障转移后的部署模型，则可将一个 VM（资源管理器）故障转移至另一个 VM（资源管理器），或将一个 VM（经典版）故障转移至一个 VM（资源管理器）。

### <a name="step-8-run-a-test-failover"></a>步骤 8：运行测试故障转移

要检查复制是否完成，请选择“Site Recovery”实例，并单击“设置” > “已复制的项”。 此时会显示复制过程的状态和完成百分比。 

初始复制完成后，请运行测试故障转移来验证复制策略。 有关测试故障转移的详细步骤，请参阅 [在 Site Recovery 中运行测试故障转移](../../site-recovery/vmware-walkthrough-overview.md)。 

> [!NOTE]
> 运行故障转移之前，请确保 VM 和复制策略满足要求。 有关运行测试故障转移的详细信息，请阅读[在 Site Recovery 中执行到 Azure 的测试故障转移](../../site-recovery/site-recovery-test-failover-to-azure.md)。

可在“设置” > “作业” > “YOUR_FAILOVER_PLAN_NAME”中查看测试故障转移的状态。 在窗格中，可以看到具体的步骤，以及成功/失败结果。 如果测试故障转移在执行任一步骤时失败，请选择该步骤查看错误消息。 

### <a name="step-9-run-a-failover"></a>步骤 9：运行故障转移

完成测试故障转移后，请运行故障转移，将磁盘迁移到高级存储并复制 VM 实例。 请遵循[运行故障转移](../../site-recovery/site-recovery-failover.md#run-a-failover)中的详细步骤。 

请务必选择“关闭 VM 并同步最新数据”。 此操作将指定 Site Recovery 应尝试关闭受保护的 VM 并同步数据，以便对最新版的数据进行故障转移。 如果不选择此选项或尝试不成功，系统将从 VM 的最近恢复点开始故障转移。 

Site Recovery 将创建类型与支持高级存储的 VM 相同或类似的 VM 实例。 可以参阅 [Windows 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)或 [Linux 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)了解各种 VM 实例的性能和价格。

## <a name="post-migration-steps"></a>迁移后的步骤

1. **将复制的 VM 配置到可用性集（如果适用）**。 Site Recovery 不支持连同可用性集一起迁移 VM。 根据复制的 VM 的部署，执行以下操作之一：
   * 对于使用经典部署模型创建的 VM：在 Azure 门户中将 VM 添加到可用性集。 有关详细步骤，请参阅[将现有虚拟机添加到可用性集](../../virtual-machines/windows/classic/configure-availability.md#addmachine)。
   * 对于通过资源管理器部署模型创建的 VM：保存 VM 的配置，并在可用性集中删除再重新创建 VM。 为此，请使用 [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)（设置 Azure Resource Manager VM 可用性集）中所述的脚本。 运行此脚本之前，请检查其自身的限制并规划停机时间。

2. **删除旧 VM 和磁盘**。 请确保高级磁盘与源磁盘一致，并且新 VM 执行的功能与源 VM 相同。 在资源管理器部署模型中，通过 Azure 门户删除源存储帐户中的 VM 和磁盘。 在经典部署模型中，可通过经典门户或 Azure 门户删除 VM 和磁盘。 如果出现了即使删除 VM 也无法删除磁盘的问题，请参阅 [排查在删除 VHD 时遇到的错误](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)。

3. **清理 Azure Site Recovery 基础结构**。 如果不再需要 Site Recovery，可清理其基础结构。 删除复制的项、配置服务器和恢复策略，然后删除 Azure Site Recovery 保管库。

## <a name="troubleshooting"></a>故障排除

* [监视虚拟机和物理服务器的保护及其故障排除](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>后续步骤

有关虚拟机迁移的特定方案，请参阅以下资源：

* [Migrate Azure Virtual Machines between Storage Accounts](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)（在存储帐户之间迁移 Azure 虚拟机）
* [创建 Windows Server VHD 并将其上传到 Azure](../../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [创建并上传包含 Linux 操作系统的虚拟硬盘](../../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
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

