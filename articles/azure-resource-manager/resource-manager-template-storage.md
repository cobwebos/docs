---
title: "用于存储的 Resource Manager 模板 | Microsoft Docs"
description: "介绍用于通过模板部署存储帐户的资源管理器架构。"
services: azure-resource-manager,storage
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 25d35683-fe99-4a11-8b1a-b80accab58e7
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: b089bc075ec3ec718eb21357bbbc5c4af09f4af1
ms.openlocfilehash: b01125160875e367b9b6e1d02031c5dd80b41594


---
# <a name="storage-account-template-schema"></a>存储帐户模板架构
创建存储帐户。

## <a name="schema-format"></a>架构格式
若要创建存储帐户，请将以下架构添加到模板的资源节中。

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>值
下表描述了需要在架构中设置的值。

| Name | 值 |
| --- | --- |
| type |枚举<br />必选<br />**Microsoft.Storage/storageAccounts**<br /><br />要创建的资源类型。 |
| apiVersion |枚举<br />必选<br />**2015-06-15** 或 **2015-05-01-preview**<br /><br />要用于创建该资源的 API 版本。 |
| 名称 |String<br />必选<br />3 到 24 个字符，仅限数字和小写字母。<br /><br />要创建的存储帐户的名称 该名称必须在全 Azure 中唯一。 请考虑在命名约定中使用 [uniqueString](resource-group-template-functions.md#uniquestring) 函数，如以下示例中所示。 |
| location |String<br />必选<br />支持存储帐户的区域。 若要确定有效的区域，请参阅[支持的区域](resource-manager-supported-services.md#supported-regions)。<br /><br />用于托管存储帐户的区域。 |
| properties |对象<br />必选<br />[properties 对象](#properties)<br /><br />一个对象，用于指定要创建的存储帐户的类型。 |

<a id="properties" />

### <a name="properties-object"></a>属性对象
| Name | 值 |
| --- | --- |
| accountType |String<br />必选<br />**Standard_LRS**、**Standard_ZRS**、**Standard_GRS**、**Standard_RAGRS** 或 **Premium_LRS**<br /><br />存储帐户的类型。 允许的值对应于标准本地冗余、标准区域冗余、标准地域冗余、标准读取访问地域冗余和高级本地冗余。 有关这些帐户类型的信息，请参阅 [Azure 存储复制](../storage/storage-redundancy.md)。 |

## <a name="examples"></a>示例
以下示例使用基于资源组 ID 的唯一名称部署标准本地冗余存储帐户。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>快速入门模板
有许多快速入门模板包含存储帐户。 以下模板演示了一些常见方案：

* [创建标准存储帐户](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create)
* [简单部署 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [简单部署 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [创建 CDN 配置文件、CDN 终结点，将存储帐户作为源]https://github.com/Azure/azure-quickstart-templates/tree/master/201-cdn-with-storage-account)
* [使用 Powershell DSC 扩展创建包含 9 个 VM 的高可用性 SharePoint 场](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-server-farm-ha)
* [简单部署已启用 WAD 的 5 节点安全 Service Fabric 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)
* [从 Windows 映像创建包含 4 个空数据磁盘的虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)

## <a name="next-steps"></a>后续步骤
* 有关存储的常规信息，请参阅 [Microsoft Azure 存储简介](../storage/storage-introduction.md)。
* 有关在虚拟机中使用新存储帐户的示例模板，请参阅[部署简单的 Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) 或[部署简单的 Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)。




<!--HONumber=Jan17_HO1-->


