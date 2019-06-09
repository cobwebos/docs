---
title: CloudSimple 提供的 Azure VMware 解决方案快速入门 - 使用 Azure 上的 VMware VM
description: 本快速入门介绍如何在 Azure 门户中通过 CloudSimple 提供的 Azure VMware 解决方案来配置和使用 VMware VM
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393492"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>快速入门：使用 Azure 上的 VMware VM

若要在 Azure 门户中创建虚拟机，可以使用私有云 vCenter 中提供的虚拟机模板。 VCenter 管理员可以在私有云中创建其他模板。

## <a name="create-a-vm-template"></a>创建 VM 模板

首先，使用 vCenter UI 在私有云中创建一个虚拟机。 若要创建模板，请遵照 VMware 文章[在 vSphere Web 客户端中将虚拟机克隆到模板](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)中的说明。 将 VM 模板存储在私有云 vCenter 中。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>在 Azure 门户中创建虚拟机

1. 选择“所有服务”  。

2. 搜索“CloudSimple 虚拟机”。 

3. 选择 **添加** 。

    ![选择“添加”](media/create-cloudsimple-virtual-machine.png)

4. 输入有关虚拟机的以下信息，然后选择“下一步:  大小”。

    ![创建 CloudSimple 虚拟机 - 基本信息](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 字段 | 说明 |
    | ------------ | ------------- |
    | **订阅** | 与私有云关联的 Azure 订阅。  |
    | **资源组** | VM 要分配到的资源组。 可以选择现有的组，或创建新组。 |
    | **名称** | 用于标识 VM 的名称。  |
    | **位置** | 托管该 VM 的 Azure 区域。  |
    | **私有云** | 要在其中创建 VM 的 CloudSimple 私有云。 |
    | **资源池** | VM 的映射资源池。 请从可用的资源池中进行选择。 |
    | **vSphere 模板** | VM 的 vSphere 模板。  |
    | **用户名** | VM 管理员的用户名（适用于 Windows 模板）。|
    | **密码** |  VM 管理员的密码（适用于 Windows 模板）。 |
    | **确认密码** | 在上一字段中提供的密码。 |

5. 选择 VM 的核心数和内存容量。 若要向来宾操作系统公开整个 CPU 虚拟化，请选择“向来宾 OS 公开”。  需要硬件虚拟化的应用程序可在虚拟机上运行，而无需进行二进制转换或半虚拟化。 有关详细信息，请参阅 VMware 文章<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">公开 VMware 硬件辅助虚拟化</a>。 完成后，选择“下一步:  配置”。

    ![创建 CloudSimple 虚拟机 - 大小](media/create-cloudsimple-virtual-machine-size.png)

6. 根据下表中所述配置网络接口和磁盘，然后选择“查看 + 创建”。 

    ![创建 CloudSimple 虚拟机 - 配置](media/create-cloudsimple-virtual-machine-configurations.png)

    对于网络接口，请选择“添加网络接口”，然后配置以下设置： 
    
    | 设置 | 说明 |
    | ------------ | ------------- |
    | **名称** | 输入一个名称用于标识接口。  |
    | **网络** | 从私有云 vSphere 中已配置的分布式端口组列表中进行选择。  |
    | **适配器** | 从为 VM 配置的可用类型列表中选择一个 vSphere 适配器。 有关详细信息，请参阅 VMware 文章<a href="https://kb.vmware.com/s/article/1001805" target="_blank">选择虚拟机的网络适配器</a>。 |
    | **启动时开机** | 选择是否要在启动 VM 时启用 NIC 硬件。 默认设置为“启用”。  |

    对于磁盘，请选择“添加磁盘”，然后配置以下设置： 

    | 设置 | 说明 |
    | ------------ | ------------- |
    | **名称** | 输入一个名称用于标识磁盘。  |
    | **大小** | 选择一种可用大小。  |
    | **SCSI 控制器** | 选择磁盘的 SCSI 控制器。  |
    | **模式** | 模式指定磁盘如何参与快照。 选择以下选项之一： <br> **独立永久性**：已写入到磁盘的所有更改将永久性写入。<br> **独立非永久性**：关闭或重置虚拟机时，会丢弃已写入到磁盘的更改。 使用独立非永久性模式可始终以相同的状态重启 VM。 有关详细信息，请参阅 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 文档</a>。

7. 验证完成后，检查设置，然后选择“创建”。  若要进行更改，请选择顶部的选项卡，或选择“上一步:  配置”。

    ![创建 CloudSimple 虚拟机 - 查看 + 创建](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>后续步骤

* [查看 CloudSimple 虚拟机列表](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [从 Azure 管理 CloudSimple 虚拟机](https://docs.azure.cloudsimple.com/azure-manage-vm/)
