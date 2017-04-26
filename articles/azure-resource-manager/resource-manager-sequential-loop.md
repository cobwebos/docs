---
title: "Azure 模板中的顺序循环 | Microsoft 文档"
description: "介绍在部署某种资源类型的多个实例时，如何扩展 Azure Resource Manager 模板功能以实现顺序循环。"
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/19/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>用于扩展 Azure Resource Manager 模板功能的模式 - 顺序循环

Azure Resource Manager 模板支持通过复制循环部署类似资源组。 资源对象中的复制循环可用于循环访问字符串数组，然后将这些字符串用于生成资源的唯一名称。 资源对象中的复制循环也可用于循环访问可描述资源的变量数组。

这些模式都有效，但是仅当每个组成员间无依赖关系时才有效。 迭代循环期间，Resource Manager 将尝试并行部署资源。 如果第一个资源依赖于第二个资源，那么在 Resource Manager 先部署第二个资源，然后部署第一个资源时，部署可能会失败。

真正的问题是，Resource Manager 目前不支持迭代循环内的 `dependsOn`。 但是，可使用现有的 Resource Manager 功能和某些创新资源命名来实现此功能。 

## <a name="sequential-looping-pattern"></a>顺序循环模式

该模式如下所示：命名第一个资源时，将名称前缀加上 `0` 或循环的第一个索引。 第二个资源包括一个复制循环，在该复制循环中，下一个资源名称是名称前缀加上 `copyIndex(1)` 函数的结果，该函数会将 1 添加到当前的 `copyIndex()`。 第二个资源换也包括一个 `dependsOn` 元素，它会引用名称前缀加上 `copyIndex()` 函数的结果。 此方法将创建一种从下一个资源到上一个资源的 `dependsOn` 关系。 Resource Manager 会等待部署下一个资源，直到上一个资源已部署完毕。

以下模板演示了这种模式。 Microsoft.Resources/deployments 资源类型只是一个嵌套模板，实际上不会部署任何内容。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
        }
      }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex(1))]",
      "dependsOn": [
        "[concat('loop-', copyIndex())]"
      ],
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
在此模板中，第一个资源对象名为 `loop-0`。 然后，在第二个资源对象中，下一个资源名称是单词 `loop-` 加上 `copyIndex(1)` 函数的结果，即：`loop-1`。 `dependsOn` 元素会引用上一个资源，因为它是单词 `loop-` 加上 `copyIndex()` 函数的结果，即：`loop-0`。 第二个资源对象中的模式会重复，直到到达 `count`，获得名为 `loop-4` 的 资源，它与 `loop-3` 具有 `dependsOn` 关系。 请注意，`count` 是一个变量，它从 `numberToDeploy` 参数中减去 `1`，使从零开始的计数保持正确。

## <a name="try-the-template"></a>尝试模板

如果要试验此模板，请按照下列步骤进行操作：

1.    转到 Azure 门户，选择“+”图标，然后搜索“模板部署”资源类型。 在搜索结果中找到后，请选择它。
2.    转到“模板部署”页后，请选择“创建”按钮。 转到“自定义部署”边栏选项卡后会看到模板并没有资源。
3.    选择“编辑”图标。
4.    删除右侧窗格中的空模板。
5.    将前面的示例模板复制粘贴到右侧窗格。
6.    单击“保存”按钮。
7.    返回到“自定义部署”窗格，但是这一次窗格中会包含一些下拉框。 选择你的订阅，或者创建新订阅或使用现有资源组，然后选择位置。 查看条款和条件，然后单击“我同意”按钮。
8.    选择“购买”按钮。

若要验证资源是否按顺序部署，请选择“资源组”，然后选择之前选择的资源组。 在“资源组”边栏选项卡中选择“部署”按钮，此时将显示按顺序部署的资源以及相应时间戳。

![在 Azure Resource Manager 中部署顺序循环模板](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>后续步骤

通过将资源添加到嵌套模板，在模板中使用此模式。 可以直接在 `Microsoft.Resources/deployments` 资源的模板元素中直接创作资源，也可使用 `templateLink` 元素链接到资源。 模板中的资源类型是一个嵌套模板，但是可部署任何资源类型。 唯一例外的是，不能从迭代循环内引用子资源。

* 若要了解如何创建一个资源的多个实例，请参阅[在 Azure Resource Manager 模板中部署多个资源实例](resource-group-create-multiple.md)。
* 此模式还可在[模板构建块项目](https://github.com/mspnp/template-building-blocks)和 [Azure 参考体系结构](/azure/architecture/reference-architectures/)中实现。
