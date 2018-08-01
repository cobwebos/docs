---
title: 从 Linux VM 中分离数据磁盘 - Azure | Microsoft Docs
description: 了解如何使用 CLI 2.0 或 Azure 门户从 Azure 虚拟机中分离数据磁盘。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 07/18/2018
ms.author: cynthn
ms.openlocfilehash: 0225c6605109489c4b9b599918dc09983ae25ac8
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144068"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>如何从 Linux 虚拟机中分离数据磁盘

不再需要附加到虚拟机的数据磁盘时，可以轻松地分离它。 这会从虚拟机中删除磁盘，但不会从存储中删除它。 在本文中，我们使用的是 Ubuntu LTS 16.04 分发版。 如果使用的是其他分发版，则卸载磁盘的说明可能会有所不同。

> [!WARNING]
> 如果分离磁盘，它将不会自动删除。 如果订阅了高级存储，则将继续承担该磁盘的存储费用。 有关详细信息，请参阅[使用高级存储时的定价和计费方式](../windows/premium-storage.md#pricing-and-billing)。 
> 
> 

如果希望再次使用磁盘上的现有数据，可以将其重新附加到相同的虚拟机或另一个虚拟机。  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>连接到 VM 以卸载磁盘

在使用 CLI 或门户分离磁盘之前，需要卸载磁盘并从 fstab 文件中删除对其的引用。

连接到 VM。 在本示例中，VM 的公共 IP 地址为 *10.0.1.4*，用户名为 *azureuser*： 

```bash
ssh azureuser@10.0.1.4
```

首先，找到要分离的数据磁盘。 以下示例使用 dmesg 来筛选 SCSI 磁盘：

```bash
dmesg | grep SCSI
```

输出类似于以下示例：

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

此处，*sdc* 是我们要分离的磁盘。 还应该获取磁盘的 UUID。

```bash
sudo -i blkid
```

输出与以下示例类似：

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


编辑 */etc/fstab* 文件以删除对该磁盘的引用。 

> [!NOTE]
> 错误地编辑 **/etc/fstab** 文件可能会导致系统无法引导。 如果没有把握，请参考分发的文档来获取有关如何正确编辑该文件的信息。 另外，建议在编辑之前创建 /etc/fstab 文件的备份。

在文本编辑器中打开 */etc/fstab* 文件，如下所示：

```bash
sudo vi /etc/fstab
```

在此示例中，需要从 */etc/fstab* 文件中删除以下行：

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

使用 `umount` 卸载磁盘。 以下示例从 */datadrive* 装入点卸载 */dev/sdc1* 分区：

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-cli-20"></a>使用 CLI 2.0 分离数据磁盘

此示例将 *myDataDisk* 磁盘与 *myResourceGroup* 中名为 *myVM* 的 VM 分离。

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

磁盘保留在存储中，但不再附加到虚拟机。


## <a name="detach-a-data-disk-using-the-portal"></a>使用门户分离数据磁盘

1. 在左侧菜单中，选择“虚拟机”。
2. 选择具有要分离的数据磁盘的虚拟机，并单击“停止”以解除分配 VM。
3. 在虚拟机窗格中，选择“磁盘”。
4. 在“磁盘”窗格的顶部，选择“编辑”。
5. 在“磁盘”窗格中，转到要分离的数据磁盘最右侧，并单击![分离按钮图像](./media/detach-disk/detach.png)分离按钮。
5. 删除磁盘后，单击窗格顶部的“保存”。
6. 在虚拟机窗格中，单击“概述”，并单击窗格顶部的“开始”按钮重启 VM。

磁盘保留在存储中，但不再附加到虚拟机。



## <a name="next-steps"></a>后续步骤
要重新使用数据磁盘，只需[将其附加到其他 VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 即可。

