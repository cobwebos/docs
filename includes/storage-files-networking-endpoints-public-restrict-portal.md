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
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464947"
---
导航到仅限从特定虚拟网络访问公共终结点的存储帐户。 在该存储帐户的目录中，选择“防火墙和虚拟网络”。 

在页面顶部，选中“选定的网络”单选按钮。 随后会显示一些用于控制公共终结点限制的设置。 单击“+添加现有虚拟网络”，选择应允许其通过公共终结点访问存储帐户的特定虚拟网络。 这需要选择一个虚拟网络以及该虚拟网络的子网。 

选中“允许受信任的 Microsoft 服务访问此服务帐户”，以允许受信任的第一方 Microsoft 服务（例如 Azure 文件同步）访问存储帐户。

[![“防火墙和虚拟网络”边栏选项卡的屏幕截图，其中显示了允许通过公共终结点访问存储帐户的特定虚拟网络](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)