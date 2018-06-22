---
title: 为 StorSimple 设备管理器服务迁移存储帐户和订阅 | Microsoft Docs
description: 了解如何为 StorSimple 设备管理器服务8000 迁移订阅和存储帐户。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: alkohli
ms.openlocfilehash: 299029e10ac8f0235bb11876aa00c98a21183c0f
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248786"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>迁移与 StorSimple 设备管理器服务相关联的订阅和存储帐户

可能需要将 StorSimple 服务移动到新注册或新订阅。 这些迁移方案要么是关于帐户更改，要么是关于数据中心更改。 通过下表来了解支持的方案和详细的移动步骤。

## <a name="account-changes"></a>帐户更改

| 是否可以迁移…| 支持| 故障时间| Azure 支持进程| 方法|
|-----|-----|-----|-----|-----|
| 将整个订阅（包括 StorSimple 服务和存储帐户）移动到另一注册？ | 是       | 否       | 注册传输<br>使用：<li>当通过新协议购买新的 Azure 承诺时。</li><li>需要将所有帐户和订阅从旧注册迁移到新注册。 这包括旧订阅下的所有 Azure 服务。</li> | 步骤 1：打开 Azure Enterprise 操作支持票证。<li>转到[http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport)。</li><li> 选择“注册管理”，然后选择“从一个注册转移到新注册”。<br>步骤 2：提供所请求的信息<br>包括：<li>源注册号</li><li> 目标注册号</li><li>转移生效日期|
| 将 StorSimple 服务从现有帐户转移到新注册？    | 是       | 否       | 帐户转移<br>使用：<li>当不想进行完整注册转移时。</li><li>只想将特定帐户移动至新注册。</li>| 步骤 1：打开 Azure Enterprise 操作支持票证。<li>转到[http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport)。</li><li>选择“注册管理”，然后选择“将一个 EA 帐户传输到新注册”。<br>步骤 2：提供所请求的信息<br>包括：<li>源注册号</li><li> 目标注册号</li><li>转移生效日期|
| 将 StorSimple 服务从一个订阅移动到另一个订阅？      | 否        |    是         | 无，手动过程|<li>从 StorSimple 设备将数据迁出。</li><li>让设备恢复出厂设置，此操作将删除设备上的一切本地数据。</li><li>使用新订阅向 StorSimple 设备管理器服务注册设备。</li><li>将数据迁移回设备。|
  |能否将 Azure 订阅的所有权转让给其他目录？ | 是       | 否       | 将现有订阅关联到 Azure AD 目录 | 请参阅[将现有订阅关联到 Azure AD 目录的具体步骤](../active-directory/active-directory-how-subscriptions-associated-directory.md)。 正确显示所有内容可能需要长达 10 分钟的时间。|
| 将 StorSimple 设备从一个 StorSimple 设备管理器服务移动到另一个区域中的服务？      | 否        | 是            | 无，手动过程 |同上。|
| 将存储帐户迁移到新的订阅或资源组？     | 是        | 否             |将存储帐户迁移到其他订阅或资源组 |迁移后，如果存储帐户访问密钥已更新，用户需要通过 StorSimple 设备管理器服务，手动为已迁移的存储帐户配置访问密钥。|
| 从经典存储帐户迁移到 Azure 资源管理器存储帐户      | 是        | 否             |从经典部署迁移到 Azure 资源管理器部署 |<li>若要详细了解如何将存储帐户从经典部署迁移到 Azure 资源管理器部署，请转到[迁移经典存储帐户](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account)。</li><li> 如果迁移后存储帐户访问密钥进行了更新，用户需要通过 StorSimple 设备管理器服务，为已迁移的存储帐户同步访问密钥。 这是为了确保 StorSimple 设备可继续正常工作，并且能够将主/备份数据分层级存储到 Azure。 若要详细了解如何同步访问密钥，请转到[轮换工作流](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)。</li><li> 对于 StorSimple 云设备，如果经典存储帐户已迁移，但基础虚拟机仍位于经典部署中设备应该能够正常工作。 如果云设备的基础虚拟机已迁移，停用和删除功能将不起作用。</li><li> 必须先在 Azure 门户中新建 StorSimple 云设备，然后再从旧的云设备进行故障转移。 无法使用经典存储帐户在新的 Azure 门户中创建 StorSimple 云设备，需要使用 Azure 资源管理器存储帐户。 有关详细信息，请参阅[部署和管理 StorSimple 云设备](storsimple-8000-cloud-appliance-u2.md)。</li>|同上。|

## <a name="datacenter-changes"></a>数据中心更改

| 是否可以迁移…| 支持|故障时间| Azure 支持进程| 方法|
|-----|-----|-----|-----|-----|
| 将 StorSimple 服务从一个 Azure 数据中心移动到另一个？ | 否 | 是 |无，手动过程  |<li>从 StorSimple 设备将数据迁出。</li><li>让设备恢复出厂设置，此操作将删除设备上的一切本地数据。</li><li>使用新订阅向新 StorSimple 设备管理器服务注册设备。</li><li>将数据迁移回设备。|
| 将存储帐户从一个 Azure 数据中心转移到另一个？ | 否 |是  |无，手动过程  | 同上。|

## <a name="next-steps"></a>后续步骤

* [部署 StorSimple 设备管理器服务](storsimple-8000-manage-service.md)
* [在 Azure 门户中部署 StorSimple 8000 系列设备](storsimple-8000-deployment-walkthrough-u2.md)
