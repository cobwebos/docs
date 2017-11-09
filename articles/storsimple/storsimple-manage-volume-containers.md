---
title: "管理 StorSimple 卷容器 | Microsoft Docs"
description: "介绍了如何使用 StorSimple Manager 服务卷容器页添加、修改或删除卷容器。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/24/2016
ms.author: v-sharos
ms.openlocfilehash: bb55a7a4bff0fd4319de6f6ce958686ad8a4142b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>使用 StorSimple Manager 服务管理 StorSimple 卷容器
## <a name="overview"></a>概述
本教程绍了如何使用 StorSimple Manager 服务创建和管理 StorSimple 卷容器。

Microsoft Azure StorSimple 设备中的卷容器包含一个或多个卷，这些卷共享存储帐户、加密和带宽消耗设置。 设备可以为它的所有卷使用多个卷容器。 

卷容器具有以下属性：

* **卷** – 卷容器中包含的分层的或本地固定 StorSimple 卷。 一个卷容器最多可以包含 256 个 StorSimple 卷。
* **加密** – 可以为每个卷容器定义的加密密钥。 此密钥用于对从 StorSimple 设备发送到云的数据进行加密。 军用级 AES 256 位密钥与用户输入的密钥配合使用。 为保护数据安全，建议始终启用云存储加密。
* **存储帐户** – 关联到云存储服务提供商的存储帐户。 同一卷容器中的所有卷都共享此存储帐户。 可以从现有列表中选择一个存储帐户，也可以在创建卷容器时创建一个新帐户，并为该帐户指定访问凭据。
* **云带宽** – 从设备向云发送数据时设备使用的带宽。 在定义此容器时，可以通过指定一个介于 1 和 1000 Mbps 之间的值来强制实施带宽控制。 如果想要设备使用所有可用带宽，请将此字段设置为“无限制”。 还可以创建并应用一个带宽模板以基于计划分配带宽。

![“卷容器”页](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

以下过程介绍了如何使用 StorSimple“卷容器”页完成以下常见操作： operations:

* 添加卷容器 
* 修改卷容器 
* 删除卷容器 

## <a name="add-a-volume-container"></a>添加卷容器
可以执行以下步骤来添加卷容器。

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>修改卷容器
可以执行以下步骤来修改卷容器。

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>删除卷容器
卷容器中具有卷。 只有首先删除卷容器中包含的所有卷后才能删除卷容器。 可以执行以下步骤来删除卷容器。

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>后续步骤
* 详细了解[管理 StorSimple 卷](storsimple-manage-volumes.md)。 
* 了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。

