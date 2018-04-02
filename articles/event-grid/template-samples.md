---
title: Azure 资源管理器模板示例 - 事件网格 | Microsoft Docs
description: 事件网格的 Azure 资源管理器模板示例
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>事件网格的 Azure 资源管理器模板

下表包含事件网格的 Azure 资源管理器模板链接。

| | |
|-|-|
|**事件网格订阅**||
| [使用 WebHook 终结点的自定义主题和订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| 部署事件网格自定义主题。 创建使用 WebHook 终结点的自定义主题的订阅。 |
| [使用事件中心终结点的自定义主题和订阅](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| 创建现有自定义主题的事件网格订阅。 该订阅使用事件中心作为终结点。 |
| [资源组订阅](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| 订阅资源组的事件。 在部署期间指定为目标的资源组是事件的源。 该订阅使用事件中心作为终结点。 |
| [Blob 存储帐户和订阅](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| 部署 Azure Blob 存储帐户并订阅该存储帐户的事件。 |
| | |
