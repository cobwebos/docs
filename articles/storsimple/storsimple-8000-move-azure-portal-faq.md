---
title: 从经典门户移至 Azure 门户常见问题 | Microsoft 文档
description: 提供有关将 StorSimple 设备从经典门户移至 Azure 门户的常见问题解答。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
ms.locfileid: "27785173"
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>将 StorSimple 设备管理器服务从经典门户移至 Azure 门户：常见问题解答 (FAQ)

## <a name="overview"></a>概述

以下是将在 Azure 经典门户运行的 StorSimple 设备管理器服务移至 Azure 门户时可能会遇到的问题及解答。

问题和解答分为以下几个类别：

* 移动 StorSimple 设备管理器服务
* 移动存储帐户
* 使用基于 Azure 资源管理器的 cmdlet
* 使用 StorSimple 数据管理器服务
* 其他

## <a name="moving-storsimple-device-manager-service"></a>移动 StorSimple 设备管理器服务

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>移至 Azure 门户后，是否仍然可在经典门户中创建 StorSimple Manager 服务？

不会。 将 StorSimple Manager 服务迁移至 Azure 门户后，无法在经典门户中新建服务。 此外，[从 2018 年 1 月 8 日起经典门户将不可用](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users)。 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>我有多个 StorSimple Manager 在经典门户中运行。 是否可对要移至 Azure 门户的管理器进行选择？

不会。 无法选择将哪一个 StorSimple Manager 移至 Azure 门户。 所有处于订阅中的 StorSimple Manager 都会被移动。


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>我启动了将我的服务迁移到新的 Azure 门户。 是否应从经典门户中删除 StorSimple Manager？ 

不会。 请勿尝试删除从经典门户移动的任何服务。 等待迁移完成，将所有的 StorSimple Manager 移至新门户后，不需要从经典门户中删除任何服务。 经典门户最终会被弃用。

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>可否在 Azure 门户中重命名 StorSimple 设备管理器服务？

不会。 一旦在 Azure 门户创建了服务，则无法更改服务名称。 这也适用于设备、卷、卷容器和备份策略等其他实体。

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>可否使用 Azure 资源管理器部署模型创建 8010/8020 StorSimple 云设备？

是的。 可以在 Azure 资源管理器部署模型中新建 8010/8020 StorSimple 云设备。 用于这些云设备的基础虚拟机也存在于资源管理器部署模型中。

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>将订阅迁移至 Azure 门户时，是否会将用于 StorSimple 云设备的基础虚拟机也迁移至 Azure 资源管理部署模型？

StorSimple 服务迁移与用于 StorSimple 云设备的虚拟机关联无关。 即使是现在，你还可以在经典和 Azure 门户中完全管理用于 StorSimple 云设备的虚拟机。

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>可否从 Azure 门户管理现有的经典 8010/8020 StorSimple 云设备？

是的。 可以从 Azure 门户管理与现有 8010/8020 云设备关联的 VM。

如果已创建运行 Update 3.0 或以上版本的 StorSimple 云设备模型 8010/8020，则不会受到将服务移至新 Azure 门户的影响。 你应能够完全管理你的云设备，而不必担心出现任何问题。 

如果你在经典门户中有运行版本早于 Update 3.0 的云设备，则只能使用部分功能。 有关详细信息，请转到[运行版本早于 Update 3 的设备的不支持操作列表](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50)。

无法更新云设备。 使用最新版本的软件新建云设备，然后将现有卷容器故障转移到新建的云设备。 有关详细信息，请转到[故障转移到云设备](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>我的 StorSimple 8000 系列设备当前运行的是 Update 2.0。 我已将服务迁移至新的 Azure 门户。 我的设备已成功连接，但好像不能完全管理我的设备。 如何解决此问题呢？

新的 Azure 门户仅支持运行 Update 3.0 和更高版本的 StorSimple 设备。 如果你的设备正在运行 Update 2.0，则仅可使用该设备的部分功能。 有关详细信息，请转到[运行版本早于 Update 3 的设备的不支持操作列表](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50)。

要完全管理设备，请在设备上安装最新更新。 有关详细信息，请转到[安装 Update 5](storsimple-8000-install-update-5.md)。

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>我刚刚将 StorSimple Manager 服务移到 Azure 门户。 我收到了几条与设备相关的警报。 这些警报与此次移动有关吗？

不会。 移动本身不会导致错误或警报。 请按照警报建议消除相关警报。

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>我正在使用 StorSimple 5000/7000 系列设备。 在 Azure 门户中，它们还受支持吗？

不会。 在 Azure 门户中，不支持 StorSimple 5000/7000 系列设备。

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>我计划将迁移数据从 StorSimple 5000/7000 系列设备迁移到 StorSimple 8000 系列设备。 将服务移至 Azure 门户会对我的数据迁移有何影响？ 

你可以将数据从你的 StorSimple 5000/7000 系列设备迁移到在 Azure 门户中运行的 StorSimple 8000 系列设备。 要使你的数据从 5000/7000 系列迁移到 8000 系列，必须[通过 Microsoft 支持部门记录支持票证](storsimple-8000-contact-microsoft-support.md)。


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>移动订阅、存储帐户、资源群组

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>可否将 StorSimple 设备管理器从一个订阅移动到 Azure 门户中的另一个订阅？

不会。 不支持将 StorSimple 设备管理器服务从一个订阅移到另一个订阅。 你可以执行包括以下步骤的手动过程：

* 从 StorSimple 设备将数据迁出。
* 让设备恢复出厂设置，此操作将删除设备上的一切本地数据。
* 使用新订阅向 StorSimple 设备管理器服务注册设备。
* 将数据迁移回设备。

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>是否必须将存储帐户迁移到 Azure 资源管理器部署模型？

不会。 可从 Azure 门户完全管理经典存储帐户。 将 StorSimple 服务移至 Azure 门户时，存储帐户将继续如以前一样运行。

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>在此服务迁移至 Azure 门户后，存储帐户会发生哪些变化？

服务移动与存储帐户的管理无关。 可在 Azure 门户中同时完全管理经典和 Azure 资源管理器存储帐户。 一旦将服务移至 Azure 门户，设备应使用此存储帐户继续运行，且应不会对数据产生影响。

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>可否将 StorSimple 设备管理器从一个资源组迁移到另一个资源组？

不会。 不能将使用一个资源组创建的 StorSimple 设备管理器移至另一个资源组。

## <a name="using-azure-resource-manager-based-cmdlets"></a>使用基于 Azure 资源管理器的 cmdlet

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>我已经移到新的 Azure 门户。 现在我的基于 Azure 经典部署模型 PowerShell cmdlet 的脚本不起作用？ 我需要做什么？

现有的 Azure 经典部署模型 PowerShell cmdlet 在 Azure 门户中不受支持。 请更新脚本以通过 Azure 资源管理器管理设备。 有关更新脚本的详细信息，请转到[针对新 Azure 门户的示例](https://github.com/anoobbacker/storsimpledevicemgmttools)。

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>我刚刚移到 Azure 门户，需要滚动服务数据加密密钥。 此选项在 Azure 门户中位于什么位置？

用于滚动服务数据加密密钥的选项不在 Azure 门户中。 有关如何在 Azure 门户中执行此滚动的详细信息，请转到[更改服务数据加密密钥](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)。

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>我当前在 StorSimple 设备上使用的是用于 StorSimple 的 Windows PowerShell cmdlet 来执行提取支持包等操作。 移至新 Azure 门户时，这些 cmdlet 会受到影响吗？

不会。 通过移至新 Azure 门户的服务，不会影响与本地 StorSimple 设备（自身不受移动影响）关联的用于 StorSimple 的 Windows PowerShell cmdlet。 即使在 Azure 门户中，你也可以继续使用这些 cmdlet 创建支持包，不会出现任何问题。 仅 Azure 经典部署模型 PowerShell cmdlet 会受到此移动的影响。

## <a name="moving-storsimple-data-manager-service"></a>移动 StorSimple 数据管理器服务

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>我正在使用 Azure 经典门户中的 StorSimple 数据管理器服务。 应如何继续进行此移动？

如果正在使用 StorSimple 数据管理器服务，你已自动移至 Azure 门户。

## <a name="miscellaneous"></a>其他

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>我当前运行的是适用于 8000 系列设备的 StorSimple Snapshot Manager。 移至 Azure 门户时，会对 StorSimple Snapshot Manager 产生任何影响吗？

不会。 将服务移至 Azure 门户时，不会对 StorSimple Snapshot Manager 产生影响。 设备和 StorSimple Snapshot Manager 将继续如以前一样运行。

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>可否重命名我的 StorSimple 设备、卷容器或卷？

不会。 无法在 Azure 门户中重命名设备、卷、卷容器或备份策略。

## <a name="next-steps"></a>后续步骤

详细了解[在运行 Update 5.0 之前版本的设备上支持的操作](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50)。



