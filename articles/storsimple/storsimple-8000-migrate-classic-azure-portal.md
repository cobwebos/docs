---
title: "为 StorSimple 设备管理器服务迁移存储帐户和订阅 | Microsoft Docs"
description: "了解如何为 StorSimple 设备管理器服务8000 迁移订阅和存储帐户。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: a64cf0ba2a4646a03c864193fb93de4f54f38039
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>迁移与 StorSimple 设备管理器服务相关联的订阅和存储帐户

可能需要将 StorSimple 服务移动到新注册或新订阅。 这些迁移方案要么是关于帐户更改，要么是关于数据中心更改。 通过下表来了解支持的方案和详细的移动步骤。

## <a name="account-changes"></a>帐户更改

| 是否可以迁移…| 支持| 故障时间| Azure 支持进程| 方法|
|-----|-----|-----|-----|-----|
| 将整个订阅（包括 StorSimple 服务和存储帐户）移动到另一注册？ | 是       | 否       | 注册传输<br>使用：<li>当通过新协议购买新的 Azure 承诺时。</li><li>需要将所有帐户和订阅从旧注册迁移到新注册。 这包括旧订阅下的所有 Azure 服务。</li> | 步骤 1：打开 Azure Enterprise 操作支持票证。<li>转至 [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt)。</li><li> 选择“注册管理”，然后选择“从一个注册转移到新注册”。<br>步骤 2：提供所请求的信息<br>包括：<li>源注册号</li><li> 目标注册号</li><li>转移生效日期|
| 将 StorSimple 服务从现有帐户转移到新注册？    | 是       | 否       | 帐户转移<br>使用：<li>当不想进行完整注册转移时。</li><li>只想将特定帐户移动至新注册。</li>| 步骤 1：打开 Azure Enterprise 操作支持票证。<li>转至 [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt)。</li><li>选择“注册管理”，然后选择“将一个 EA 帐户传输到新注册”。<br>步骤 2：提供所请求的信息<br>包括：<li>源注册号</li><li> 目标注册号</li><li>转移生效日期|
| 将 StorSimple 服务从一个订阅移动到另一个订阅？      | 否        |    是         | 无，手动过程|<li>从 StorSimple 设备将数据迁出。</li><li>让设备恢复出厂设置，此操作将删除设备上的一切本地数据。</li><li>使用新订阅向 StorSimple 设备管理器服务注册设备。</li><li>将数据迁移回设备。|
| 将 StorSimple 设备从一个 StorSimple 设备管理器服务移动到另一个区域中的服务？      | 否        | 是            | 无，手动过程 |同上。|

## <a name="datacenter-changes"></a>数据中心更改

| 是否可以迁移…| 支持|故障时间| Azure 支持进程| 方法|
|-----|-----|-----|-----|-----|-----|
| 将 StorSimple 服务从一个 Azure 数据中心移动到另一个？ | 否 | 是 |无，手动过程  |<li>从 StorSimple 设备将数据迁出。</li><li>让设备恢复出厂设置，此操作将删除设备上的一切本地数据。</li><li>使用新订阅向新 StorSimple 设备管理器服务注册设备。</li><li>将数据迁移回设备。|
| 将存储帐户从一个 Azure 数据中心转移到另一个？ | 否 |是  |无，手动过程  | 同上。|

## <a name="next-steps"></a>后续步骤

* [部署 StorSimple 设备管理器服务](storsimple-8000-manage-service.md)
* [在 Azure 门户中部署 StorSimple 8000 系列设备](storsimple-8000-deployment-walkthrough-u2.md)
