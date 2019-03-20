---
title: Azure 站点恢复的备份的互操作性 |Microsoft Docs
description: 概述如何 Azure Site Recovery 和 Azure 备份可以一起使用。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731863"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>有关 Site Recovery 和备份互操作性

本文提供了有关成功使用 Azure IaaS VM 备份和 Azure VM 灾难恢复的指南。

## <a name="azure-backup"></a>Azure 备份

Azure 备份有助于保护数据的本地服务器、 虚拟机、 虚拟化工作负荷、 SQL server、 SharePoint 服务器和的详细信息。 Azure Site Recovery 可以协调和管理 Azure Vm、 本地 Vm 和物理服务器灾难恢复。

## <a name="azure-site-recovery"></a>Azure Site Recovery

它是可以将 VM 或一组 Vm 上配置 Azure 备份和 Azure Site Recovery。 这两种产品进行互操作。 备份和 Azure Site Recovery 之间的互操作性变得重要的几个方案如下所示：

### <a name="file-backuprestore"></a>文件备份/还原

如果备份和复制是同时启用了，并且执行备份，则还原在源端 VM 或 Vm 的组上的任何文件的任何问题。 复制将像往常一样继续复制运行状况中进行任何更改。

### <a name="disk-backuprestore"></a>磁盘备份/还原

如果从备份中还原磁盘的虚拟机的保护将不得不重新启用它。

### <a name="vm-backuprestore"></a>VM 备份/还原

不支持备份和还原的 VM 或 Vm 的组。 若要使其工作，需要在重新启用保护。

**方案** | **支持 Azure Site recovery？** | **解决方法是，如果有**  
--- | --- | ---
文件/文件夹备份 | 是 | 不适用
磁盘备份 | 当前未 | 禁用和启用保护
VM 备份 | 否 | 禁用和启用保护
