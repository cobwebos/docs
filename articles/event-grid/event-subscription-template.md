---
title: 使用模板的 Azure 事件网格订阅
description: 使用资源管理器模板创建事件网格订阅。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: ee0b2c228ae4ea53c0ee9794529aa190334ceed9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>使用资源管理器模板进行事件网格订阅

本文说明如何使用 Azure 资源管理器模板来创建事件网格订阅。 使用的格式因要订阅资源组事件还是要订阅特定资源类型的事件而异。 本文对这两种格式都进行了介绍。

## <a name="subscribe-to-resource-group-events"></a>订阅资源组事件

如果要订阅资源组事件，请使用 `Microsoft.EventGrid/eventSubscriptions` 作为资源类型。 对于事件点类型，可以使用 `WebHook` 或 `EventHub`。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

如果将此模板部署到资源组，请订阅该资源组的事件。

## <a name="subscribe-to-resource-events"></a>订阅资源事件

如果订阅资源事件，请通过在订阅定义中包含资源类型和名称将订阅关联到相应的资源。 对于资源类型，请使用 `<provider-namespace>/<resource-type>/providers/eventSubscriptions`。 对于名称，请使用 `<resource-name>/Microsoft.EventGrid/<subscription-name>`。 对于事件点类型，可以使用 `WebHook` 或 `EventHub`。

以下示例演示如何订阅 Blob 存储事件。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 有关资源管理器的简介，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。
* 若要开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。