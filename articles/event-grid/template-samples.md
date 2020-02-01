---
title: Azure 资源管理器模板示例 - 事件网格 | Microsoft Docs
description: 本文提供了 GitHub 上适用于 Azure 事件网格的 Azure 资源管理器模板示例的列表。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720616"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>事件网格的 Azure 资源管理器模板

有关要在模板中使用的 JSON 语法和属性，请参阅 [Microsoft.EventGrid 资源类型](/azure/templates/microsoft.eventgrid/allversions)。 下表包含事件网格的 Azure 资源管理器模板链接。

| | |
|-|-|
|**事件网格订阅**||
| [使用 WebHook 终结点的自定义主题和订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| 部署事件网格自定义主题。 创建使用 WebHook 终结点的自定义主题的订阅。 |
| [使用事件中心终结点的自定义主题和订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 创建自定义主题的事件网格订阅。 该订阅使用事件中心作为终结点。 |
| [Azure 订阅或资源组订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| 订阅资源组或 Azure 订阅的事件。 在部署期间指定为目标的资源组是事件的源。 该订阅使用 WebHook 作为终结点。 |
| [Blob 存储帐户和订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| 部署 Azure Blob 存储帐户并订阅该存储帐户的事件。 |
| | |
