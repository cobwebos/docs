---
title: 如何创建 Azure 资源管理器模板
description: 介绍创作 Azure 资源管理器模板的过程。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: bbe891aa584423d64531ae4b23bb8a6ead38c3da
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205555"
---
# <a name="create-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

本文介绍创建 Azure 资源管理器模板的过程和相关的选择。 其中提供了可帮助你创作模板的功能概述和示例。 本文假设你要将资源部署到资源组。 如果需要将资源部署到 Azure 订阅，例如，创建 Azure 策略或基于角色的访问控制分配，请参阅[为 Azure 订阅创建资源组和资源](deploy-to-subscription.md)。

## <a name="select-json-editor"></a>选择 JSON 编辑器

资源管理器模板是一个 JSON 文件。 需要有适当的创作工具才能处理 JSON 文件。 创作工具的选项很多，但是，如果你没有顺手的编辑器，请安装 [Visual Studio Code (VS Code)](https://code.visualstudio.com/)。 

安装 VS Code 后，添加 [Azure 资源管理器工具扩展](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)。 此扩展添加了许多功能，可以简化模板的创作。

![Visual Studio Code 中的模板](./media/how-to-create-template/template-visual-studio-code.png)

以下屏幕截图显示了在 Visual Studio Code 中打开的资源管理器模板。 

有关安装资源管理器工具扩展以及 VS Code 用法的教程，请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)。

## <a name="understand-the-template-structure"></a>了解模板结构

让我们查看模板的各个组成部分，以了解模板的工作原理。 你的模板可能不包含此处所示的每个节。 要关注的节包括：

* [parameters](resource-group-authoring-templates.md#parameters) 节：显示在部署期间可以指定的、用于自定义所要部署的基础结构的值。 

* [variables](resource-group-authoring-templates.md#variables) 节：显示在整个模板中使用的值。

* [functions](resource-group-authoring-templates.md#functions) 节：显示在模板中使用的自定义模板表达式。

* [resources](resource-group-authoring-templates.md#resources) 节：显示要部署到订阅的 Azure 资源。

* [outputs](resource-group-authoring-templates.md#outputs) 节：显示部署完成后返回的值。

## <a name="look-for-similar-templates"></a>查找类似的模板

通常，可以查找一个现有模板用于部署与所需解决方案类似的解决方案。 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)包含社区参与者提供的数百个模板。

![模板快速入门存储库](./media/how-to-create-template/template-quickstart-repo.png)

在该存储库中搜索类似于所需模板的模板。 如果该模板并不完全符合需要，没有关系，可以对它进行自定义。

找到所需的模板后，选择“在 Github 上浏览”，然后复制存储库中的 **azuredeploy.json** 文件。  在 VS Code 中，创建名为 **azuredeploy.json** 的新文件，并添加从快速入门存储库复制的模板文件的内容。

## <a name="add-resources"></a>添加资源

你可能想要自定义模板，以确保它完全符合需要。 首先，请查看已部署的资源。 可能需要在模板中添加、删除或更改资源。 有关资源的说明和语法，请参阅 [Azure 资源管理器模板参考](/azure/templates/)。

![模板参考](./media/how-to-create-template/template-reference.png)

查看这些属性后，进行任何所需的更改。 有关如何定义资源的建议，请参阅[资源 - 建议的做法](template-best-practices.md#resources)。

## <a name="add-or-remove-parameters"></a>添加或删除参数

你还可能需要调整模板的参数。 可以根据部署期间要完成的自定义工作量来添加或删除参数。 有关参数的用法建议，请参阅[参数 - 建议的做法](template-best-practices.md#parameters)。

## <a name="add-tags"></a>添加标记

可将标记添加到资源，以按类别进行逻辑组织，以及分割计费成本。 添加标记的过程非常简单，只需在资源的 JSON 中应用标记即可。 例如，以下存储帐户具有两个标记：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

也可以从参数动态应用标记。 有关详细信息，请参阅[模板中的标记](resource-group-using-tags.md#templates)。

## <a name="review-template-functions"></a>查看模板函数

你可能已注意到，模板中的表达式括在方括号中，例如 `"[some-expression]"`。 在部署期间，这些表达式使用模板函数来动态构造值。

例如，我们经常会看到如下所示的表达式：

```json
"name": "[parameters('siteName')]"
```

该表达式获取某个参数的值。 将值将赋给 name 属性。

或者，我们可能会看到使用多个函数的更复杂表达式，例如：

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

该表达式获取包含存储帐户属性的对象。

若要函数的作用，请查看[模板函数参考](resource-group-template-functions.md)文档。

## <a name="create-more-than-one-instance"></a>创建多个实例

有时，你想要创建某个资源的多个实例。 例如，需要多个存储帐户。 可以使用 `copy` 语法指定多个实例，而无需通过模板重复指定资源。

以下示例创建三个存储帐户：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

也可以通过参数动态指定实例数目。 有关详细信息，请参阅[在 Azure 资源管理器模板中部署资源或属性的多个实例](resource-group-create-multiple.md)。

## <a name="conditionally-deploy-a-resource"></a>按条件部署资源

有时，在部署期间，你需要指定是否要部署模板中的某个资源。 例如，你可能想要获得这种灵活性：既可以部署新的资源，也可以使用现有资源。 使用 `condition` 元素可以启用或禁用某个资源的部署。 如果 condition 元素中的表达式为 true，则部署该资源。 如果为 false，在部署期间将跳过该资源。

以下示例按条件部署某个存储帐户：

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

有关详细信息，请参阅 [condition 元素](resource-group-authoring-templates.md#condition)。

## <a name="review-dependencies"></a>查看依赖项

模板中的某些资源需要先于其他资源进行部署。 例如，在创建 SQL 数据库之前，SQL 服务器必须事先存在。 如果使用 [reference 函数](resource-group-template-functions-resource.md#reference)，则资源管理器可以隐式确定资源的部署顺序。 但是，在某些情况下，需要使用 `dependsOn` 元素显式定义依赖项。 请查看模板，以确定是否需要添加任何依赖项。 请注意不要添加不必要的依赖项，因为它们可能会减慢部署速度，或产生循环引用。

下图显示了各种应用服务资源的依赖顺序：

![Web 应用依赖项](./media/how-to-create-template/web-dependencies.png)

以下示例演示了用于定义依赖项的模板部分。

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

有关详细信息，请参阅[定义 Azure 资源管理器模板中部署资源的顺序](resource-group-define-dependencies.md)。

## <a name="review-recommended-practices"></a>查看建议的做法

在部署模板之前，请查看 [Azure 资源管理器模板最佳做法](template-best-practices.md)，以确定是否需要在模板中实施任何建议的做法。

如果需要在不同的 Azure 云环境中使用模板，请参阅[根据云中一致性开发 Azure 资源管理器模板](templates-cloud-consistency.md)。

## <a name="next-steps"></a>后续步骤

* 若要部署模板，请参阅[使用 Azure CLI 进行部署](resource-group-template-deploy-cli.md)或[使用 PowerShell 进行部署](resource-group-template-deploy.md)。
* 有关创建模板的循序渐进快速入门，请参阅[使用 Visual Studio Code 创建 Azure 资源管理器模板](resource-manager-quickstart-create-templates-use-visual-studio-code.md)。
* 有关模板的可用函数列表，请参阅[模板函数](resource-group-template-functions.md)。
