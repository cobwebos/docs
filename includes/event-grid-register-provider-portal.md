---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 04/16/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8833bb75f5f50372deda3e71c1df90bed9a20054
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "81605594"
---
## <a name="enable-event-grid-resource-provider"></a>启用事件网格资源提供程序

如果以前未在 Azure 订阅中使用过事件网格，则可能需要注册事件网格资源提供程序。

在 Azure 门户中：

1. 在左侧菜单中，选择“订阅”。 
1. 选择要用于事件网格的订阅。
1. 在左侧菜单中的“设置”下，选择“资源提供程序”。  
1. 找到 **Microsoft.EventGrid**。
1. 如果尚未注册，请选择“注册”。  

完成注册可能需要一些时间。 选择“刷新”可更新状态。  当“状态”为“已注册”后，即可继续。  