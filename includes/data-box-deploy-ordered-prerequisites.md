---
title: 先决条件包括同一文件中两个选项卡共享的文件 | Microsoft Docs
description: Azure Data Box 部署已排序的先决条件
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/15/2020
ms.author: v-grpr
ms.openlocfilehash: 6aaf57d9bcdfb1f350e1d54937e9c705dd32116e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392466"
---
### <a name="for-service"></a>对于服务

[!INCLUDE [Data Box service prerequisites](data-box-supported-subscriptions.md)]

### <a name="for-device"></a>对于设备

在开始之前，请确保：

* 应该将一个主机连接到数据中心网络。 Data Box 会从此计算机复制数据。 主机必须按照 [Azure Data Box 系统要求](../articles/databox/data-box-system-requirements.md)中的说明运行支持的操作系统。
* 数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，则可使用 1-GbE 数据链路，但复制速度会受影响。
