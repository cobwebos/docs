---
title: 为 Azure 上的 SAP HANA（大型实例）类型 II SKU 执行操作系统备份和还原 | Microsoft Docs
description: 为 Azure 上的 SAP HANA（大型实例）类型 II SKU 执行操作系统备份和还原
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d5caf5836b96555e01b55d408e51f3df2407d35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657598"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>为类型 II Sku 执行 OS 备份和还原

本文档介绍为 HANA 大型实例的类型 II SKU 执行操作系统备份和还原。 

>[!NOTE]
>OS 备份脚本使用预安装在服务器中的 ReaR 软件。  

Microsoft 服务管理团队完成预配后，默认情况下，将通过两次备份计划配置服务器来备份整个操作系统。 可以使用以下命令查看备份作业的计划：
```
#crontab –l
```
可以使用以下命令随时更改备份计划：
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>如何执行手动备份？

已使用 cron 作业计划操作系统备份。 但也可以手动执行操作系统备份。 要执行手动备份，请运行以下命令：

```
#rear -v mkbackup
```
以下屏幕演示了示例手动备份：

![如何操作](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>如何还原备份？

可以从备份中还原整个备份或单个文件。 若要还原，请使用以下命令：

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
还原后，文件会在当前工作目录中恢复。

以下命令演示从备份文件 backup.tar.gz 还原文件 /etc/fstab
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>从备份中还原文件后，需要将文件复制到所需位置。

以下屏幕截图演示还原完整备份：

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>如何安装 ReaR 工具并更改配置？ 

Relax-and-Recover (ReaR) 包预安装在 HANA 大型实例的类型 II SKU 中，你不需要执行任何操作。 可以直接开始使用 ReaR 进行操作系统备份。
但如果需要自行安装此包，则可以按照列出的步骤安装并配置 ReaR 工具。

若要安装 ReaR 备份包，请使用以下命令：

对于 SLES 操作系统，请使用以下命令：
```
#zypper install <rear rpm package>
```
对于 RHEL 操作系统，请使用以下命令： 
```
#yum install rear -y
```
若要配置 ReaR 工具，需要在 /etc/rear/local.conf 文件中更新 OUTPUT_URL 和 BACKUP_URL。
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

以下屏幕截图演示还原完整备份：![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
