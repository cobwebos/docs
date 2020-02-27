---
title: 部署多个资源实例
description: 使用 Azure 资源管理器模板中的复制操作和数组多次部署资源类型。
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e90673504ceaccdc25a477e856defa77eed37d86
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620223"
---
# <a name="resource-iteration-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的资源迭代

本文介绍如何在 Azure 资源管理器模板中创建多个资源实例。 通过将**copy**元素添加到模板的 resources 节，可以动态设置要部署的资源数。 还应避免重复模板语法。

你还可以使用具有[属性](copy-properties.md)、[变量](copy-variables.md)和[输出](copy-outputs.md)的副本。

如需指定究竟是否部署资源，请参阅 [condition 元素](conditional-resource-deployment.md)。

## <a name="resource-iteration"></a>资源迭代

Copy 元素具有以下常规格式：

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

**Name**属性是标识循环的任何值。 **Count**属性指定资源类型所需的迭代数。

使用**mode**和**batchSize**属性来指定是以并行方式还是按顺序部署资源。 这些属性在[串行或并行](#serial-or-parallel)中进行了介绍。

以下示例创建在**storageCount**参数中指定的存储帐户的数目。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

请注意，每个资源的名称都包括 `copyIndex()` 函数，用于返回循环中的当前迭代。 `copyIndex()` 从零开始。 因此，以下示例：

```json
"name": "[concat('storage', copyIndex())]",
```

将创建以下名称：

* storage0
* storage1
* storage2。

若要偏移索引值，可以在 copyIndex() 函数中传递一个值。 迭代数仍在 copy 元素中指定，但 copyIndex 的值为 offset 指定的值。 因此，以下示例：

```json
"name": "[concat('storage', copyIndex(1))]",
```

将创建以下名称：

* storage1
* storage2
* storage3

当使用数组时，copy 操作十分有用，因为这样可以迭代数组中的每个元素。 可以对数组使用 `length` 函数来指定迭代计数，并使用 `copyIndex` 来检索数组中的当前索引。

下面的示例为参数中提供的每个名称创建一个存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

如果要从已部署的资源返回值，可以使用["输出" 部分中的 "复制"](copy-outputs.md)。

## <a name="serial-or-parallel"></a>串行或并行

默认情况下，资源管理器将并行创建资源。 它不限制并行部署的资源数，而不是模板中800资源的总限制。 不会保证它们的创建顺序。

但是，可能需要指定按顺序部署资源。 例如，在更新生产环境时，可能需要错开更新，使得任何一次仅更新一定数量。 若要按顺序部署多个资源实例，请将 `mode` 设置为“串行”，并将  **设置为一次要部署的实例数量**`batchSize`。 在串行模式下，资源管理器会在循环中创建早前实例的依赖项，以便在前一个批处理完成之前它不会启动一个批处理。

例如，若要按顺序一次部署两个存储帐户，请使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

mode 属性也接受 **parallel**（它是默认值）。

## <a name="depend-on-resources-in-a-loop"></a>依赖于循环中的资源

然后使用 `dependsOn` 元素指定部署一个资源后再部署另一个资源。 若要部署的资源依赖于循环中的资源集合，请在 dependsOn 元素中提供 copy 循环的名称。 以下示例演示了如何在部署虚拟机之前部署三个存储帐户。 未显示完整的虚拟机定义。 请注意，copy 元素的名称设置为 `storagecopy`，而虚拟机的 dependsOn 元素也设置为 `storagecopy`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>子资源的迭代

不能对子资源使用 copy 循环。 要创建通常定义为嵌套在另一个资源中的资源的多个实例，必须将该资源创建为顶级资源。 通过 type 和 name 属性定义与父资源的关系。

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
  ]
```

若要创建多个数据集，请将其移出数据工厂。 数据集必须与数据工厂处于同一级别，但它仍是数据工厂的子资源。 通过 type 和 name 属性保留数据集和数据工厂之间的关系。 由于不能从模板中的位置推断 type，因此必须按以下格式提供完全限定的 type：`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`。

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
  },
  ...
}]
```

## <a name="copy-limits"></a>复制限制

计数不能超过800。

计数不能为负数。 如果部署 Azure PowerShell 2.6 或更高版本的模板，Azure CLI 2.0.74 或更高版本，或者 REST API 版本**2019-05-10**或更高版本，则可以将 count 设置为零。 PowerShell、CLI 和 REST API 的早期版本不支持计数为零。

使用复制的[完整模式部署](deployment-modes.md)时请小心。 如果使用完整模式重新部署到资源组，则会删除在解析复制循环后未在模板中指定的任何资源。

## <a name="example-templates"></a>示例模板

以下示例展示了创建资源或属性的多个实例的常见方案。

|模板  |说明  |
|---------|---------|
|[复制存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |部署名称中带索引号的多个存储帐户。 |
|[串行的复制存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |一次部署多个存储帐户。 名称中包含索引号。 |
|[复制具有数组的存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |部署多个存储帐户。 名称中包含数组中的值。 |
|[部署数据磁盘数量不定的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |通过虚拟机部署多个数据磁盘。 |
|[多个安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |将多个安全规则部署到网络安全组。 这会从参数构造安全规则。 有关参数，请参阅[多个 NSG 参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>后续步骤

* 要查看教程，请参阅[教程：使用资源管理器模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关 copy 元素的其他用法，请参阅：
  * [Azure 资源管理器模板中的属性迭代](copy-properties.md)
  * [Azure 资源管理器模板中的变量迭代](copy-variables.md)
  * [Azure 资源管理器模板中的输出迭代](copy-outputs.md)
* 有关将 copy 与嵌套模板结合使用的信息，请参阅[使用副本](linked-templates.md#using-copy)。
* 若要了解有关模板区段的信息，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 若要了解如何部署模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

