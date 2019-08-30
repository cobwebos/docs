---
title: Azure 资源管理器模板
description: 介绍如何使用 Azure 资源管理器模板来部署资源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 95c127b3a7c9c47c96b292066bf1597a02896806
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166519"
---
# <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

借助 Azure 资源管理器, 你可以创建模板来定义要部署到 Azure 的内容。 该模板是一个 JavaScript 对象表示法 (JSON) 文件, 其中包含 Azure 解决方案的基础结构和配置。 使用模板，可以在解决方案的整个生命周期内重复部署该解决方案，确保以一致的状态部署资源。

该模板使用声明性语法, 这使你可以陈述打算部署的内容, 而无需编写编程命令序列来创建它。 在模板中, 指定要部署的资源以及这些资源的属性。

## <a name="benefits-of-resource-manager-templates"></a>资源管理器模板的优点

资源管理器模板具有以下优点:

* 可以以组的形式部署、管理和监视解决方案的所有资源，而不是单独处理这些资源。

* 可以在整个开发生命周期内重复部署解决方案，并确保以一致的状态部署资源。

* 可以通过声明性模板而非脚本来管理基础结构。

* 可以定义各资源之间的依赖关系，使其按正确的顺序进行部署。

以下建议将帮助你在使用解决方案时充分利用 Resource Manager。

* 通过 Resource Manager 模板中的声明性语法而不是强制性的命令来定义和部署基础结构。

* 在模板中定义所有部署和配置步骤。 在设置解决方案时不应执行手动步骤。

* 运行强制性命令来管理资源，例如启动或停止应用或计算机。

* 遵循[Azure 资源管理器模板最佳实践](template-best-practices.md)。

## <a name="template-deployment-process"></a>模板部署进程

部署模板时, 资源管理器分析模板并将其语法转换为 REST API 操作。 它会将这些操作发送到相应的资源提供程序。 例如，当 Resource Manager 收到具有以下资源定义的模板：

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

它将定义转换为以下 REST API 操作，后者将发送到 Microsoft.Storage 资源提供程序：

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-structure"></a>模板结构

模板和资源组的定义方式完全取决于用户及其所需的解决方案管理方式。 例如，可以通过单个模板在单个资源组中部署三层式应用程序。

![三层模板](./media/resource-group-overview/3-tier-template.png)

但无需在单个模板中定义整个基础结构。 通常，合理的做法是将部署要求划分成一组有针对性的模板。 可以轻松地将这些模板重复用于不同的解决方案。 若要部署特定的解决方案，请创建链接所有所需模板的主模板。 下图显示了如何通过包含三个嵌套模板的父模板部署三层解决方案。

![嵌套层模板](./media/resource-group-overview/nested-tiers-template.png)

要各层具有单独的生命周期，可将三个层部署到单独的资源组。 请注意，资源仍可链接到其他资源组中的资源。

![层模板](./media/resource-group-overview/tier-templates.png)

有关嵌套模板的信息，请参阅[将链接的模板用于 Azure 资源管理器](resource-group-linked-templates.md)。

Azure 资源管理器会分析依赖关系，以确保按正确的顺序创建资源。 如果一个资源依赖于另一个资源（例如虚拟机需要存储帐户才能访问磁盘）中的值，请设置依赖关系。 有关详细信息，请参阅[在 Azure 资源管理器模板中定义依赖关系](resource-group-define-dependencies.md)。

还可以使用模板对基础结构进行更新。 例如，可以将新的资源添加到应用程序，并为已部署的资源添加配置规则。 如果模板定义的资源已存在，则资源管理器会更新现有资源而不会创建新资源。

如果需要其他操作（例如，安装未包含在安装程序中的特定软件）时，资源管理器可提供所需的扩展。 如果已在使用配置管理服务（如 DSC、Chef 或 Puppet），则可以使用扩展来继续处理该服务。 有关虚拟机扩展的信息，请参阅[关于虚拟机扩展和功能](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

从门户创建解决方案时，该解决方案会自动包含部署模板。 无需从头开始创建模板，因为可以从解决方案的模板着手，并根据特定需求自定义该模板。 有关示例，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-the-portal.md)。 还可以通过导出资源组的当前状态或查看特定部署所用的模板来检索现有资源组的模板。 查看[导出的模板](./manage-resource-groups-portal.md#export-resource-groups-to-templates)是了解模板语法的有用方法。

最后，该模板将成为应用程序源代码的一部分。 可以将它签入源代码存储库，并随着应用程序的发展更新该模板。 可以通过 Visual Studio 编辑模板。

## <a name="next-steps"></a>后续步骤

有关模板文件的详细信息, 请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。

定义模板后，即可将资源部署到 Azure。 若要部署资源，请参阅：

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](resource-group-template-deploy.md)
* [使用 Resource Manager 模板和 Azure CLI 部署资源](resource-group-template-deploy-cli.md)
* [使用 Resource Manager 模板和 Azure 门户部署资源](resource-group-template-deploy-portal.md)
* [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](resource-group-template-deploy-rest.md)

