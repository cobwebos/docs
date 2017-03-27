---
title: "恢复服务保管库常见问题解答 | Microsoft Docs"
description: "此版常见问题支持 Azure 备份服务公共预览版。 针对备份代理、备份和保留、恢复、安全性的常见问题以及针对 Azure 备份解决方案的其他常见问题的答案。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "备份解决方案; 备份服务"
ms.assetid: 5f55b500-1ee9-4f64-9306-02d6f7a8eded
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: e12d533ac5befe020c0aad4aa64ca9ed50349c3d
ms.openlocfilehash: 271f447a36f42330ed7b8dea78b32c06eebdeda6
ms.lasthandoff: 02/17/2017


---
# <a name="recovery-services-vault---faq"></a>恢复服务保管库 - 常见问题
本文提供特定于恢复服务保管库的信息，并对 [Azure 备份常见问题解答](backup-azure-backup-faq.md)进行了补充。 Azure 备份常见问题提供整套有关 Azure 备份服务的问答。  

你可以在本文或相关章的 Disqus 部分中提出有关 Azure 备份的问题。 你也可以在 [论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中发布有关 Azure 备份服务的问题。

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>恢复服务保管库基于 Resource Manager。 是否仍支持备份保管库（经典模式）？ <br/>
是，仍然支持备份保管库。 可在[经典门户](https://manage.windowsazure.com)中创建备份保管库。 在 [Azure 门户](https://portal.azure.com)中创建恢复服务保管库。 但是我们强烈建议用户创建恢复服务保管库，因为所有未来的增强功能将仅在恢复服务保管库中可用。

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>是否可以将备份保管库迁移到恢复服务保管库？ <br/>
很遗憾不可以，目前无法将备份保管库的内容迁移到恢复服务保管库。 我们正着手添加此功能，但公共预览中并未提供。

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>恢复服务保管库是支持基于经典 VM 还是支持基于资源管理器的 VM？ <br/>
恢复服务保管库同时支持这两种模式。  可以将经典门户中创建的 VM（即经典模式 VM）或 Azure 门户中创建的 VM（即基于资源管理器的 VM）备份到恢复服务保管库。

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>我已在备份保管库中备份经典 VM。 现在，我想将我的 VM 从经典模式迁移到资源管理器模式。  如何在恢复服务保管库中备份它们？
如果在备份保管库中备份经典 VM，将 VM 从经典迁移到资源管理器模式时不会自动迁移到恢复服务。 请按照以下步骤迁移 VM 备份：

1. 在备份保管库中，请转到“ **受保护的项** ”选项卡并选择 VM。 单击“ [停止保护](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines)”。 将“ *删除关联的备份数据* ”选项保留为 **取消选中**状态。
2. 将虚拟机从经典模式迁移到资源管理器模式。 确保与虚拟机对应的存储和网络也已迁移到资源管理器模式。
3. 创建恢复服务保管库，使用保管库仪表板顶部的“ **备份** ”操作在迁移的虚拟机上配置备份。 了解有关如何 [在恢复服务保管库中启用备份](backup-azure-vms-first-look-arm.md)

