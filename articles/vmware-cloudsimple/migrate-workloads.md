---
title: Azure VMware 解决方案 (通过 CloudSimple)-将工作负荷 Vm 迁移到私有云
description: 介绍如何将虚拟机从本地 vCenter 迁移到 CloudSimple 私有云 vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 99b19639dee17f332d66ce4c737cdd8a3845b77b
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880366"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>将工作负荷 Vm 从本地 vCenter 迁移到私有云 vCenter 环境

若要将 Vm 从本地数据中心迁移到 CloudSimple 私有云, 可以使用多个选项。  私有云提供对 VMware vCenter 的本机访问, VMware 支持的工具可用于工作负荷迁移。 本文介绍了一些 vCenter 迁移选项。

## <a name="prerequisites"></a>先决条件

从本地数据中心迁移 Vm 和数据需要从数据中心到专用云环境的网络连接。  使用以下任一方法建立网络连接:

* 本地环境和私有云之间的[站点到站点 VPN 连接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* ExpressRoute Global Reach 本地 ExpressRoute 线路与 CloudSimple ExpressRoute 线路之间的连接。

从本地 vCenter 环境到私有云的网络路径必须可用于使用 vMotion 迁移 Vm。  本地 vCenter 上的 vMotion 网络必须具有路由功能。  验证防火墙是否允许本地 vCenter 和私有云 vCenter 之间的所有 vMotion 流量。 (在私有云上, 默认情况下会配置 vMotion 网络上的路由。)

## <a name="migrate-isos-and-templates"></a>迁移 Iso 和模板

若要在私有云上创建新的虚拟机, 请使用 Iso 和 VM 模板。  若要将 Iso 和模板上传到私有云 vCenter 并使其可用, 请使用以下方法。

1. 从 vCenter UI 将 ISO 上传到私有云 vCenter。
2. 在私有云 vCenter 上[发布内容库](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html):

    1. 发布本地内容库。
    2. 在私有云 vCenter 上创建新的内容库。
    3. 订阅已发布的本地内容库。
    4. 同步内容库以访问订阅的内容。

## <a name="migrate-vms-using-powercli"></a>使用 PowerCLI 迁移 Vm

若要将 Vm 从本地 vCenter 迁移到私有云 vCenter, 请使用 vmware PowerCLI 或 VMware 实验室提供的跨 vCenter 工作负荷迁移实用程序。  下面的示例脚本显示 PowerCLI 迁移命令。

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> 要使用目标 vCenter 服务器和 ESXi 主机的名称, 请配置从本地到私有云的 DNS 转发。

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>使用 NSX 第2层 VPN 迁移 Vm

利用此选项, 可以将工作负荷从本地 VMware 环境实时迁移到 Azure 中的私有云。  使用此扩展第2层网络, 私有云中将提供本地子网。  迁移后, 不需要为 Vm 分配新的 IP 地址。

[使用第2层延伸网络迁移工作负载](migration-layer-2-vpn.md)介绍了如何使用第2层 VPN 将第2层网络从本地环境延伸到私有云。

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>使用备份和灾难恢复工具迁移 Vm

可以使用备份/还原工具和灾难恢复工具将 Vm 迁移到私有云。  使用私有云作为从使用第三方工具创建的备份进行还原的目标。  私有云还可用作使用 VMware SRM 或第三方工具进行灾难恢复的目标。

有关使用这些工具的详细信息, 请参阅以下主题:

* [使用 Veeam B & R 在 CloudSimple 私有云上备份工作负荷虚拟机](backup-workloads-veeam.md)
* [将 CloudSimple 私有云设置为本地 VMware 工作负荷的灾难恢复站点](disaster-recovery-zerto.md)
