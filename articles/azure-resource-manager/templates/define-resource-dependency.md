---
title: 设置资源的部署顺序
description: 介绍如何在部署期间将一个资源设置为依赖于另一个资源，以确保按正确的顺序部署资源。
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: ffd6d6c65a1cbe9578b5f9162d29f3238e27ea71
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207683"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>定义 Azure 资源管理器模板中部署资源的顺序

部署资源时，可能需要在部署资源之前确保其他资源存在。 例如，在部署 SQL 数据库之前，需要 SQL server。 可通过将一个资源标记为依赖于其他资源来定义此关系。 使用 **dependsOn** 元素或 **reference** 函数定义依赖项。

Resource Manager 将评估资源之间的依赖关系，并根据其依赖顺序进行部署。 如果资源互不依赖，资源管理器将以并行方式部署资源。 只需为在同一模板中部署的资源定义依赖关系。

## <a name="dependson"></a>dependsOn

在模板中，使用 dependsOn 元素可将一个资源定义为与一个或多个资源相依赖。 它的值是一个以逗号分隔的资源名称列表。 此列表可以包含有条件地[部署](conditional-resource-deployment.md)的资源。 当条件资源未部署时，Azure 资源管理器会自动从所需的依赖项中将其删除。

以下示例显示了一个虚拟机规模集，该集依赖于负载均衡器、虚拟网络以及创建多个存储帐户的循环。 下面的示例中未显示其他这些资源，但它们需要存在于模板的其他位置。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

在前面的示例中，通过名为 **storageLoop** 的复制循环创建的资源上包含一个依赖关系。 有关示例，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。

定义依赖关系时，可以包括资源提供程序命名空间和资源类型，以避免模糊。 例如，为明确表示可能与其他资源同名的负载均衡器和虚拟网络，可使用以下格式：

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

尽管你可能倾向使用 dependsOn 来映射资源之间的关系，但请务必了解这么做的理由。 例如，若要记录资源的互连方式，那么，dependsOn 方法并不合适。 部署之后，你无法查询 dependsOn 元素中定义了哪些资源。 使用 dependsOn 可能会影响部署时间，因为资源管理器不会并行部署两个具有依赖关系的资源。

## <a name="child-resources"></a>子资源

资源属性允许指定与所定义的资源相关的子资源。 子资源总共只能定义五级。 请务必注意，不会在子资源和父资源之间创建隐式部署依赖关系。 如果要在父级资源后部署子资源，则必须使用 dependsOn 属性明确声明该依赖关系。

每个父资源仅接受特定的资源类型作为子资源。 可接受的资源类型在父资源的[模板架构](https://github.com/Azure/azure-resource-manager-schemas)中指定。 子资源类型的名称包含父资源类型的名称，例如 **Microsoft.Web/sites/config** 和 **Microsoft.Web/sites/extensions** 都是 **Microsoft.Web/sites** 的子资源。

以下示例演示了 SQL 服务器和 SQL 数据库。 请注意，在 SQL 数据库与 SQL 服务器之间定义了显式依赖关系，尽管数据库是服务器的子级。

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>reference 和 list 函数

[引用函数](template-functions-resource.md#reference)使表达式能够从其他 JSON 名值对或运行时资源中派生其值。 [list* 函数](template-functions-resource.md#list)从列表操作返回资源的值。  当引用的资源部署位于同一模板中并通过其名称（而不是资源 ID）引用时，reference 和 list 表达式隐式声明一个资源依赖于另一个资源。 如果将资源 ID 传入到 reference 或 list 函数中，则不会创建隐式引用。

reference 函数的一般格式为：

```json
reference('resourceName').propertyPath
```

listKeys 函数的一般格式为：

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

在以下示例中，CDN 终结点显式依赖于 CDN 配置文件，隐式依赖于 Web 应用。

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

可以使用此元素或 dependsOn 元素来指定依赖关系，但不需要同时将它们用于同一依赖资源。 只要可能，可使用隐式引用以避免添加不必要的依赖项。

若要了解详细信息，请参阅[引用函数](template-functions-resource.md#reference)。

## <a name="circular-dependencies"></a>循环依赖项

Resource Manager 可在模板验证过程中确定循环依赖项。 如果收到的错误指出存在循环依赖关系，请评估模板，了解是否存在不需要且可删除的任何依赖关系。 如果删除依赖关系不起作用，则可将一些部署操作移至在具有循环依赖关系的资源后部署的子资源中，来避免循环依赖关系。 例如，假设要部署两个虚拟机，但必须在每个虚拟机上设置引用另一虚拟机的属性。 可以按下述顺序部署这两个虚拟机：

1. vm1
2. vm2
3. vm1 上的扩展依赖于 vm1 和 vm2。 扩展在 vm1 上设置的值是从 vm2 获取的。
4. vm2 上的扩展依赖于 vm1 和 vm2。 扩展在 vm2 上设置的值是从 vm1 获取的。

有关评估部署顺序和解决依赖项错误的信息，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](common-deployment-errors.md)。

## <a name="next-steps"></a>后续步骤

* 相关教程，请参阅[教程：使用从属资源创建 Azure 资源管理器模板](template-tutorial-create-templates-with-dependent-resources.md)。
* 有关设置依赖项的建议，请参阅 [Azure 资源管理器模板的最佳做法](template-best-practices.md)。
* 若要了解如何在部署期间排查依赖项故障，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](common-deployment-errors.md)。
* 若要了解有关创建 Azure 资源管理器模板的信息，请参阅[创作模板](template-syntax.md)。
* 有关模板的可用函数列表，请参阅[模板函数](template-functions.md)。

