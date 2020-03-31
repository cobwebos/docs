---
title: Azure VMware 解决方案（按云简单 ） - 使用 VM 模板在 Azure 中创建虚拟机
description: 介绍如何使用云简单私有云的 VMware 基础结构上的 VM 模板在 Azure 中创建虚拟机
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244688"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>使用 VMware 基础结构上的 VM 模板在 Azure 中创建虚拟机

您可以使用 CloudSimple 管理员为订阅启用的 VMware 基础结构上的 VM 模板在 Azure 门户中创建虚拟机。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure[门户](https://portal.azure.com)。

## <a name="create-cloudsimple-virtual-machine"></a>创建云简单虚拟机

1. 选择**所有服务**。

2. 搜索“CloudSimple 虚拟机”。****

3. 单击 **“添加”**。

    ![创建云简单虚拟机](media/create-cloudsimple-virtual-machine.png)

4. 输入基本信息，单击 **"下一步：大小**"。

    > [!NOTE]
    > 在 Azure 上创建云简单虚拟机需要 VM 模板。  此 VM 模板应存在于私有云 vCenter 上。  使用 vCenter UI 在私有云上创建虚拟机，并具有所需的操作系统和配置。  使用[将虚拟机克隆到 vSphere Web 客户端中的模板中的](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)说明，创建一个模板。

    ![创建云简单虚拟机 - 基础知识](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 字段 | 描述 |
    | ------------ | ------------- |
    | 订阅 | 与私有云关联的 Azure 订阅。  |
    | 资源组 | 将 VM 分配给的资源组。 可以选择现有的组，或创建新组。 |
    | “属性” | 名称以标识 VM。  |
    | 位置 | 托管此 VM 的 Azure 区域。  |
    | 私有云 | 云简单私有云，您要创建虚拟机。 |
    | 资源池 | 映射 VM 的资源池。 请从可用的资源池中进行选择。 |
    | vSphere 模板 | VM 的 vSphere 模板。  |
    | 用户名 | VM 管理员的用户名（适用于 Windows 模板）|
    | 密码 <br>确认密码 | VM 管理员的密码（适用于 Windows 模板）。  |

5. 选择 VM 的核心和内存容量数，然后单击 **"下一步：配置**"。 如果要向来宾操作系统公开完全 CPU 虚拟化，以便需要硬件虚拟化的应用程序可以在虚拟机上运行，而无需二进制转换或半虚拟化，请选择该复选框。 有关详细信息，请参阅 VMware 文章[公开 VMware 硬件辅助虚拟化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)。

    ![创建云简单虚拟机 - 大小](media/create-cloudsimple-virtual-machine-size.png)

6. 按照下表中所述配置网络接口和磁盘，然后单击 **"审阅 + 创建**"。

    ![创建云简单虚拟机 - 配置](media/create-cloudsimple-virtual-machine-configurations.png)

    对于网络接口，请单击"**添加网络接口**"并配置以下设置。

    | 控制 | 描述 |
    | ------------ | ------------- |
    | “属性” | 输入一个名称用于标识接口。  |
    | 网络 | 从私有云 vSphere 中配置的分布式端口组列表中选择。  |
    | 适配器 | 从为 VM 配置的可用类型列表中选择 vSphere 适配器。 有关详细信息，请参阅 VMware 知识库文章[为虚拟机选择网络适配器](https://kb.vmware.com/s/article/1001805)。 |
    | 启动时开机 | 选择是否要在启动 VM 时启用 NIC 硬件。 默认设置为“启用”。**** |

    对于磁盘，请单击"**添加磁盘"** 并配置以下设置。

    | Item | 描述 |
    | ------------ | ------------- |
    | “属性” | 输入一个名称用于标识磁盘。  |
    | 大小 | 选择一种可用大小。  |
    | SCSI 控制器 | 选择磁盘的 SCSI 控制器。  |
    | “模式” | 确定磁盘参与快照的方式。 选择以下选项之一： <br> - 独立持久性：写入磁盘的所有数据都是永久写入的。<br> - 独立非持久性：当您关闭或重置虚拟机时，将丢弃写入磁盘的更改。  使用独立非永久性模式可始终以相同的状态重启 VM。 有关详细信息，请参阅 [VMware 文档](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。

7. 验证完成后，查看设置并单击"**创建**"。 要进行任何更改，请单击顶部的选项卡或单击。

    ![创建云简单虚拟机 - 查看](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>查看 CloudSimple 虚拟机列表

1. 选择**所有服务**。

2. 搜索“CloudSimple 虚拟机”。****

3. 选择创建私有云的方面。

    ![云简单虚拟机列表](media/list-cloudsimple-virtual-machines.png)

云简单虚拟机列表包括从 Azure 门户创建的虚拟机。  在映射的 vCenter 资源池中的私有云 vCenter 上创建的虚拟机将显示在列表中。  
