---
title: Azure 的 VMware 解决方案 CloudSimple 快速入门-使用 Azure 上的 VMware Vm
description: 了解如何配置和使用 VMware Vm 从 Azure 门户使用 Azure CloudSimple VMware 解决方案
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577715"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>快速入门-使用 Azure 上的 VMware Vm

若要在 Azure 门户中创建虚拟机，请为你的订阅使用 CloudSimple 管理员已启用的虚拟机模板。 这些虚拟机模板位于 VMware 基础结构上。

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>在 Azure 上的 CloudSimple VM 创建所需的 VM 模板

从 vCenter UI 在私有云上创建虚拟机。 若要创建模板，请按照中的说明[克隆到 vSphere Web 客户端中的模板的虚拟机](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)。 在私有云 vCenter 上存储的 VM 模板。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>在 Azure 门户中创建虚拟机

1. 在左侧菜单中，单击**+** 或**创建资源**。

2. 在左侧菜单中，单击**计算**，然后单击**CloudSimple 虚拟机**。

3. 单击**确认**以验证你想要创建新的 VM。

4. 下表中所述设置基本配置，然后单击**下一步：大小**。

    | 字段 | 描述 |
    | ------------ | ------------- |
    | 订阅 | 与私有云部署关联的 azure 订阅。  |
    | 资源组 | 要向其分配 VM 的部署组。 可以选择现有组或新建一个。 |
    | 名称 | 若要确定 VM 的名称。  |
    | Location | 在其中承载此 VM 的 azure 区域。  |
    | 资源池 | VM 的的物理资源。 选择从可用的资源池。 |
    | vSphere 模板 | 操作系统的 vm 模板的类型。  |
    | 用户名 | VM 管理员用户名。 |
    | 密码确认密码 | VM 管理员密码。  |

5. 选择内核和内存容量的 vm 的数。

6. （可选）如果你想要公开与来宾操作系统的完整 CPU 虚拟化，则选择**向来宾操作系统公开**复选框。
选择此选项可让应用程序需要硬件虚拟化，而无需二进制转换或半虚拟化的虚拟机上运行。 有关详细信息，请参阅 VMware 文章[公开 VMware 硬件辅助虚拟化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)。

7. 单击“下一步:**配置**。

8. 配置网络接口和下表中所述的磁盘。

    对于网络接口，请单击**添加网络接口**和配置下列设置。

    | 控制 | 描述 |
    | ------------ | ------------- |
    | 名称 | 输入用于标识接口的名称。  |
    | 网络 | 从私有云 vSphere 中配置的网络列表中选择。  |
    | 适配器 | 从为 VM 配置的可用类型列表中选择 vSphere 适配器。 有关详细信息，请参阅 VMware 知识库文章[选择用于你的虚拟机的网络适配器](https://kb.vmware.com/s/article/1001805)。 |
    | 在启动开机 | 选择是否要在启动 VM 时，启用 NIC 硬件。 默认值是**启用**。 |

    对于磁盘，请单击**添加磁盘**和配置下列设置。

    | Item | 描述 |
    | ------------ | ------------- |
    | 名称 | 输入用于标识磁盘的名称。  |
    | 大小 | 选择其中一个可用的大小。  |
    | SCSI 控制器 | 选择一个 SCSI 控制器。 不同的受支持操作系统的不同可用控制器。  |
    | Mode | 确定如何在快照中加入该磁盘。 选择以下选项之一： <br> 的永久独立于：向磁盘写入的所有数据将永久都写入。<br> 的非持久性独立于：当你关闭或重置虚拟机，向磁盘写入的更改将被丢弃。  独立的非持久性模式，可始终处于相同的状态重启 VM。 有关详细信息，请参阅[VMware 文档。](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. 查看设置。 若要进行任何更改，请单击顶部的选项卡。

10. 单击**创建**以保存设置并创建 VM。

## <a name="next-steps"></a>后续步骤

* [查看 CloudSimple 虚拟机的列表](https://docs.azure.cloudsimple.com/azurelistvms/)
* [从 Azure 管理 CloudSimple 虚拟机](https://docs.azure.cloudsimple.com/azureoverviewpage/)