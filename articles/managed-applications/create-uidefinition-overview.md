---
title: 了解如何为 Azure 托管应用程序创建 UI 定义 | Microsoft Docs
description: 介绍了如何为 Azure 托管应用程序创建 UI 定义
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 59003e71324f5342cb2b724f670603fd6b67afe4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305219"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>为托管应用程序创建 Azure 门户用户界面
本文档介绍 createUiDefinition.json 文件的核心概念。 Azure 门户使用此文件生成用于创建托管应用程序的用户界面。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition 始终包含三个属性： 

* handler
* version
* parameters

对于托管应用程序，handler 应当始终为 `Microsoft.Compute.MultiVm`，支持的最新版本为 `0.1.2-preview`。

parameters 属性的架构取决于所指定的 handler 和 version 的组合。 对于托管应用程序，支持的属性为 `basics`、`steps` 和 `outputs`。 basics 和 steps 属性包含要在 Azure 门户中显示的_元素_，例如文本框和下拉列表。 outputs 属性用来将指定元素的输出值映射到 Azure 资源管理器部署模板的参数。

建议包括 `$schema`，但这是可选的。 如果指定，则 `version` 的值必须与 `$schema` URI 中的版本匹配。

## <a name="basics"></a>Basics
基础步骤始终是 Azure 门户在分析文件时生成的向导的第一个步骤。 除了会显示 `basics` 中指定的元素外，该门户还会为用户注入其他元素以用于为部署选择订阅、资源组和位置。 通常，对部署范围内的参数进行查询的元素（例如群集名称或管理员凭据）应当放在此步骤中。

如果元素的行为依赖于用户的订阅、资源组或位置，则不能在 basics 中使用该元素。 例如，**Microsoft.Compute.SizeSelector** 需要依赖于用户的订阅和位置来确定可用大小的列表。 因此，**Microsoft.Compute.SizeSelector** 只能用于 steps 中。 通常，只有 **Microsoft.Common** 命名空间中的元素可以用于 basics 中。 但是也允许其他命名空间中不依赖于用户上下文的某些元素（例如 **Microsoft.Compute.Credentials**）。

## <a name="steps"></a>Steps
steps 属性可以包含要在 basics 后显示的零个或多个其他步骤，每个步骤都包含一个或多个元素。 请考虑按所部署的应用程序的角色或层添加步骤。 例如，针对群集中的主节点添加一个用于输入的步骤，针对辅助角色节点添加另一个步骤。

## <a name="outputs"></a>Outputs
Azure 门户使用 `outputs` 属性来将 `basics` 和 `steps` 中的元素映射到 Azure 资源管理器部署模板的参数。 此字典中的键是模板参数的名称，值是所引用元素中的输出对象的属性。

若要设置托管应用程序资源名称，必须在 outputs 属性中包括名为 `applicationResourceName` 的值。 如果未设置此值，应用程序将为名称分配 GUID。 可以在用户界面中包含一个文本框，用于向用户请求名称。

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>函数
与 Azure 资源管理器中的模板函数类似（在语法和功能方面），CreateUiDefinition 提供了用于处理元素的输入和输出的函数，以及条件语句等诸多功能。

## <a name="next-steps"></a>后续步骤
createUiDefinition.json 文件本身具有一个简单的架构。 它的实际深度来自所有受支持的元素和函数。 在以下页中更详细地说明了这些项：

- [元素](create-uidefinition-elements.md)
- [函数](create-uidefinition-functions.md)

此处提供了 createUiDefinition 的当前 JSON 架构：https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json。

有关用户界面文件示例，请参阅 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json)。
