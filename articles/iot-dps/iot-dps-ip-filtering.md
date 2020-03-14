---
title: Azure IoT DPS IP 连接筛选器 |Microsoft Docs
description: 如何使用 IP 筛选来阻止从特定 IP 地址到 Azure IoT DPS 实例的连接。 可阻止来自单独 IP 地址或 IP 地址范围的连接。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284910"
---
# <a name="use-ip-filters"></a>使用 IP 筛选器

安全性是任何 IoT 解决方案的一个重要方面。 作为安全配置的一部分，有时需要显式指定设备可从其连接的 IP 地址。 使用 Azure IoT 中心设备预配服务（DPS）的*IP 筛选器*功能，可以配置用于拒绝或接受来自特定 IPv4 地址的流量的规则。

## <a name="when-to-use"></a>何时使用

有两个特定用例可用于阻止从特定 IP 地址连接到 DPS 终结点：

* 你的 DPS 只应从指定范围内的 IP 地址接收流量，并拒绝其他所有内容。 例如，你将 DPS 与[Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services)一起使用，以便在 DPS 和设备之间创建专用连接。

* 需要拒绝由 DPS 管理员标识为可疑的 IP 地址的流量。

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

IP 筛选器规则在 DPS 实例级别应用。 因此，IP 筛选器规则适用于使用任意受支持协议和从设备和后端应用发出的所有连接。

从与你的 DPS 实例中的拒绝 IP 规则匹配的 IP 地址进行的任何连接尝试都会收到未经授权的401状态代码和说明。 响应消息不会提及 IP 规则。

## <a name="default-setting"></a>默认设置

默认情况下，用于 DPS 的门户中的 " **IP 筛选器**" 网格为空。 此默认设置表示你的 DPS 接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

![IoT DPS 默认 IP 筛选器设置](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>添加或编辑 IP 筛选器规则

若要添加 IP 筛选器规则，请选择 " **+ 添加 Ip 筛选器规则**"。

![向 IoT DPS 添加 IP 筛选器规则](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

选择 "**添加 IP 筛选器规则**" 后，填写字段。

![选择 "添加 IP 筛选器规则" 后](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* 提供 IP 筛选规则的**名称**。 这必须是唯一的、不区分大小写的字母数字字符串，最长为128个字符。 只接受 ASCII 7 位字母数字字符以及以下字符：`{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。

* 选择 "**允许**" 或 "**阻止**" 作为 IP 筛选器规则的**操作**。

填写字段后，选择 "**保存**" 以保存规则。 你会看到一个警报，通知你更新正在进行。

![关于保存 IP 筛选规则的通知](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

当存在的 IP 筛选规则达到最大数目 10 时，“添加”选项被禁用。

若要编辑现有规则，请选择要更改的数据，进行更改，然后选择 "**保存**" 以保存编辑。

> [!NOTE]
> 拒绝 IP 地址可阻止其他 Azure 服务与 DPS 实例进行交互。

## <a name="delete-an-ip-filter-rule"></a>删除 IP 筛选器规则

若要删除 IP 筛选器规则，请在该行上选择垃圾桶图标，然后选择 "**保存**"。 删除该规则，并保存更改。

![删除 IoT DPS IP 筛选规则](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>在代码中更新 IP 筛选器规则

可以使用 Azure 资源提供程序的 REST 终结点检索和修改你的 DPS IP 筛选器。 请参阅 [createorupdate 方法](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate)`properties.ipFilterRules`。

Azure CLI 或 Azure PowerShell 当前不支持更新 DPS IP 筛选器规则，但可以通过 Azure 资源管理器模板来完成。 有关使用资源管理器模板的指导，请参阅[Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)。 以下模板示例显示了如何创建、编辑和删除 DPS IP 筛选规则。

### <a name="add-an-ip-filter-rule"></a>添加 IP 筛选器规则

以下模板示例创建一个名为 "AllowAll" 的新的 IP 筛选器规则，该规则接受所有流量。

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

根据您的要求更新模板的 IP 筛选器规则属性。

| 特性                | 说明 |
| ------------------------ | ----------- |
| **FilterName**           | 提供 IP 筛选规则的名称。 这必须是唯一的、不区分大小写的字母数字字符串，最长为128个字符。 仅 ASCII 7 位字母数字字符加上 {"-"、"："、"/"、"\'、". "、" + "、"% "、" _ "、" # "、" * "、"？ "，"！ "接受" （'，'） "、"，"、" = "、" @ "、"; "、" ""}。 |
| **Action**               | 接受的值为**Accept** 或 **拒绝** 作为 IP 筛选器规则的操作。 |
| **ipMask**               | 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。 |


### <a name="update-an-ip-filter-rule"></a>更新 IP 筛选器规则

以下模板示例将更新名为 "AllowAll" 的 IP 筛选器规则，如前文所述，拒绝所有流量。

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

以下模板示例将删除 DPS 实例的所有 IP 筛选器规则。

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

IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

例如，如果希望接受 192.168.100.0/22 范围中的地址并拒绝任何其他地址，则网格中的第一个规则应接受 192.168.100.0/22 地址范围。 下一个规则应通过使用 0.0.0.0/0 范围拒绝所有地址。

可以通过单击行开头的三个竖直点并使用拖放操作更改 IP 筛选规则在网格中的顺序。

若要保存新的 IP 筛选器规则顺序，请单击“保存”。

![更改你的 DPS IP 筛选规则的顺序](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>后续步骤

若要进一步了解管理 DPS，请参阅：

* [了解 IoT DPS IP 地址](iot-dps-understand-ip-address.md)
* [使用 Azure CLI 配置 DPS](how-to-manage-dps-with-cli.md)
* [控制对 DPS 的访问](how-to-control-access.md)
