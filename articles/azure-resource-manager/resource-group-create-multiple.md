---
title: "部署 Azure 资源的多个实例 | Microsoft Docs"
description: "在部署资源时使用 Azure 资源管理器模板中的复制操作和数组执行多次迭代。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/19/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>在 Azure Resource Manager 模板中部署资源的多个实例
本主题演示如何在您的 Azure 资源管理器模板中进行迭代操作，以创建多个资源实例。

## <a name="copy-and-copyindex"></a>copy 和 copyIndex
要多次创建的资源将采用以下格式：

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

请注意，循环访问的次数将在复制对象中指定：

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

计数值必须是不超过 800 的正整数。

请注意，每个资源的名称都包括 `copyIndex()` 函数，用于返回循环中的当前迭代。

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

如果部署三个网站，则它们被命名为：

* examplecopy-0
* examplecopy-1
* examplecopy-2。

若要偏移索引值，可以将某个值传递到 copyIndex() 函数中，如 `copyIndex(1)`。 要执行的迭代次数仍被指定在 copy 元素中，但 copyIndex 的值已按指定的值发生了偏移。 因此，请使用于前一示例中相同的模板，但指定 copyIndex(1) 会部署具有以下名称的三个网站：

* examplecopy-1
* examplecopy-2
* examplecopy-3

Resource Manager 将并行创建资源。 因此，不保证创建的顺序。 若要按顺序创建循环访问的资源，请参阅 [Azure Resource Manager 模板的顺序循环](resource-manager-sequential-loop.md)。 

只能将 copy 对象应用于顶级资源。 不能将其应用于资源类型上的属性或应用于子资源。 以下伪代码示例展示了可应用 copy 的位置：

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

若要循环访问子资源，请参阅[创建子资源的多个实例](#create-multiple-instances-of-a-child-resource)。

虽然无法将 copy 应用于属性，但该属性仍然是包含该属性的资源的迭代的一部分。 因此，可以在属性中使用 copyIndex() 来指定值。 若要创建一个属性的多个值，请参阅[在资源类型上创建多个属性实例](resource-manager-property-copy.md)。

## <a name="use-copy-with-array"></a>对数组使用复制
当使用数组时，copy 操作十分有用，因为这样可以迭代数组中的每个元素。 部署具有以下名称的三个网站：

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

请使用以下模版：

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

请注意，`length` 函数用于指定计数。 请提供该数组作为 length 函数的参数。

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

## <a name="depend-on-resources-in-a-loop"></a>依赖于循环中的资源
然后使用 `dependsOn` 元素指定部署一个资源后再部署另一个资源。 若要部署的资源依赖于循环中的资源集合，请在 dependsOn 元素中提供 copy 循环的名称。 以下示例演示了如何在部署虚拟机之前部署 3 个存储帐户。 此处并未显示完整的虚拟机定义。 请注意，copy 元素的名称设置为 `storagecopy`，而虚拟机的 dependsOn 元素也设置为 `storagecopy`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
            "copy": { 
                "name": "storagecopy", 
                "count": 3 
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>创建子资源的多个实例
不能对子资源使用 copy 循环。 若要创建通常定义为嵌套在另一个资源中的资源的多个实例，必须将该资源创建为顶级资源。 通过 type 和 name 属性定义与父资源的关系。

例如，假设通常将数据集定义为数据工厂中的子资源。

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

若要创建数据集的多个实例，请将其移出数据工厂。 数据集必须与数据工厂处于同一级别，但它仍是数据工厂的子资源。 通过 type 和 name 属性保留数据集和数据工厂之间的关系。 由于不能从模板中的位置推断 type，因此必须按以下格式提供完全限定的 type：`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`。

若要与数据工厂的实例建立父/子关系，请为包含父资源名称的数据集提供名称。 使用以下格式：`{parent-resource-name}/{child-resource-name}`。  

以下示例演示了如何实现：

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="next-steps"></a>后续步骤
* 若要了解有关模板区段的信息，请参阅[创作 Azure Resource Manager 模板](resource-group-authoring-templates.md)。
* 若要按顺序创建循环访问的资源，请参阅 [Azure Resource Manager 模板的顺序循环](resource-manager-sequential-loop.md)。
* 若要了解如何部署模板，请参阅[使用 Azure Resource Manager 模板部署应用程序](resource-group-template-deploy.md)。


