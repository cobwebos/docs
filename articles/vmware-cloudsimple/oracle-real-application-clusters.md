---
title: Azure VMware 解决方案（按云简单 - 为 Oracle RAC 优化云简单私有云）
description: 描述如何部署新群集并优化针对 Oracle 实际应用程序群集 （RAC） 安装和配置的 VM
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b945beaa7497e1ad19315bacf1284dd0cbc24d6a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868078"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>优化云简单私有云，以安装 Oracle RAC

您可以在云简单私有云环境中部署 Oracle 实际应用程序群集 （RAC）。 本指南介绍如何部署新群集和优化 Oracle RAC 解决方案的 VM。 完成本主题中的步骤后，可以安装和配置 Oracle RAC。

## <a name="storage-policy"></a>存储策略

成功实施 Oracle RAC 需要群集中的足够数量的节点。  在 vSAN 存储策略中，容容失败 （FTT） 应用于用于存储数据库、日志和重做磁盘的数据磁盘。  有效容忍故障所需的节点数为 2N+1，其中 N 是 FTT 的值。

示例：如果所需的 FTT 为 2，则群集中的节点总数必须为 2*2+1 = 5。

## <a name="overview-of-deployment"></a>部署概述

以下各节介绍如何为 Oracle RAC 设置云简单私有云环境。

1. 磁盘配置的最佳做法
2. 部署云简单私有云 vSphere 群集
3. 为 Oracle RAC 设置网络
4. 设置 vSAN 存储策略
5. 创建 Oracle VM 并创建共享 VM 磁盘
6. 设置 VM 到主机的关联规则

## <a name="best-practices-for-disk-configuration"></a>磁盘配置的最佳做法

Oracle RAC 虚拟机有多个磁盘，用于特定功能。  共享磁盘安装在 Oracle RAC 群集使用的所有虚拟机上。  操作系统和软件安装磁盘仅安装在单个虚拟机上。  

![Oracle RAC 虚拟机磁盘概述](media/oracle-vm-disks-overview.png)

下面的示例使用下表中定义的磁盘。

| 磁盘                                      | 目的                                       | 共享磁盘 |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | 操作系统磁盘                         | 否          |
| 网 格                                      | Oracle 网格软件的安装位置     | 否          |
| DATABASE                                  | 安装 Oracle 数据库软件的位置 | 否          |
| 奥拉霍马                                   | Oracle 数据库二进制文件的基本位置    | 否          |
| 数据1， 数据2， 数据3， 数据4                | 存储 Oracle 数据库文件的磁盘   | 是         |
| 重做 1、重做 2、重做 3、重做 4、重做 5、重做6  | 重做日志磁盘                                | 是         |
| OCR1、 OCR2、 OCR3、 OCR4、 OCR5              | 投票磁盘                                  | 是         |
| FRA1， FRA2                                | 快速恢复区域磁盘                      | 是         |

![Oracle 虚拟机磁盘配置](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>虚拟机配置

* 每个虚拟机都配置了四个 SCSI 控制器。
* SCSI 控制器类型设置为 VMware 半虚拟。
* 创建了多个虚拟磁盘 （.vmdk）。
* 磁盘安装在不同的 SCSI 控制器上。
* 为共享群集磁盘设置了多写入器共享类型。
* vSAN 存储策略的定义是确保磁盘的高可用性。

### <a name="operating-system-and-software-disk-configuration"></a>操作系统和软件磁盘配置

每个 Oracle 虚拟机都配置了多个磁盘，用于主机操作系统、交换、软件安装和其他操作系统功能。  这些磁盘不会在虚拟机之间共享。  

* 每个虚拟机的三个磁盘配置为虚拟磁盘并安装在 Oracle RAC 虚拟机上。
    * OS 磁盘
    * 用于存储 Oracle 网格的磁盘安装文件
    * 用于存储 Oracle 数据库安装文件的磁盘
* 磁盘可以配置为**精简预配**。
* 每个磁盘都安装在第一个 SCSI 控制器 （SCSI0） 上。  
* 共享设置为 **"无共享**"。
* 冗余在存储上使用 vSAN 策略定义。  

![Oracle RAC 数据磁盘组配置](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>数据磁盘配置

数据磁盘主要用于存储数据库文件。  

* 四个磁盘配置为虚拟磁盘，并安装在所有 Oracle RAC 虚拟机上。
* 每个磁盘都安装在不同的 SCSI 控制器上。
* 每个虚拟磁盘都配置为 **"厚备配"已归零**。  
* 共享设置为**多写入器**。  
* 磁盘必须配置为自动存储管理 （ASM） 磁盘组。  
* 冗余在存储上使用 vSAN 策略定义。  
* ASM 冗余设置为**外部**冗余。

![Oracle RAC 数据磁盘组配置](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>重做日志磁盘配置

重做日志文件用于存储对数据库所做的更改的副本。  当任何故障后需要恢复数据时，将使用日志文件。

* 重做日志磁盘必须配置为多个磁盘组。  
* 所有 Oracle RAC 虚拟机上创建并装载了六个磁盘。
* 磁盘安装在不同的 SCSI 控制器上
* 每个虚拟磁盘都配置为 **"厚备配"已归零**。
* 共享设置为**多写入器**。  
* 磁盘必须配置为两个 ASM 磁盘组。
* 每个 ASM 磁盘组包含三个磁盘，它们位于不同的 SCSI 控制器上。  
* ASM 冗余设置为 **"正常**冗余"。
* 在两个 ASM 重做日志组创建五个重做日志文件

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC 重做日志磁盘组配置](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle 投票磁盘配置

投票磁盘提供仲裁磁盘功能作为额外的通信通道，以避免任何分裂的大脑情况。

* 在所有 Oracle RAC 虚拟机上创建并装载五个磁盘。
* 磁盘安装在一个 SCSI 控制器上
* 每个虚拟磁盘都配置为 **"厚备配"已归零**。
* 共享设置为**多写入器**。  
* 磁盘必须配置为 ASM 磁盘组。  
* ASM 冗余设置为**高**冗余。

![Oracle RAC 投票磁盘组配置](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 快速恢复区域磁盘配置（可选）

快速恢复区域 （FRA） 是由 Oracle ASM 磁盘组管理的文件系统。  FRA 为备份和恢复文件提供共享存储位置。 Oracle 在快速恢复区域中创建存档的日志和闪回日志。 Oracle 恢复管理器 （RMAN） 可以选择将其备份集和映像副本存储在快速恢复区域中，并在媒体恢复期间还原文件时使用它。

* 在所有 Oracle RAC 虚拟机上创建并装载两个磁盘。
* 磁盘安装在不同的 SCSI 控制器上
* 每个虚拟磁盘都配置为 **"厚备配"已归零**。
* 共享设置为**多写入器**。  
* 磁盘必须配置为 ASM 磁盘组。  
* ASM 冗余设置为**外部**冗余。

![Oracle RAC 投票磁盘组配置](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>部署云简单私有云 vSphere 群集

要在私有云上部署 vSphere 群集，请按照以下步骤操作：

1. 从云简单门户创建[私有云](create-private-cloud.md)。 CloudSimple 在新创建的私有云中创建名为"云所有者"的默认 vCenter 用户。 有关默认私有云用户和权限模型的详细信息，请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。  此步骤为私有云创建主管理群集。

2. 从云简单门户，使用新群集[扩展私有云](expand-private-cloud.md)。  此群集将用于部署 Oracle RAC。  根据所需的容错（最少三个节点）选择节点数。

## <a name="set-up-networking-for-oracle-rac"></a>为 Oracle RAC 设置网络

1. 在私有云中，[创建两个 VLAN，](create-vlan-subnet.md)一个用于 Oracle 公共网络，一个用于 Oracle 专用网络，并分配适当的子网 CIDR。
2. 创建 VLAN 后，[在私有云 vCenter 上创建分布式端口组](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)。
3. 在 Oracle 环境的管理群集上设置[DHCP 和 DNS 服务器虚拟机](dns-dhcp-setup.md)。
4. 在私有云中安装[的 DNS 服务器上配置 DNS 转发](on-premises-dns-setup.md#create-a-conditional-forwarder)。

## <a name="set-up-vsan-storage-policies"></a>设置 vSAN 存储策略

vSAN 策略定义 VM 磁盘上存储的数据的允许和磁盘条带化的故障。  创建存储策略必须在创建 VM 时应用于 VM 磁盘。

1. [登录到私有云的 vSphere 客户端](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access)。
2. 从顶部菜单中，选择 **"策略和配置文件**"。
3. 从左侧菜单中，选择**VM 存储策略**，然后选择 **"创建 VM 存储策略**"。
4. 输入策略的有意义的名称，然后单击 **"下一步**"。
5. 在 **"策略结构**"部分中，选择**启用 vSAN 存储的规则**，然后单击 **"下一步**"。
6. 在**vSAN** > **可用性**部分中，为站点容差选择 **"无**"。 对于无法容忍的故障，选择所需 FTT 的**RAID - 镜像**选项。
    ![vSAN](media/oracle-rac-storage-wizard-vsan.png)设置 。
7. 在 **"高级"** 部分中，选择每个对象的磁盘条带数。 对于对象空间预留，选择 **"粗配置**"。 选择**禁用对象校验和**。 单击 **"下一步**"。
8. 按照屏幕上的说明查看兼容的 vSAN 数据存储的列表、查看设置并完成设置。

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>创建 Oracle VM 并为 Oracle 创建共享 VM 磁盘

要为 Oracle 创建 VM，请克隆现有 VM 或创建新 VM。  本节介绍如何创建新 VM，然后在安装基本操作系统后克隆它以创建第二个 VM。  创建 VM 后，可以创建向它们添加磁盘。  Oracle 群集使用共享磁盘来存储、数据、日志和重做日志。

### <a name="create-vms"></a>创建 VM

1. 在 vCenter 中，单击 **"主机和群集"** 图标。 选择为 Oracle 创建的群集。
2. 右键单击群集并选择 **"新虚拟机**"。
3. 选择 **"创建新虚拟机**"，然后单击"**下一步**"。
4. 命名计算机，选择 Oracle VM 的位置，然后单击 **"下一步**"。
5. 选择群集资源，然后单击 **"下一步**"。
6. 选择群集的 vSAN 数据存储，然后单击 **"下一步**"。
7. 保留默认 ESXi 6.5 兼容性选择，然后单击 **"下一步**"。
8. 为要创建的 VM 选择 ISO 的来宾操作系统，然后单击"**下一步**"。
9. 选择安装操作系统所需的硬盘大小。
10. 要将应用程序安装在其他设备上，请单击"**添加新设备**"。
11. 选择网络选项并分配为公共网络创建的分布式端口组。
12. 要添加其他网络接口，请单击"**添加新设备**"并选择为专用网络创建的分布式端口组。
13. 对于新 DC/DVD 驱动器，请选择包含首选操作系统安装的 ISO 的数据存储 ISO 文件。 选择您以前上载到 ISO 和模板文件夹的文件，然后单击"**确定**"。
14. 查看设置并单击 **"确定"** 以创建新的 VM。
15. 打开 VM 电源。 安装操作系统和所需的任何更新

安装操作系统后，可以克隆第二个 VM。 右键单击 VM 条目并选择 和 克隆选项。

### <a name="create-shared-disks-for-vms"></a>为 VM 创建共享磁盘

Oracle 使用共享磁盘来存储数据、日志和重做日志文件。  您可以在 vCenter 上创建共享磁盘，并将其安装在两个 VM 上。  为了获得更高的性能，请将数据磁盘放在不同的 SCSI 控制器上，下面将演示如何在 vCenter 上创建共享磁盘，然后将其附加到虚拟机。 vCenter 闪存客户端用于修改 VM 属性。

#### <a name="create-disks-on-the-first-vm"></a>在第一个 VM 上创建磁盘

1. 在 vCenter 中，右键单击其中一个 Oracle VM 并选择 **"编辑设置**"。
2. 在新设备部分中，选择**SCSI 控制器**，然后单击"**添加**"。
3. 在新设备部分中，选择 **"新建硬盘**"，然后单击"**添加**"。
4. 展开新硬盘的属性。
5. 指定硬盘的大小。
6. 指定 VM 存储策略是您之前定义的 vSAN 存储策略。
7. 选择 vSAN 数据存储上的位置作为文件夹。 该位置有助于浏览磁盘并将磁盘附加到第二个 VM。
8. 对于磁盘预配，选择 **"密集预配"，即求归**零。
9. 要共享，请指定**多写入器**。
10. 对于虚拟设备节点，请选择步骤 2 中创建的新 SCSI 控制器。

    ![在第一个 VM 上创建磁盘](media/oracle-rac-new-hard-disk.png)

对于 Oracle 数据、日志和重做日志文件所需的所有新磁盘，重复步骤 2 = 10。

#### <a name="attach-disks-to-second-vm"></a>将磁盘连接到第二个 VM

1. 在 vCenter 中，右键单击其中一个 Oracle VM 并选择 **"编辑设置**"。
2. 在新设备部分中，选择**SCSI 控制器**，然后单击"**添加**"。
3. 在新设备部分中，选择 **"现有硬盘"，** 然后单击"**添加**"。
4. 浏览到为第一个 VM 创建磁盘的位置，然后选择 VMDK 文件。
5. 指定 VM 存储策略是您之前定义的 vSAN 存储策略。
6. 对于磁盘预配，选择 **"密集预配"，即求归**零。
7. 要共享，请指定**多写入器**。
8. 对于虚拟设备节点，请选择步骤 2 中创建的新 SCSI 控制器。

    ![在第一个 VM 上创建磁盘](media/oracle-rac-existing-hard-disk.png)

对于 Oracle 数据、日志和重做日志文件所需的所有新磁盘，重复步骤 2 + 7。

## <a name="set-up-vm-host-affinity-rules"></a>设置 VM 主机关联规则

VM 到主机的关联规则可确保 VM 在所需的主机上运行。  您可以在 vCenter 上定义规则，以确保 Oracle VM 在主机上运行，具有足够的资源，并满足任何特定的许可要求。

1. 在云简单门户[中，上报](escalate-private-cloud-privileges.md)云所有者用户的权限。
2. [登录到私有云的 vSphere 客户端](https://docs.azure.cloudsimple.com/vsphere-access)。
3. 在 vSphere 客户端中，选择部署 Oracle VM 的群集，然后单击"**配置**"。
4. 在"配置"下，选择**VM/主机组**。
5. 单击**+**。
6. 添加 VM 组。 选择**VM 组**作为类型。 输入组的名称。 选择 VM，然后单击 **"确定"** 以创建组。
6. 添加主机组。 选择**主机组**作为类型。 输入组的名称。 选择 VM 将在其中运行的主机，然后单击 **"确定"** 以创建组。
7. 要创建规则，请单击**VM/主机规则**。
8. 单击**+**。
9. 输入规则的名称，并选中 **"启用**"。
10. 对于规则类型，选择 **"虚拟机以承载**"。
11. 选择包含 Oracle VM 的 VM 组。
12. 选择**必须在此组中的主机上运行**。
13. 选择您创建的主机组。
14. 单击 **"确定"** 以创建规则。

## <a name="references"></a>参考

* [关于 vSAN 策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [用于共享 VMDK 的 VMware 多写入器属性](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
