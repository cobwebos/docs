---
title: "在 Azure 模板中定义子资源 | Microsoft 文档"
description: "说明如何在 Azure Resource Manager 模板中设置子资源的资源类型和名称"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>在 Resource Manager 模板中设置子资源的名称和类型
创建模板时，通常需要包括与父资源相关的子资源。 例如，模板可以包括 SQL Server 和数据库。 SQL Server 是父资源，数据库是子资源。 

子资源类型的格式为：`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

子资源名称的格式为：`{parent-resource-name}/{child-resource-name}`

但是，可以根据它是嵌套在父资源中，还是独自在最高级别，以不同方式在模板中指定类型和名称。 本主题演示如何处理这两种方法。

向资源构造完全限定的引用时，类型和名称的分段组合顺序并不是这两者的简单串联。  相反，在命名空间后，需采用“类型/名称”对从最不具体到最具体的序列：

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

例如：

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` 正确，`Microsoft.Compute/virtualMachines/extensions/myVM/myExt` 不正确

## <a name="nested-child-resource"></a>嵌套的子资源
要定义子资源，最简单的方式是将其嵌套在父资源中。 以下示例演示了 SQL Server 中嵌套的 SQL 数据库。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

对于子资源，类型设置为 `databases`，但其完整资源类型是 `Microsoft.Sql/servers/databases`。 可不提供 `Microsoft.Sql/servers/`，因为假设它继承父资源类型。 子资源名称设置为 `exampledatabase`，但完整名称包括父名称。 可不提供 `exampleserver`，因为假设它继承父资源。

## <a name="top-level-child-resource"></a>顶级子资源
可以定义顶级子资源。 如果父资源未部署在同一模板中，或者想要使用 `copy` 创建多个子资源，可以使用此方法。 使用此方法时，必须提供完整的资源类型，并在子资源名称中包括父资源名称。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

数据库是服务器的子资源，即使它们在模板中的相同级别上定义。

## <a name="next-steps"></a>后续步骤
* 有关如何创建模板的建议，请参阅 [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md)（创建 Azure Resource Manager 模板的最佳做法）。
* 有关创建多个子资源的示例，请参阅[在 Azure Resource Manager 模板中部署多个资源实例](resource-group-create-multiple.md)。
