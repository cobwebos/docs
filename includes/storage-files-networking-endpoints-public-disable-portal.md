---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 295b9fc842d926d0a8b264ed5200b60ac7bd2261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464949"
---
导航到要限制对其公共终结点的所有访问的存储帐户。 在该存储帐户的目录中，选择“防火墙和虚拟网络”。

在页面顶部，选中“选定的网络”单选按钮。 随后会显示一些用于控制公共终结点限制的设置。 选中“允许受信任的 Microsoft 服务访问此服务帐户”，以允许受信任的第一方 Microsoft 服务（例如 Azure 文件同步）访问存储帐户。

[![“防火墙和虚拟网络”边栏选项卡的屏幕截图，其中显示实施了相应的限制](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png#lightbox)