---
title: "使用 Azure Site Recovery 将物理服务器复制到 Azure 的先决条件 | Microsoft Docs"
description: "总结了使用 Azure Site Recovery 服务将物理 Windows/Linux 服务器上运行的工作负载复制到 Azure 的先决条件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 5f5cb4b9b6fcee6b56ccdcb6223afddd9de90fec
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="step-2-review-the-prerequisites-for-physical-server-to-azure-replication"></a>步骤 2：复查物理服务器到 Azure 复制的先决条件

查看下表中汇总的先决条件。


**先决条件** | **详细信息**
--- | ---
**Azure** | 了解 [Azure 要求](site-recovery-prereq.md#azure-requirements)
**本地配置服务器** | 需要运行 Windows Server 2012 R2 或更高版本的物理服务器（或 VMware VM）。 将在 Site Recovery 部署过程中设置此服务器。<br/><br/> 默认情况下，进程服务器和主目标服务器也安装在此计算机上。 进行扩展时，可能需要单独的进程服务器。 如果确实需要单独的进程服务器，那么它与配置服务器具有相同的要求。<br/><br/> [了解详细信息](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)。
**本地 VM** | 想要复制的计算机应正在运行[受支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，并且符合 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**URLs** | 配置服务器需要以下 URL 的访问权限：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/></br> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/></br> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。<br/><br/> 允许以下用于下载 MySQL 的 URL：http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi。
**移动服务** | 已安装在每个复制的服务器上。




## <a name="limitations"></a>限制

在部署前，请注意此表中汇总的限制。

**限制** | **详细信息**
--- | ---
**Azure** | 存储帐户和网络帐户必须与保管库位于同一区域<br/><br/> 如果使用高级存储帐户，还需要一个用于存储复制日志的标准存储帐户<br/><br/> 不能复制到位于印度中部和印度南部的高级帐户。
**本地配置服务器** | 如果将 VMware VM 用作配置服务器计算机，则 VMware VM 适配器类型应为 VMXNET3。 如果它不是，请[安装此更新](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> 对于 VMware VM，应已安装 vSphere PowerCLI 6.0。<br/><br> 该计算机不应是域控制器。<br/><br/> 该计算机应拥有一个静态 IP 地址。<br/><br/> 主机名长度应不超过 15 个字符，并且操作系统应采用英文。
**复制计算机** | 验证 [Azure VM 限制](site-recovery-prereq.md#azure-requirements)<br/><br/> 不能复制包含加密磁盘的 VM 或带有 UEFI/EFI 引导系统的 VM。<br/><br> 共享的磁盘群集不受支持。 如果源 VM 具有 NIC 组合，那么在故障转移后，它将转换为单个 NIC。<br/><br/> 如果 VM 有一个 iSCSI 磁盘，那么在故障转移后，Site Recovery 会将其转换为 VHD 文件。 如果 Azure VM 可以访问 iSCSI 目标，它将连接到该目标，并且会发现该目标和 VHD 这两者。 如果发生这种情况，请断开与 iSCSI 目标的连接。<br/><br/> 如果想要启用多 VM 一致性，从而使运行相同工作负荷的 VM 一起恢复到一个一致数据点，请打开 VM 上的端口 20004。<br/><br/> 必须在 C 驱动器上安装 Windows。 OS 磁盘应是基本磁盘而不是动态磁盘。 数据磁盘可以是动态磁盘。<br/><br/> VM 上的 Linux /etc/hosts 文件应包含将本地主机名映射到与所有网络适配器相关联的 IP 地址的条目。 主机名、装载点、设备名称、系统路径和文件名（/etc/；/usr）只能采用英文形式。<br/><br/> 特定类型的 [Linux 存储](site-recovery-support-matrix-to-azure.md#support-for-storage)受支持。<br/><br/>在 VM 设置中创建或设置 **disk.enableUUID=true**。 这会向 VMDK 提供一致的 UUID，以便它正确进行装载，并确保在故障回复期间仅将增量更改传输回本地而不执行完全复制。


## <a name="next-steps"></a>后续步骤

- 如果执行的是完整部署，请转到[步骤 3：规划容量](physical-walkthrough-capacity.md)
- 如果执行的是简单的测试部署，请转到[步骤 4：规划网络](physical-walkthrough-network.md)。
