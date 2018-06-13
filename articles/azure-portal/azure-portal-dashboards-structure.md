---
title: Azure 仪表板结构 | Microsoft Docs
description: 本文介绍 Azure 仪表板的 JSON 结构
services: azure-portal
documentationcenter: ''
author: adamab
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: fa50b31a0c8e9077658106039c4fbd2eef8b4367
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165173"
---
# <a name="the-structure-of-azure-dashboards"></a>Azure 仪表板结构
本文档将使用以下仪表板作为示例，介绍 Azure 仪表板的结构：

![示例仪表板](./media/azure-portal-dashboards-structure/sample-dashboard.png)

由于共享的 [Azure 仪表板是资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)，所以此仪表板可以表示为 JSON。  以下 JSON 表示上面构思的仪表板。

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>常见资源属性

现在分别介绍 JSON 的各个相关部分。  顶级属性，即 id、名称、类型、位置和标记属性是所有 Azure 资源类型所共有的属性。 也就是说，它们与仪表板内容没有太大关系。

### <a name="the-id-property"></a>Id 属性

Azure 资源 id 服从 [Azure 资源命名约定](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 门户创建仪表板时通常选择 GUID 形式的 id，如果以编程方式创建 id，可随意使用任何有效的名称。 

### <a name="the-name-property"></a>“名称”属性
名称是资源 Id 中不包含订阅、资源类型或资源组信息的那部分。 本质上，它是资源 id 的最后一段。

### <a name="the-type-property"></a>“类型”属性
所有仪表板均属于 Microsoft.Portal/dashboards 类型。

### <a name="the-location-property"></a>“位置”属性
与其他资源不同，仪表板没有运行时组件。  对于仪表板，location 指示用于存储仪表板 JSON 表示形式的主地理位置。 该值应为可使用[订阅资源上的位置 API](https://docs.microsoft.com/rest/api/resources/subscriptions) 提取的位置代码之一。

### <a name="the-tags-property"></a>“标记”属性
标记是 Azure 资源的一项常见功能，可使用它按任意名称-值对来整理资源。 对于仪表板，有一个名为“hidden-title”的特殊标记。 如果仪表板已填充此属性，则它将用作门户中仪表板的显示名称。 Azure 资源 Id 不能重命名，但标记可以。 通过标记可重命名仪表板的显示名称。

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>属性对象
属性对象包含两个属性：可重用功能区和元数据。 “可重用功能区”属性包含有关仪表板上磁贴（也称为 部件）的信息。  “元数据”属性用于将来可能会出现的功能。

### <a name="the-lenses-property"></a>“可重用功能区”属性
“可重用功能区”属性包含仪表板。 请注意，此示例中的可重用功能区对象包含名为“0”的单个属性。 可重用功能区是一个分组概念，当前未在仪表板中实现。 现在，所有仪表板在可重用功能区对象上具有此单个属性，即“0”。

### <a name="the-lens-object"></a>可重用功能区对象
“0”下面的对象包含两个属性：顺序和部件。  在当前版本的仪表板中，顺序始终为 0。 “部件”属性包含定义仪表板上各个部件（也称为 磁贴）的对象。

部件对象包含每个部件的一个属性，其中属性名称为一个数字。 此数字并不重要。 

### <a name="the-part-object"></a>部件对象
每个部件对象具有位置和元数据。

### <a name="the-position-object"></a>位置对象
“位置”属性包含以 x、y、rowSpan 和 colSpan 表示的部件大小和位置信息。 值以网格单位表示。 仪表板处于自定义模式时，网格单位可见，如下所示。 如果希望磁贴具有两个网格单位的宽度、一个网格单元的高度，且位于仪表板左上角，则位置对象如下所示：

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![网格单元](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>元数据对象
每个部件都具有一个元数据属性，一个对象仅有一个必需的属性，名为“类型”。 此字符串告知门户要显示的磁贴。 示例仪表板使用这些类型的磁贴：


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – 用于显示监视度量值
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – 用于显示带列表、链接等基本格式的文本或图像。
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – 用于显示来自 YouTube、Channel9 的视频，以及使用 html 视频标记的任何其他类型的视频。
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – 用于显示 Azure 虚拟机的名称和状态。

所有部件类型都有其自己的配置。 可能的配置属性有“输入”、“设置”和“资产”。 

### <a name="the-inputs-object"></a>“输入”对象
输入对象通常包含将磁贴绑定到资源实例的信息。  示例仪表板中的虚拟机部件包含使用 Azure 资源 id 来表达绑定的单个输入。  在所有 Azure 资源中，此资源 id 格式是统一的。

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
度量值图表部件含有表示要绑定到的资源的单个输入以及有关显示的度量值的信息。 以下是显示 Network In 和 Network Out 度量值的磁贴的输入。

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>设置对象
设置对象包含部件的可配置元素。  在示例仪表板中，Markdown 部件使用设置来存储自定义 Markdown 内容和可配置的标题、副标题。

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

同样，视频磁贴也有自己的设置，包含指向要播放的视频的指针、自动播放设置，以及可选的标题信息。

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>资产对象
绑定到第一类可管理门户对象（称为“资产”）的磁贴通过资产对象表示此关系。  在示例仪表板中，虚拟机磁贴包含此资产说明。  IdInputName 属性告知门户 id 输入包含资产的唯一标识符（示例中为资源 id）。大多数 Azure 资源类型在门户中定义资产。

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`