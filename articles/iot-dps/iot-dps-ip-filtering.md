---
title: Azure IoT DPS IP 连接筛选器 | Microsoft Docs
description: 如何使用 IP 筛选阻止从特定 IP 地址到 Azure IoT DPS 实例的连接。 可阻止来自单独 IP 地址或 IP 地址范围的连接。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 580c378df5fc3912aa540b5d85adf99bc42605e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511936"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>使用 Azure IoT DPS IP 连接筛选器

安全性对于任何 IoT 解决方案来说都是一个重要方面。 作为安全配置的一部分，有时需要显式指定设备可从其连接的 IP 地址。 使用 Azure IoT 中心设备预配服务 (DPS) 的 IP 筛选器  功能，可以配置规则来拒绝或接受来自特定 IPv4 地址的流量。

## <a name="when-to-use"></a>何时使用

在两个具体用例中，可以阻止从特定 IP 地址到 DPS 终结点的连接：

* DPS 只能从指定范围的 IP 地址接收流量并拒绝任何其他流量。 例如，将 DPS 与 [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) 配合使用，以在 DPS 与设备之间创建专用连接。

* 需要拒绝来自 DPS 管理员已标识为可疑地址的 IP 地址的流量。

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

在 DPS 实例级别应用 IP 筛选器规则。 因此，IP 筛选器规则适用于使用任意受支持协议和从设备和后端应用发出的所有连接。

与 DPS 实例的拒绝 IP 规则匹配的 IP 地址发出的任何连接尝试都会收到“未授权”401 状态代码和说明。 响应消息不提及 IP 规则。

## <a name="default-setting"></a>默认设置

默认情况下，门户中针对 DPS 的“IP 筛选器”网格为空。  此默认设置意味着 DPS 会接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

![IoT DPS 默认 IP 筛选器设置](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>添加或编辑 IP 筛选器规则

若要添加 IP 筛选器规则，请选择“+ 添加 IP 筛选器规则”。 

![向 IoT DPS 添加 IP 筛选规则](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

选择“添加 IP 筛选器规则”  后，请填写字段。

![在选择“添加 IP 筛选器规则”后](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* 提供 IP 筛选器规则的**名称**。 此项必须是不区分大小写的唯一字母数字字符串，长度不超过 128 个字符。 只接受 ASCII 7 位字母数字字符以及以下字符：`{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。

* 选择“允许”或“阻止”作为 IP 筛选器规则的“操作”。   

填写这些字段后，请选择“保存”以保存该规则  。 会出现一个提醒，通知你更新正在进行。

![关于保存 IP 筛选规则的通知](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

当存在的 IP 筛选规则达到最大数目 10 时，“添加”  选项被禁用。

若要编辑现有规则，请选择要更改的数据，进行更改，然后选择“保存”以保存所做的编辑。 

> [!NOTE]
> 拒绝 IP 地址可以阻止其他 Azure 服务与 DPS 实例交互。

## <a name="delete-an-ip-filter-rule"></a>删除 IP 筛选器规则

若要删除 IP 筛选器规则，请选择与该行对应的垃圾桶图标，然后选择“保存”。  此时会删除规则并保存所做的更改。

![删除 IoT DPS IP 筛选规则](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>在代码中更新 IP 筛选规则

可以使用 Azure 资源提供程序的 REST 终结点检索和修改 DPS IP 筛选器。 请参阅 [createorupdate 方法](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate)`properties.ipFilterRules`。

Azure CLI 或 Azure PowerShell 当前不支持更新 DPS IP 筛选器规则，但可以通过 Azure 资源管理器模板来完成该更新操作。 请参阅 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)，了解如何使用资源管理器模板。 以下模板示例显示了如何创建、编辑和删除 DPS IP 筛选器规则。

### <a name="add-an-ip-filter-rule"></a>添加 IP 筛选器规则

以下模板示例创建一项名为“AllowAll”的新的 IP 筛选器规则，该规则接受所有流量。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

根据要求更新模板的 IP 筛选器规则属性。

| 属性                | 说明 |
| ------------------------ | ----------- |
| **FilterName**           | 提供 IP 筛选器规则的名称。 此项必须是不区分大小写的唯一字母数字字符串，长度不超过 128 个字符。 仅接受 ASCII 7 位字母数字字符和 {'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}。 |
| **操作**               | 接收的值为 **Accept** 或 **Reject** （充当 IP 筛选器规则的操作）。 |
| **ipMask**               | 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。 |


### <a name="update-an-ip-filter-rule"></a>更新 IP 筛选器规则

以下模板示例更新名为“AllowAll”的 IP 筛选器规则（如前所示），该规则拒绝所有流量。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>删除 IP 筛选器规则

以下模板示例删除 DPS 实例的所有 IP 筛选器规则。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则按顺序应用，与 IP 地址匹配的第一条规则决定了是采取接受操作还是拒绝操作。

例如，若要接受 192.168.100.0/22 范围内的地址并拒绝所有其他地址，则网格中的第一条规则应接受 192.168.100.0/22 这一地址范围。 下一个规则应通过使用 0.0.0.0/0 范围拒绝所有地址。

可以通过单击行开头的三个竖直点并使用拖放操作更改 IP 筛选规则在网格中的顺序。

若要保存新的 IP 筛选器规则顺序，请单击“保存”。 

![更改 DPS IP 筛选器规则的顺序](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>后续步骤

若要进一步探索管理 DPS，请参阅：

* [了解 IoT DPS IP 地址](iot-dps-understand-ip-address.md)
* [使用 Azure CLI 配置 DPS](how-to-manage-dps-with-cli.md)
* [控制对 DPS 的访问](how-to-control-access.md)
