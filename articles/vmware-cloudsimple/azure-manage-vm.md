---
title: Azure VMware 解决方案（按云简单 ） - 在 Azure 中管理私有云 VM
description: 介绍如何在 Azure 门户中管理云简单私有云 VM，包括添加磁盘、更改 VM 容量和添加网络接口
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014991"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>在 Azure 中管理云简单私有云虚拟机

要管理[为云简单私有云创建的虚拟机](azure-create-vm.md)，请登录到[Azure 门户](https://portal.azure.com)。 搜索并选择虚拟（在侧菜单上**的所有服务**或**虚拟机**下搜索）。

## <a name="control-virtual-machine-operation"></a>控制虚拟机操作

以下控件可从所选虚拟机的 **"概述"** 页获得。

| 控制 | 描述 |
| ------------ | ------------- |
| 连接 | 连接到指定的 VM。  |
| 开始 | 启动指定的 VM。  |
| 重新启动 | 关闭，然后打开指定的 VM 电源。  |
| 停止 | 关闭特定 VM。  |
| 捕获 | 捕获指定 VM 的映像，以便将其用作创建其他 VM 的图像。 请参阅[在 Azure 中创建通用 VM 的托管映像](../virtual-machines/windows/classic/capture-image.md)。   |
| 移动 | 移动到指定的 VM。  |
| 删除 | 删除指定的 VM。  |
| 刷新 | 刷新显示屏中的数据。  |

### <a name="view-performance-information"></a>查看性能信息

**"概述"** 页下部的图表显示所选间隔的性能数据（最后一小时到持续 30 天;默认值为最后一小时）。 在每个图表中，您可以通过在图表上来回移动光标来显示间隔内任意时间的数值。

将显示以下图表。

| Item | 描述 |
| ------------ | ------------- |
| CPU（平均） | 所选间隔内的平均 CPU 利用率（百分比）。   |
| 网络 | 在所选间隔内进出网络 （MB） 的流量。  |
| 磁盘字节 | 在所选间隔内从磁盘读取并写入磁盘 （MB） 的总数据。  |
| 磁盘操作 | 所选间隔内磁盘操作的平均速率（操作/秒）。 |

## <a name="manage-vm-disks"></a>管理 VM 磁盘

要添加 VM 磁盘，打开所选 VM 的**磁盘**页面。 要添加磁盘，请单击"**添加磁盘**"。 通过输入或选择内联选项配置以下每个设置。 单击“保存”。****

   | Item | 描述 |
   | ------------ | ------------- |
   | “属性” | 输入一个名称用于标识磁盘。  |
   | 大小 | 选择一种可用大小。  |
   | SCSI 控制器 | 选择 SCSI 控制器。 可用的控制器因受支持的不同操作系统而异。  |
   | “模式” | 确定磁盘参与快照的方式。 选择以下选项之一： <br> - 独立持久性：写入磁盘的所有数据都是永久写入的。<br> - 独立、非持久性：当您关闭或重置虚拟机时，将丢弃写入磁盘的更改。  此模式允许您始终以相同的状态重新启动 VM。 有关详细信息，请参阅 [VMware 文档](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。 |

要删除磁盘，请选择它，然后单击"**删除**"。

## <a name="change-the-capacity-of-the-vm"></a>更改 VM 的容量

要更改 VM 的容量，打开所选 VM**的大小页。** 指定以下任一项，然后单击"**保存**"。

| Item | 描述 |
| ------------ | ------------- |
| 内核数 | 分配给 VM 的内核数。  |
| 硬件虚拟化 | 选择该复选框以向来宾操作系统公开硬件虚拟化。 请参阅 VMware 文章["公开 VMware 硬件辅助虚拟化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)"。 |
| 内存大小 | 选择要分配给 VM 的内存量。  

## <a name="manage-network-interfaces"></a>管理网络接口

要添加接口，请单击"**添加网络接口**"。 通过输入或选择内联选项配置以下每个设置。 单击“保存”。****

   | 控制 | 描述 |
   | ------------ | ------------- |
   | “属性” | 输入一个名称用于标识接口。  |
   | 网络 | 从私有云 vSphere 中配置的网络列表中选择。  |
   | 适配器 | 从为 VM 配置的可用类型列表中选择 vSphere 适配器。 有关详细信息，请参阅 VMware 知识库文章[为虚拟机选择网络适配器](https://kb.vmware.com/s/article/1001805)。 |
   | 启动时开机 | 选择是否要在启动 VM 时启用 NIC 硬件。 默认设置为“启用”。**** |

要删除网络接口，请选择它并单击"**删除**"。
