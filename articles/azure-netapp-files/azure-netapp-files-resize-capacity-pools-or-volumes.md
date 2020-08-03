---
title: 为 Azure NetApp 文件重设容量池或卷的大小 | Microsoft Docs
description: 了解如何更改容量池或卷的大小。 重设容量池大小会更改购买的 Azure NetApp 文件容量。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7d1c54d05430600e9fffa2659dbaaf6db0b086b5
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512867"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>重设容量池或卷的大小
可以根据需要更改容量池或卷的大小。 

## <a name="resize-the-capacity-pool"></a>重设容量池大小 

可以按 1-TiB 的增量或减量来更改容量池大小。 但是，容量池大小不能小于 4 TiB。 重设容量池大小会更改购买的 Azure NetApp 文件容量。

1. 在“管理 NetApp 帐户”边栏选项卡中，单击要重设大小的容量池。 
2. 右键单击容量池名称，或单击容量池所在行末尾的“...”图标以显示上下文菜单。 
3. 使用上下文菜单选项来重设容量池大小或删除容量池。

## <a name="resize-a-volume"></a>重设卷大小

可以根据需要更改卷大小。 卷的容量消耗是依据其池的预配容量计数的。

1. 在“管理 NetApp 帐户”边栏选项卡中，单击“卷”****。 
2. 右键单击要重设大小的卷的名称，或单击卷所在行末尾的“...”图标以显示上下文菜单。
3. 使用上下文菜单选项来重设卷大小或删除卷。

