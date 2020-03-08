---
title: Azure SAP HANA（大型实例）操作系统升级 | Microsoft Docs
description: 执行 Azure SAP HANA（大型实例）操作系统升级
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a0a5d39a7cb2162186291ea534a623ef45c40d4
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675629"
---
# <a name="operating-system-upgrade"></a>操作系统升级
本文档介绍 HANA 大型实例操作系统升级的详细信息。

>[!NOTE]
>OS 升级是客户的责任，Microsoft 操作支持可指导你访问升级期间要注意的关键区域。 在计划升级前，你还应咨询操作系统供应商。

在配置过程中，Microsoft 运营团队会安装操作系统。
随着时间推移，你需要维护 HLI 单元的操作系统（例如，修补、优化、升级等）。

在对操作系统进行重大更改（例如，将 SP1 升级到 SP2）之前，需要通过打开支持票证来与 Microsoft 运营团队联系以进行查阅。

在票证中包括：

* 你的 HLI 订阅 ID。
* 你的服务器名称。
* 你打算应用的修补程序级别。
* 你计划此更改的日期。 

建议你至少比理想的升级日期提前一周来提交此票证，因为需要让操作团队检查你的服务器刀片是否需要进行固件升级。


有关不同 Linux 版本的不同 SAP HANA 版本的支持矩阵，请参阅 [SAP 说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。


## <a name="known-issues"></a>已知问题

以下是升级过程中几个常见的已知问题：
- 在 II 类 SKU 上，Software Foundation Software (SFS) 会在操作系统升级后移除。 在 OS 升级后，需要重新安装兼容的 SFS。
- 以太网卡驱动程序（ENIC 和 FNIC）会回滚到旧版本。 升级后需要重新安装兼容的驱动程序版本。

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA 大型实例（类型 I）建议的配置

由于修补、系统升级和客户所做的更改，操作系统配置可能会随着时间的推移而发生变化。 此外，Microsoft 还识别了现有系统所需的更新，以确保以最佳方式配置这些更新以实现最佳性能和复原能力。 以下说明概述了解决网络性能、系统稳定性和最佳 HANA 性能的建议。

### <a name="compatible-enicfnic-driver-versions"></a>兼容的 eNIC/fNIC 驱动程序版本
  为了获得正确的网络性能和系统稳定性，建议确保按以下兼容性表中所述安装 eNIC 和 fNIC 驱动程序的特定于 OS 的适当版本。 服务器交付到具有兼容版本的客户。 请注意，在某些情况下，在操作系统/内核修补过程中，驱动程序可以回滚到默认的驱动程序版本。 确保适当的驱动程序版本正在运行操作系统/内核修补后操作。
       
      
  |  操作系统供应商    |  OS 包版本     |  固件版本  |  eNIC 驱动程序 |  fNIC 驱动程序 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>用于驱动程序升级和清理旧 rpm 包的命令
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>要确认的命令
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB 更新失败
Azure HANA 大型实例上的 SAP （类型为 I）在升级后可能处于无法启动状态。 以下过程将解决此问题。
#### <a name="execution-steps"></a>执行步骤


*   执行 `multipath -ll` 命令。
*   获取大小约为50G 的 LUN ID，或者使用命令： `fdisk -l | grep mapper`
*   用行 `/dev/mapper/<LUN ID>`更新 `/etc/default/grub_installdevice` 的文件。 示例：/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID 不同于服务器。


### <a name="disable-edac"></a>禁用 EDAC 
   "错误检测和更正（EDAC）" 模块有助于检测和更正内存错误。 但 Azure SAP HANA 大型实例的基础硬件（类型 I）已在执行相同的功能。 在硬件和操作系统（OS）级别启用相同的功能可能会导致冲突，并可能导致服务器意外关闭。 因此，建议禁用操作系统中的模块。

#### <a name="execution-steps"></a>执行步骤

* 检查是否已启用 EDAC 模块。 如果在下面的命令中返回输出，则表示该模块已启用。 
```
lsmod | grep -i edac 
```
* 通过将以下行追加到文件来禁用模块 `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
需要重新启动才能进行更改。 执行 `lsmod` 命令，并验证输出中是否不存在该模块。


### <a name="kernel-parameters"></a>内核参数
   请确保应用 `transparent_hugepage`、`numa_balancing`、`processor.max_cstate`、`ignore_ce` 和 `intel_idle.max_cstate` 的正确设置。

* intel_idle。 max_cstate = 1
* processor。 max_cstate = 1
* transparent_hugepage = 从不
* numa_balancing = 禁用
* mce = ignore_ce


#### <a name="execution-steps"></a>执行步骤

* 将这些参数添加到文件中的 `GRB_CMDLINE_LINUX` 行 `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 创建新的 grub 文件。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* 重新启动系统。


## <a name="next-steps"></a>后续步骤
- 有关 I 类 SKU 操作系统备份的信息，请参阅[备份和恢复](hana-overview-high-availability-disaster-recovery.md)。
- 请参阅第 ii 类 SKU 类的[修订版3橡皮类型的操作系统备份](os-backup-type-ii-skus.md)。
