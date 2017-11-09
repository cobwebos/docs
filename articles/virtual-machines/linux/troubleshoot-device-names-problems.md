---
title: "排查 Azure 中 Linux VM 设备名更改问题 | Microsoft Docs"
description: "说明 Linux VM 设备名更改的原因以及解决此问题的方法。"
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 9b1c9a283e1439c0db5fa37b6601868a7a830ed3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>排查 Linux VM 设备名更改问题

本文解释重启 Linux VM 或重新附加数据磁盘后，设备名为何发生更改。 此外，本文还提供针对此问题的解决方案。

## <a name="symptoms"></a>症状
在 Microsoft Azure 中运行 Linux VM 时，可能会遇到以下问题：

- 重启后 VM 无法引导。
- 分离再重新附加数据磁盘时，磁盘的设备名发生更改。
- 使用设备名引用磁盘的应用程序或脚本会失败，因为设备名已发生更改。

## <a name="cause"></a>原因

不保证 Linux 中的设备路径在重启后保持一致。 设备名由主要编号（字母）和次要编号组成。 当 Linux 存储设备驱动程序检测到新设备时，驱动程序会将可用范围内的主要和次要设备编号分配给该设备。 移除某个设备后，其设备编号将被释放，供重复使用。

之所以发生该问题，是因为 Linux 中的设备扫描由 SCSI 子系统计划以异步方式进行。 因此，设备路径名称会在重启后发生变化。 

## <a name="solution"></a>解决方案

若要解决此问题，请使用持久命名。 有四个方法可使用持久命名：按文件系统标签、按 UUID、按 ID 或按路径。 我们建议对 Azure Linux VM 使用文件系统标签或 UUID。 

大多数分发都提供 `fstab` nofail 或 nobootwait 参数。 在启动时若无法装载磁盘，这些参数可使系统启动。 有关这些参数的详细信息，请查看分发文档。 有关在添加数据磁盘时如何将 Linux VM 配置为使用 UUID 的信息，请参阅[连接到 Linux VM 以装载新磁盘](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)。 

在 VM 上安装 Azure Linux 代理后，该代理使用 Udev 规则在 /dev/disk/azure 路径下构造一组符号链接。 应用程序和脚本使用 Udev 规则来识别附加到 VM 的磁盘，以及磁盘类型和磁盘 LUN。

### <a name="identify-disk-luns"></a>识别磁盘 LUN

应用程序使用 LUN 来查找所有附加的磁盘，并构造符号链接。 Azure Linux 代理包含一些 Udev 规则，用以设置从 LUN 到设备的符号链接：

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 
Linux 来宾帐户中的 LUN 信息通过使用 `lsscsi` 或类似工具进行检索：

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

来宾 LUN 信息与 Azure 订阅元数据一起用于在包含分区数据的 Azure 存储中查找 VHD。 例如，可以使用 `az` CLI：

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                            
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>使用 blkid 发现文件系统 UUID

应用程序和脚本读取 `blkid` 的输出或类似的信息源，以在 /dev 路径中构造符号链接。 该输出显示所有附加到 VM 的磁盘的 UUID 及与其关联的设备文件：

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Azure Linux 代理 Udev 规则在 /dev/disk/azure 路径下构造一组符号链接：

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

应用程序可使用此链接来识别启动盘设备和资源（临时）磁盘。 在 Azure 中，应用程序应搜索 /dev/disk/azure/root-part1 或 /dev/disk/azure-resource-part1 路径来发现这些分区。

`blkid` 列表中的任何其他分区都驻留在数据磁盘上。 应用程序维护这些分区的 UUID，并在运行时使用路径来发现设备名：

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>获取最新的 Azure 存储规则

若要获取最新的 Azure 存储规则，请运行以下命令：

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>另请参阅

有关详细信息，请参阅以下文章：

- [Ubuntu：使用 UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat：持久命名](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux：UUID 有什么作用](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev：新式 Linux 系统中的设备管理简介](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

