---
title: "Linux VM 设备名称在 Azure 中发生更改 | Microsoft Docs"
description: "解释设备名称为何发生更改，并提供此问题的解决方法。"
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
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>故障排除：Linux VM 设备名称发生更改

本文解释重启 Linux 虚拟机 (VM) 或重新附加磁盘后，设备名称为何发生更改。 此外提供此问题的解决方法。

## <a name="symptom"></a>症状

在 Microsoft Azure 中运行 Linux VM 时，可能会遇到以下问题。

- 重启后 VM 无法引导。

- 如果分离再重新附加数据磁盘，磁盘的设备名称会发生更改。

- 使用设备名称引用磁盘的应用程序或脚本会失败。 磁盘的设备名称会发生更改。

## <a name="cause"></a>原因

不保证 Linux 中的设备路径在重启后保持一致。 设备名称由主要编号（字母）和次要编号组成。  当 Linux 存储设备驱动程序检测到新设备时，会将可用范围内的主要和次要设备编号分配给新设备。 移除某个设备后，其设备编号将被释放，供将来重复使用。

之所以发生该问题，是因为 SCSI 子系统在 Linux 中计划的设备扫描以异步方式进行。 每次重启后，最终的设备路径命名可能会发生变化。 

## <a name="solution"></a>解决方案

若要解决此问题，请使用持久命名。 有四个方法可实现持久命名 - 按文件系统标签、按 UUID、按 ID 以及按路径。 我们建议对 Azure Linux VM 使用文件系统标签和 UUID 方法。 

大多数分发版还提供 **nofail** 或 **nobootwait** fstab 选项。 即使在启动时无法装载磁盘，这些选项也能使系统引导。 有关这些参数的详细信息，请查看分发版的文档。 有关在添加数据磁盘时如何将 Linux VM 配置为使用 UUID 的详细信息，请参阅[连接到 Linux VM 以装载新磁盘](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)。 

在 VM 上安装 Azure Linux 代理后，该代理会使用 Udev 规则在 **/dev/disk/azure** 下构造一组符号链接。 应用程序和脚本可以使用这些 Udev 规则来确定磁盘已附加到 VM，以及磁盘的类型和 LUN。

## <a name="more-information"></a>详细信息

### <a name="identify-disk-luns"></a>识别磁盘 LUN

应用程序可以使用 LUN 来查找所有附加的磁盘和构造性的符号链接。 Azure Linux 代理现在包含一些 udev 规则，这些规则可以设置从 LUN 到设备的符号链接，如下所示：

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
                                 

也可以使用 lsscsi 或类似的工具从 Linux 来宾检索 LUN 信息，如下所示。

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

可将此来宾 LUN 信息与 Azure 订阅元数据结合使用，识别 Azure 存储中用于存储分区数据的 VHD 的位置。 例如，使用 az cli：

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

脚本或应用程序可以读取 blkid 的输出或类似的信息源，并在 **/dev** 中构造可供使用的符号链接。 输出会显示已附加到 VM 的所有磁盘的 UUID 及其关联到的设备文件：

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

waagent udev 规则在 **/dev/disk/azure** 下构造一组符号链接：


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


应用程序可以使用此信息来识别引导磁盘设备和资源（临时）磁盘。 在 Azure 中，应用程序应该引用 **/dev/disk/azure/root-part1** 或 **/dev/disk/azure-resource-part1** 来发现这些分区。

如果 blkid 列表中有其他分区，这些分区将驻留在数据磁盘上。 应用程序可以维护这些分区的 UUID，并在运行时使用如下所示的路径来发现设备名称：

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>获取最新的 Azure 存储规则

若要获取最新的 Azure 存储规则，请运行以下命令：

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


有关详细信息，请参阅以下文章：

- [Ubuntu：使用 UUID](https://help.ubuntu.com/community/UsingUUID)

- [Red Hat：持久命名](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)

- [Linux：UUID 有什么作用](https://www.linux.com/news/what-uuids-can-do-you)

- [Udev：新式 Linux 系统中的设备管理简介](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)


