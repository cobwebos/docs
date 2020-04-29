---
title: 用于门户窗格的 CreateUiDefinition.json 文件
description: 介绍如何为 Azure 门户创建用户界面定义。 定义 Azure 托管应用程序时使用。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294890"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>适合 Azure 托管应用程序的创建体验的 CreateUiDefinition.json

本文档介绍 **createUiDefinition.json** 文件的核心概念，Azure 门户在创建托管应用程序时使用该文件定义用户界面。

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

* 处理程序 (handler)
* 版本
* parameters

handler 应当始终为 `Microsoft.Azure.CreateUIDef`，支持的最新版本为 `0.1.2-preview`。

parameters 属性的架构取决于所指定的 handler 和 version 的组合。 对于托管应用程序，支持的属性为 `basics`、`steps` 和 `outputs`。 basics 和 steps 属性包含要在 Azure 门户中显示的[元素](create-uidefinition-elements.md)，例如文本框和下拉列表。 outputs 属性用来将指定元素的输出值映射到 Azure 资源管理器部署模板的参数。

建议包括 `$schema`，但这是可选的。 如果指定，则 `version` 的值必须与 `$schema` URI 中的版本匹配。

可以使用 JSON 编辑器创建 createUiDefinition，然后在 [createUiDefinition 沙盒](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)中对其进行测试，以便预览它。 有关沙盒的详细信息，请参阅[测试 Azure 托管应用程序的门户接口](test-createuidefinition.md)。

## <a name="basics"></a>基础

基础知识是 Azure 门户分析文件时生成的第一步。 除了会显示 `basics` 中指定的元素外，该门户还会为用户注入其他元素以用于为部署选择订阅、资源组和位置。 可能情况下，对部署范围内的参数进行查询的元素（例如群集名称或管理员凭据）应当放在此步骤中。

## <a name="steps"></a>步骤

steps 属性可以包含要在 basics 后显示的零个或多个其他步骤，每个步骤都包含一个或多个元素。 请考虑按所部署的应用程序的角色或层添加步骤。 例如，针对群集中的主节点输入添加一个步骤，针对辅助角色节点添加另一个步骤。

## <a name="outputs"></a>Outputs

Azure 门户使用 `outputs` 属性来将 `basics` 和 `steps` 中的元素映射到 Azure 资源管理器部署模板的参数。 此字典中的键是模板参数的名称，值是所引用元素中的输出对象的属性。

若要设置托管应用程序资源名称，必须在 outputs 属性中包括名为 `applicationResourceName` 的值。 如果未设置此值，应用程序会为名称分配 GUID。 可以在用户界面中包含一个文本框，用于向用户请求名称。

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

若要筛选可用位置（仅可用于那些支持要部署的资源类型的位置），请提供资源类型的数组。 如果提供了多个资源类型，则仅返回支持所有资源类型的位置。 此属性是可选的。

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

CreateUiDefinition 提供了用于处理元素的输入和输出的[函数](create-uidefinition-functions.md)，以及条件语句等功能。 这些函数在语法和功能上类似于 Azure 资源管理器模板函数。

## <a name="next-steps"></a>后续步骤

createUiDefinition.json 文件本身具有一个简单的架构。 它的实际深度来自所有受支持的元素和函数。 在以下页中更详细地说明了这些项：

- [元素](create-uidefinition-elements.md)
- [函数](create-uidefinition-functions.md)

此处提供了 createUiDefinition 的当前 JSON 架构： `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` 。

有关用户界面文件示例，请参阅 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)。
