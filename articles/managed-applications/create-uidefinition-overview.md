---
title: CreateUiDefinition Azure 托管应用程序的创建体验 |Microsoft Docs
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
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 1ee6d9332a2be5ccb22b7571b348e2e0aae78fb2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563562"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition Azure 托管应用程序的创建体验

本文档介绍了在创建托管应用程序时 Azure 门户用来定义用户界面的**createUiDefinition**文件的核心概念。

模板如下所示

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

CreateUiDefinition 始终包含三个属性： 

* handler
* version
* 参数

处理程序应始终为`Microsoft.Azure.CreateUIDef`, 最新支持的版本为`0.1.2-preview`。

parameters 属性的架构取决于所指定的 handler 和 version 的组合。 对于托管应用程序，支持的属性为 `basics`、`steps` 和 `outputs`。 basics 和 steps 属性包含要在 Azure 门户中显示的[元素](create-uidefinition-elements.md)，例如文本框和下拉列表。 outputs 属性用来将指定元素的输出值映射到 Azure 资源管理器部署模板的参数。

建议包括 `$schema`，但这是可选的。 如果指定，则 `version` 的值必须与 `$schema` URI 中的版本匹配。

可以使用 JSON 编辑器创建 UI 定义, 然后在[Ui 定义沙盒](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)中对其进行测试以预览。 有关沙盒的详细信息, 请参阅[为 Azure 托管应用程序测试门户接口](test-createuidefinition.md)。

## <a name="basics"></a>基本

基础知识是 Azure 门户分析文件时生成的第一步。 除了会显示 `basics` 中指定的元素外，该门户还会为用户注入其他元素以用于为部署选择订阅、资源组和位置。 如果可能, 查询部署范围内参数的元素 (如群集或管理员凭据的名称) 应在此步骤中进行。

如果元素的行为取决于用户的订阅、资源组或位置, 则不能在基础上使用该元素。 例如，**Microsoft.Compute.SizeSelector** 需要依赖于用户的订阅和位置来确定可用大小的列表。 因此，**Microsoft.Compute.SizeSelector** 只能用于 steps 中。 通常，只有 **Microsoft.Common** 命名空间中的元素可以用于 basics 中。 尽管仍允许其他命名空间中的某些元素 (例如, 不依赖于用户的上下文)。

## <a name="steps"></a>步骤

steps 属性可以包含要在 basics 后显示的零个或多个其他步骤，每个步骤都包含一个或多个元素。 请考虑按所部署的应用程序的角色或层添加步骤。 例如, 为主节点输入添加一个步骤, 并为群集中的辅助角色节点添加一个步骤。

## <a name="outputs"></a>outputs

Azure 门户使用 `outputs` 属性来将 `basics` 和 `steps` 中的元素映射到 Azure 资源管理器部署模板的参数。 此字典中的键是模板参数的名称，值是所引用元素中的输出对象的属性。

若要设置托管应用程序资源名称，必须在 outputs 属性中包括名为 `applicationResourceName` 的值。 如果未设置此值, 应用程序将为名称指定一个 GUID。 可以在用户界面中包含一个文本框，用于向用户请求名称。

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>资源类型

若要仅将可用位置筛选为支持要部署的资源类型的位置, 请提供资源类型的数组。 如果提供了多个资源类型, 则仅返回支持所有资源类型的位置。 此属性是可选的。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>函数

CreateUiDefinition 提供了用于处理元素的输入和输出的[函数](create-uidefinition-functions.md), 以及条件等功能。 这些函数在语法和功能上类似于 Azure 资源管理器模板函数。

## <a name="next-steps"></a>后续步骤

createUiDefinition.json 文件本身具有一个简单的架构。 它的实际深度来自所有受支持的元素和函数。 在以下页中更详细地说明了这些项：

- [元素](create-uidefinition-elements.md)
- [函数](create-uidefinition-functions.md)

此处提供了 createUiDefinition 的当前 JSON 架构： https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json 。

有关用户界面文件示例，请参阅 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json)。
