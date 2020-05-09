---
title: Azure VMware 解决方案（通过 CloudSimple）-为 Oracle RAC 优化 CloudSimple 私有云
description: 描述如何部署新群集并为 Oracle 真实应用程序群集（RAC）安装和配置优化 VM
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f657e18d7185d6b3c63ac8f1424da9d36d4189e9
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793034"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>优化 CloudSimple 私有云以安装 Oracle RAC

你可以在 CloudSimple 私有云环境中部署 Oracle 真实应用程序群集（RAC）。 本指南介绍了如何部署新群集并优化 Oracle RAC 解决方案的 VM。 完成本主题中的步骤后，即可安装和配置 Oracle RAC。

## <a name="storage-policy"></a>存储策略

成功实现 Oracle RAC 需要群集中有足够数量的节点。  在 vSAN 存储策略中，可容忍的故障（FTT）将应用于用于存储数据库、日志和重做磁盘的数据磁盘。  需要有效地容忍故障的节点数量为 2N + 1，其中 N 是 FTT 的值。

示例：如果所需的 FTT 为2，则群集中的节点总数必须为 2 * 2 + 1 = 5。

## <a name="overview-of-deployment"></a>部署概述

以下部分介绍如何为 Oracle RAC 设置 CloudSimple 私有云环境。

1. 磁盘配置最佳做法
2. 部署 CloudSimple 私有云 vSphere 群集
3. 为 Oracle RAC 设置网络
4. 设置 vSAN 存储策略
5. 创建 Oracle Vm 并创建共享 VM 磁盘
6. 设置 VM 到主机的相关性规则

## <a name="best-practices-for-disk-configuration"></a>磁盘配置最佳做法

Oracle RAC 虚拟机具有多个磁盘，这些磁盘用于特定功能。  共享磁盘安装在 Oracle RAC 群集使用的所有虚拟机上。  仅在单个虚拟机上装载操作系统和软件安装磁盘。  

![Oracle RAC 虚拟机磁盘概述](media/oracle-vm-disks-overview.png)

以下示例使用下表中定义的磁盘。

| 磁盘                                      | 目标                                       | 共享磁盘 |
|-------------------------------------------|-----------------------------------------------|-------------|
| 操作系统                                        | 操作系统磁盘                         | 否          |
| 格                                      | Oracle 网格软件的安装位置     | 否          |
| DATABASE                                  | Oracle 数据库软件的安装位置 | 否          |
| ORAHOME                                   | Oracle 数据库二进制文件的基准位置    | 否          |
| DATA1、DATA2、DATA3、DATA4                | 存储 Oracle 数据库文件的磁盘   | 是         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | 重做日志磁盘                                | 是         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | 投票磁盘                                  | 是         |
| FRA1, FRA2                                | 快速恢复区域磁盘                      | 是         |

![Oracle 虚拟机磁盘配置](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>虚拟机配置

* 每个虚拟机都配置有四个 SCSI 控制器。
* SCSI 控制器类型设置为 "VMware 半虚拟"。
* 创建了多个虚拟磁盘（.vmdk）。
* 磁盘装载到不同的 SCSI 控制器上。
* 为共享群集磁盘设置了多个编写器共享类型。
* 为确保磁盘的高可用性，定义了 vSAN 存储策略。

### <a name="operating-system-and-software-disk-configuration"></a>操作系统和软件磁盘配置

每个 Oracle 虚拟机均配置有多个磁盘用于主机操作系统、交换、软件安装和其他 OS 功能。  这些磁盘不会在虚拟机之间共享。  

* 每个虚拟机的三个磁盘配置为虚拟磁盘，并装载在 Oracle RAC 虚拟机上。
    * OS 磁盘
    * 用于存储 Oracle 网格的磁盘安装文件
    * 用于存储 Oracle 数据库安装文件的磁盘
* 磁盘可以配置为**精简预配**。
* 每个磁盘都装载在第一个 SCSI 控制器（SCSI0）上。  
* 共享设置为 "**无共享**"。
* 使用 vSAN 策略对存储定义冗余。  

![Oracle RAC 数据磁盘组配置](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>数据磁盘配置

数据磁盘主要用于存储数据库文件。  

* 四个磁盘配置为虚拟磁盘，并装载在所有 Oracle RAC 虚拟机上。
* 每个磁盘都装载到不同的 SCSI 控制器上。
* 每个虚拟磁盘均配置为**厚空间预配零**。  
* 共享设置为**多作者**。  
* 磁盘必须配置为自动存储管理（ASM）磁盘组。  
* 使用 vSAN 策略对存储定义冗余。  
* ASM 冗余设置为**外部**冗余。

![Oracle RAC 数据磁盘组配置](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>重做日志磁盘配置

重做日志文件用于存储对数据库所做更改的副本。  如果在发生任何故障后需要恢复数据，则使用日志文件。

* 重做日志磁盘必须配置为多个磁盘组。  
* 在所有 Oracle RAC 虚拟机上创建并装入六个磁盘。
* 磁盘装载到不同的 SCSI 控制器上
* 每个虚拟磁盘均配置为**厚空间预配零**。
* 共享设置为**多作者**。  
* 磁盘必须配置为两个 ASM 磁盘组。
* 每个 ASM 磁盘组都包含三个磁盘，它们位于不同的 SCSI 控制器上。  
* ASM 冗余设置为 "**正常**冗余"。
* 同时在 ASM 重做日志组上创建了五个重做日志文件

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

投票磁盘提供仲裁磁盘功能作为额外的通信通道，以避免出现任何裂脑情况。

* 在所有 Oracle RAC 虚拟机上创建并装入五个磁盘。
* 磁盘装载到一个 SCSI 控制器上
* 每个虚拟磁盘均配置为**厚空间预配零**。
* 共享设置为**多作者**。  
* 磁盘必须配置为 ASM 磁盘组。  
* ASM 冗余设置为 "**高**冗余"。

![Oracle RAC 投票磁盘组配置](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 快速恢复区域磁盘配置（可选）

"快速恢复" 区域（FRA）是 Oracle ASM 磁盘组管理的文件系统。  FRA 为备份和恢复文件提供共享存储位置。 Oracle 在快速恢复区域中创建存档的日志和闪回日志。 Oracle 恢复管理器（RMAN）可以选择在 "快速恢复" 区域中存储其备份集和映像副本，并在媒体恢复期间还原文件时使用。

* 在所有 Oracle RAC 虚拟机上创建并装入两个磁盘。
* 磁盘装载到不同的 SCSI 控制器上
* 每个虚拟磁盘均配置为**厚空间预配零**。
* 共享设置为**多作者**。  
* 磁盘必须配置为 ASM 磁盘组。  
* ASM 冗余设置为**外部**冗余。

![Oracle RAC 投票磁盘组配置](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>部署 CloudSimple 私有云 vSphere 群集

若要在私有云上部署 vSphere 群集，请遵循以下过程：

1. 在 CloudSimple 门户中，[创建私有云](create-private-cloud.md)。 CloudSimple 在新创建的私有云中创建一个名为 "cloudowner" 的默认 vCenter 用户。 有关默认私有云用户和权限模型的详细信息，请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。  此步骤将为私有云创建主管理群集。

2. 在 CloudSimple 门户中，使用新群集[展开私有云](expand-private-cloud.md)。  此群集将用于部署 Oracle RAC。  根据所需的容错容错（最少三个节点）选择节点数。

## <a name="set-up-networking-for-oracle-rac"></a>为 Oracle RAC 设置网络

1. 在私有云中，[创建两个 vlan](create-vlan-subnet.md)，一个用于 oracle 公用网络，另一个用于 oracle 专用网络并分配适当的子网 CIDRs。
2. 创建 Vlan 后，[在私有云 vCenter 上创建分布式端口组](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)。
3. 在管理群集上为 Oracle 环境设置[DHCP 和 DNS 服务器虚拟机](dns-dhcp-setup.md)。
4. 在安装在私有云中[的 dns 服务器上配置 dns 转发](on-premises-dns-setup.md#create-a-conditional-forwarder)。

## <a name="set-up-vsan-storage-policies"></a>设置 vSAN 存储策略

vSAN 策略为 VM 磁盘上存储的数据定义允许和磁盘条带化失败。  创建 VM 时，必须在 VM 磁盘上应用创建的存储策略。

1. [登录到私有云的 vSphere 客户端](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access)。
2. 在顶部菜单中，选择 "**策略和配置文件**"。
3. 在左侧菜单中，选择 " **Vm 存储策略**"，然后选择 "**创建 vm 存储策略**"。
4. 为策略输入有意义的名称，然后单击 "**下一步**"。
5. 在 "**策略结构**" 部分，选择 "**启用 vSAN 存储的规则**"，然后单击 "**下一步**"。
6. 在 " **vSAN** > **可用性**" 部分中，为 "站点灾难容错" 选择 "**无**"。 对于 "容错失败"，请为所需的 FTT 选择**RAID 镜像**选项。
    ![vSAN 设置](media/oracle-rac-storage-wizard-vsan.png)。
7. 在 "**高级**" 部分中，选择 "每个对象的磁盘条带数"。 对于 "对象空间保留"，请选择 "**配置**"。 选择 "**禁用对象校验和**"。 单击 "**下一步**"。
8. 按照屏幕上的说明查看兼容 vSAN 数据存储的列表，查看设置，然后完成安装。

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>创建 Oracle Vm 并创建用于 Oracle 的共享 VM 磁盘

若要创建 Oracle VM，请克隆现有 VM 或创建新 VM。  本部分介绍如何创建新的 VM，然后将其克隆，以便在安装基本操作系统后创建另一个 VM。  创建 Vm 后，可以创建将磁盘添加到其中。  Oracle 群集使用共享磁盘存储、数据、日志和重做日志。

### <a name="create-vms"></a>创建 VM

1. 在 vCenter 中，单击 "**主机和群集**" 图标。 选择为 Oracle 创建的群集。
2. 右键单击该群集，然后选择 "**新建虚拟机**"。
3. 选择 "**新建虚拟机**"，然后单击 "**下一步**"。
4. 为计算机命名，选择 Oracle VM 的位置，然后单击 "**下一步**"。
5. 选择群集资源，然后单击 "**下一步**"。
6. 选择群集的 vSAN 数据存储，然后单击 "**下一步**"。
7. 保留默认的 ESXi 6.5 兼容性选择，并单击 "**下一步**"。
8. 选择要创建的 VM 的 ISO 来宾操作系统，然后单击 "**下一步**"。
9. 选择安装操作系统所需的硬盘大小。
10. 若要在另一台设备上安装应用程序，请单击 "**添加新设备**"。
11. 选择 "网络选项" 并分配为公用网络创建的分布式端口组。
12. 若要添加其他网络接口，请单击 "**添加新设备**"，然后选择为专用网络创建的分布式端口组。
13. 对于 "新 DC/DVD 驱动器"，选择包含用于首选操作系统安装的 ISO 的数据存储 ISO 文件。 选择之前上传到 Iso 和 Templates 文件夹的文件，然后单击 **"确定"**。
14. 查看设置，然后单击 **"确定"** 以创建新的 VM。
15. 开启 VM 的电源。 安装操作系统和所需的任何更新

安装操作系统后，可以克隆第二个 VM。 右键单击 VM 条目，然后选择 "和克隆" 选项。

### <a name="create-shared-disks-for-vms"></a>为 Vm 创建共享磁盘

Oracle 使用共享磁盘来存储数据、日志和重做日志文件。  可以在 vCenter 上创建共享磁盘，并将其装载到这两个 Vm 上。  为了获得更高的性能，请将数据磁盘放置在不同的 SCSI 控制器上以下步骤显示如何在 vCenter 上创建共享磁盘，并将其附加到虚拟机。 vCenter Flash 客户端用于修改 VM 属性。

#### <a name="create-disks-on-the-first-vm"></a>在第一个 VM 上创建磁盘

1. 在 vCenter 中，右键单击其中一个 Oracle Vm，然后选择 "**编辑设置**"。
2. 在 "新设备" 部分中，选择 " **SCSI 控制器**"，并单击 "**添加**"。
3. 在 "新设备" 部分中，选择 "**新建硬盘**"，然后单击 "**添加**"。
4. 展开 "新建硬盘" 的属性。
5. 指定硬盘的大小。
6. 将 VM 存储策略指定为你前面定义的 vSAN 存储策略。
7. 选择该位置作为 vSAN 数据存储上的文件夹。 此位置可帮助浏览磁盘并将磁盘附加到另一个 VM。
8. 对于磁盘设置，请选择 "**厚预配预先零**"。
9. 对于共享，请指定**多个编写器**。
10. 对于 "虚拟设备" 节点，选择在步骤2中创建的新的 SCSI 控制器。

    ![在第一个 VM 上创建磁盘](media/oracle-rac-new-hard-disk.png)

对于 Oracle 数据、日志和重做日志文件所需的所有新磁盘，重复步骤2到10。

#### <a name="attach-disks-to-second-vm"></a>将磁盘附加到第二个 VM

1. 在 vCenter 中，右键单击其中一个 Oracle Vm，然后选择 "**编辑设置**"。
2. 在 "新设备" 部分中，选择 " **SCSI 控制器**"，并单击 "**添加**"。
3. 在 "新设备" 部分，选择 "**现有硬盘**"，然后单击 "**添加**"。
4. 浏览到为第一个 VM 创建磁盘的位置，并选择 VMDK 文件。
5. 将 VM 存储策略指定为你前面定义的 vSAN 存储策略。
6. 对于磁盘设置，请选择 "**厚预配预先零**"。
7. 对于共享，请指定**多个编写器**。
8. 对于 "虚拟设备" 节点，选择在步骤2中创建的新的 SCSI 控制器。

    ![在第一个 VM 上创建磁盘](media/oracle-rac-existing-hard-disk.png)

对于 Oracle 数据、日志和重做日志文件所需的所有新磁盘，重复步骤2–7。

## <a name="set-up-vm-host-affinity-rules"></a>设置 VM 主机相关性规则

VM 到主机关联规则确保 VM 在所需的主机上运行。  你可以在 vCenter 上定义规则，以确保 Oracle VM 在具有足够资源的主机上运行，并满足任何特定的许可要求。

1. 在 CloudSimple 门户中，[升级](escalate-private-cloud-privileges.md)cloudowner 用户的权限。
2. 登录到私有云的 vSphere 客户端。
3. 在 vSphere 客户端中，选择部署了 Oracle Vm 的群集，然后单击 "**配置**"。
4. 在 "配置" 下，选择 " **VM/主机组**"。
5. 单击“+”  。
6. 添加 VM 组。 选择 " **VM 组**" 作为 "类型"。 输入组的名称。 选择 Vm，然后单击 **"确定"** 以创建组。
6. 添加主机组。 选择 "**主机组**" 作为 "类型"。 输入组的名称。 选择将在其中运行 Vm 的主机，然后单击 **"确定"** 以创建组。
7. 若要创建规则，请单击 " **VM/主机规则**"。
8. 单击“+”  。
9. 输入规则的名称，并选中 "**启用**"。
10. 对于 "规则类型"，选择 "**要承载的虚拟机**"。
11. 选择包含 Oracle Vm 的 VM 组。
12. 选择 **"必须在此组中的主机上运行"**。
13. 选择创建的主机组。
14. 单击 **"确定"** 创建规则。

## <a name="references"></a>参考

* [关于 vSAN 策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [共享 Vmdk 的 VMware 多编写器属性](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
