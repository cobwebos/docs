---
title: Azure VMware 解决方案 (按 CloudSimple 快速入门-在 Azure 上使用 VMware Vm)
description: 了解如何通过 CloudSimple 使用 Azure VMware 解决方案配置和使用 VMware Vm Azure 门户
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 504550358bb56602093e58c90506c9140afccadb
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574486"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>快速入门-在 Azure 上使用 VMware Vm

若要在 Azure 门户中创建虚拟机, 请使用 CloudSimple 管理员为你的订阅启用的虚拟机模板。 可在 VMware 基础结构中找到 VM 模板。

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>在 Azure 上创建 CloudSimple VM 需要 VM 模板

从 vCenter UI 创建私有云上的虚拟机。 若要创建模板, 请按照将[虚拟机克隆到 VSphere Web 客户端](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)中的模板中的说明进行操作。 在私有云 vCenter 上存储 VM 模板。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>在 Azure 门户中创建虚拟机

1. 选择“所有服务”。

2. 搜索“CloudSimple 虚拟机”。

3. 单击“添加”。

    ![创建 CloudSimple 虚拟机](media/create-cloudsimple-virtual-machine.png)

4. 输入基本信息, 并单击 "**下一步: 大小**"。

    ![创建 CloudSimple 虚拟机-基础](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 字段 | 描述 |
    | ------------ | ------------- |
    | 订阅 | 与私有云关联的 Azure 订阅。  |
    | 资源组 | VM 将分配到的资源组。 可以选择现有的组，或创建新组。 |
    | 姓名 | 用于标识 VM 的名称。  |
    | Location | 托管此 VM 的 Azure 区域。  |
    | 私有云 | CloudSimple 要在其中创建虚拟机的私有云。 |
    | 资源池 | VM 的映射资源池。 请从可用的资源池中进行选择。 |
    | vSphere 模板 | VM 的 vSphere 模板。  |
    | 用户名 | VM 管理员 (适用于 Windows 模板) 的用户名。|
    | 密码 |  VM 管理员的密码 (适用于 Windows 模板)。 |
    | 确认密码 | 确认该密码。 |

5. 选择 VM 的核心数和内存容量, 并单击 "**下一步: 配置**"。 如果要向来宾操作系统公开完全 CPU 虚拟化, 请选中相应的复选框。 需要硬件虚拟化的应用程序可在虚拟机上运行，而无需进行二进制转换或半虚拟化。 有关详细信息，请参阅 VMware 文章<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">公开 VMware 硬件辅助虚拟化</a>。

    ![创建 CloudSimple 虚拟机-大小](media/create-cloudsimple-virtual-machine-size.png)

6. 按照下表中所述配置网络接口和磁盘, 然后单击 "**查看" + "创建**"。

    ![创建 CloudSimple 虚拟机-配置](media/create-cloudsimple-virtual-machine-configurations.png)

    对于 "网络接口", 单击 "**添加网络接口**" 并配置以下设置。

    | 控件 | 描述 |
    | ------------ | ------------- |
    | 姓名 | 输入一个名称用于标识接口。  |
    | 网络 | 从私有云 vSphere 中已配置的分布式端口组列表中进行选择。  |
    | 适配器 | 从为 VM 配置的可用类型列表中选择 vSphere 适配器。 有关详细信息, 请参阅 VMware 知识库文章<a href="https://kb.vmware.com/s/article/1001805" target="_blank">为虚拟机选择网络适配器</a>。 |
    | 启动时开机 | 选择是否要在启动 VM 时启用 NIC 硬件。 默认设置为“启用”。 |

    对于磁盘, 单击 "**添加磁盘**", 然后配置下列设置。

    | 项 | 描述 |
    | ------------ | ------------- |
    | 姓名 | 输入一个名称用于标识磁盘。  |
    | Size | 选择一种可用大小。  |
    | SCSI 控制器 | 选择磁盘的 SCSI 控制器。  |
    | 模式 | 确定磁盘如何参与快照。 选择以下选项之一： <br> 独立持久性:写入磁盘的所有数据都将被永久写入。<br> 独立非永久性:关闭或重置虚拟机时，会丢弃已写入到磁盘的更改。  使用独立非永久性模式可始终以相同的状态重启 VM。 有关详细信息，请参阅 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 文档</a>。

7. 验证完成后, 查看设置, 然后单击 "**创建**"。 若要进行任何更改, 请单击顶部的选项卡或单击。

    ![创建 CloudSimple 虚拟机-审阅](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>后续步骤

* [查看 CloudSimple 虚拟机列表](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [从 Azure 管理 CloudSimple 虚拟机](azure-manage-vm.md)
