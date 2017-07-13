---
title: "通过门户启用 Azure 事件中心捕获 | Microsoft Docs"
description: "通过 Azure 门户启用事件中心捕获功能。"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---

# 通过 Azure 门户启用事件中心捕获
<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

可以使用 [Azure 门户](https://portal.azure.com)在创建事件中心时配置捕获。 在“创建事件中心”门户边栏选项卡中单击“启用”按钮即可启用捕获。 然后，单击边栏选项卡的“容器”部分即可配置存储帐户和容器。 由于事件中心捕获对存储使用服务到服务身份验证，因此无需指定存储连接字符串。 资源选取器自动为存储帐户选择资源 URI。 如果使用 Azure Resource Manager，必须以字符串形式显式提供此 URI。

默认时间段为 5 分钟。 最小值为 1，最大值为 15。 **大小**窗口的范围为 10-500 MB。

![][1]

## 将捕获添加到现有的事件中心
<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

可以在事件中心命名空间中的现有事件中心上配置捕获。 较早的“消息类型”或“混合类型”命名空间中未提供此功能。 若要对现有的事件中心启用“捕获”功能，或者要更改“捕获”设置，请单击命名空间以加载“概要”边栏选项卡，然后单击要启用或更改“捕获”设置的事件中心。 最后，单击打开的边栏选项卡的“属性”部分，如下图所示：

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

## 后续步骤
<a id="next-steps" class="xliff"></a>

还可以通过 Azure Resource Manager 模板配置事件中心捕获。 有关详细信息，请参阅[通过 Azure Resource Manager 模板启用捕获](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。

