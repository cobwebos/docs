---
title: 恢复服务保管库常见问题 | Microsoft Docs
description: 此版常见问题支持 Azure 备份服务公共预览版。针对备份代理、备份和保留、恢复、安全性的常见问题以及针对 Azure 备份解决方案的其他常见问题的答案。
services: backup
documentationcenter: ''
author: markgalioto
manager: jwhit
editor: ''
keywords: 备份解决方案; 备份服务

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/21/2016
ms.author: trinadhk; markgal; jimpark;

---
# 恢复服务保管库 - 常见问题
> [!div class="op_single_selector"]
> * [经典模式备份常见问题](backup-azure-backup-faq.md)
> * [资源管理器模式备份常见问题](backup-azure-backup-ibiza-faq.md)
> 
> 

本文提供特定于恢复服务保管库的信息，并对 [Azure 备份常见问题](backup-azure-backup-faq.md)进行了补充。Azure 备份常见问题提供整套有关 Azure 备份服务的问答。

你可以在本文或相关章的 Disqus 部分中提出有关 Azure 备份的问题。你也可以在[论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中发布有关 Azure 备份服务的问题。

## 恢复服务保管库是基于资源管理器吗，是仍受支持的备份保管库（经典模式）吗？<br/>
是，仍然支持备份保管库。可在[经典门户](https://manage.windowsazure.com)中创建备份保管库，在 [Azure 门户](https://portal.azure.com)中创建恢复服务保管库。但是我们强烈建议你创建恢复服务保管库，因为所有未来的增强功能将仅在恢复服务保管库中可用。

## 是否可以将备份保管库迁移到恢复服务保管库？<br/>
很遗憾不可以，目前无法将备份保管库的内容迁移到恢复服务保管库。我们正着手添加此功能，但公共预览中并未提供。

## 恢复服务保管库是支持基于经典 VM 还是支持基于资源管理器的 VM？<br/>
恢复服务保管库同时支持这两种模式。可以将经典门户中创建的 VM（即经典模式 VM）或 Azure 门户中创建的 VM（即基于资源管理器的 VM）备份到恢复服务保管库。

## 我已在备份保管库中备份经典 VM。现在，我想将我的 VM 从经典模式迁移到资源管理器模式。如何在恢复服务保管库中备份它们？
如果在备份保管库中备份经典 VM，将 VM 从经典迁移到资源管理器模式时不会自动迁移到恢复服务。请按照以下步骤迁移 VM 备份：

1. 在备份保管库中，请转到“**受保护的项**”选项卡并选择 VM。单击“[停止保护](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines)”。将“*删除关联的备份数据*”选项保留为**取消选中**状态。
2. 将虚拟机从经典模式迁移到资源管理器模式。确保与虚拟机对应的存储和网络也已迁移到资源管理器模式。
3. 创建恢复服务保管库，使用保管库仪表板顶部的“**备份**”操作在迁移的虚拟机上配置备份。了解有关如何[在恢复服务保管库中启用备份](backup-azure-vms-first-look-arm.md)的详细信息

<!---HONumber=AcomDC_0921_2016-->