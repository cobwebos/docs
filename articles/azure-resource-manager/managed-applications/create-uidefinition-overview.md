---
title: 用于门户窗格的 CreateUiDefinition.json 文件
description: 介绍如何为 Azure 门户创建用户界面定义。 定义 Azure 托管应用程序时使用。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 327fa1d7eb73d8e65bb4f81c1dff0fe2bec2913b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89319555"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>适合 Azure 托管应用程序的创建体验的 CreateUiDefinition.json

本文档介绍 createUiDefinition.json 文件的核心概念。 创建托管应用程序时，Azure 门户使用此文件来定义用户界面。

模板如下所示

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

`CreateUiDefinition` 始终包含三个属性：

* 处理程序 (handler)
* 版本
* parameters

handler 应当始终为 `Microsoft.Azure.CreateUIDef`，支持的最新版本为 `0.1.2-preview`。

parameters 属性的架构取决于所指定的 handler 和 version 的组合。 对于托管应用程序，支持的属性为 `config`、`basics``steps` 和 `outputs`。 仅当需要重写 `basics` 步骤的默认行为时，才使用 `config`。 basics 和 steps 属性包含要在 Azure 门户中显示的[元素](create-uidefinition-elements.md)，例如文本框和下拉列表。 outputs 属性用来将指定元素的输出值映射到 Azure 资源管理器模板的参数。

建议包括 `$schema`，但这是可选的。 如果指定，则 `version` 的值必须与 `$schema` URI 中的版本匹配。

可以使用 JSON 编辑器创建 createUiDefinition，然后在 [createUiDefinition 沙盒](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)中对其进行测试，以便预览它。 有关沙盒的详细信息，请参阅[测试 Azure 托管应用程序的门户接口](test-createuidefinition.md)。

## <a name="config"></a>Config

`config` 属性为可选。 使用它可以重写基本步骤的默认行为，也可以将界面设置为分步向导。 如果使用 `config`，则它是 **createUiDefinition.json** 文件的 `parameters` 节中的第一个属性。 以下示例显示可用的属性。

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

### <a name="wizard"></a>向导

`isWizard` 属性使你能够在继续下一步之前要求对每个步骤进行成功的验证。 如果未指定 `isWizard` 属性，则默认值为 **false**，不需要逐步验证。

启用了 `isWizard` 时，将设置为 **true**，“基本信息”选项卡可用，所有其他选项卡被禁用。 选择“下一步”按钮时，该选项卡的图标指示选项卡的验证是通过还是失败。 完成并验证某个选项卡的必填字段之后，即可通过“下一步”按钮导航到下一个选项卡。所有选项卡都通过验证后，你可以进入“查看并创建”页面，选择“创建”按钮开始进行部署。

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="选项卡向导":::

### <a name="override-basics"></a>重写基本信息

基本信息配置允许你自定义基本信息步骤。

对于 `description`，请提供启用了 markdown 的字符串，用于描述资源。 支持多行格式和链接。

通过 `subscription` 和 `resourceGroup` 元素，可以指定其他验证。 用于指定验证的语法与[文本框](microsoft-common-textbox.md)自定义验证的语法相同。 还可以在订阅或资源组上指定 `permission` 验证。  

订阅控件接受资源提供程序命名空间的列表。 例如，可以指定 Microsoft.Compute。 当用户选择了不支持资源提供程序的订阅时，会显示错误消息。 如果资源提供程序未在该订阅上注册，并且用户没有注册资源提供程序的权限，则会发生此错误。  

资源组控件有一个 `allowExisting` 的选项。 为 `true` 时，用户可以选择已含有资源的资源组。 此标志最适用于解决方案模板，其默认行为要求用户必须选择新的或空的资源组。 在大多数其他情况下，不需要指定该属性。  

对于 `location`，指定希望重写的位置控件的属性。 任何未重写的属性均设置为其默认值。 `resourceTypes` 接受包含完全限定资源类型名称的字符串数组。 位置选项限为仅支持该资源类型的区域。 `allowedValues`  接受区域字符串的数组。 下拉列表中仅显示这些区域。 可以同时设置 `allowedValues` 和 `resourceTypes`。 结果是两个列表的交集。 最后，可使用 `visible` 属性有条件地或完全禁用位置下拉列表。  

## <a name="basics"></a>基础

“基本信息”步骤是 Azure 门户分析文件时生成的第一步。 默认情况下，通过“基本信息”步骤，用户可选择订阅、资源组和部署位置。

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="选项卡向导":::

在本部分中可以添加更多元素。 在可能的情况下，请添加可查询部署范围内的参数的元素（例如群集名称或管理员凭据）。

以下示例展示添加到默认元素中的文本框。

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>步骤

步骤属性包含零个或多个在基本信息步骤后显示的其他步骤。 每个步骤包含一个或多个元素。 请考虑按所部署的应用程序的角色或层添加步骤。 例如，针对群集中的主节点输入添加一个步骤，针对辅助角色节点添加另一个步骤。

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>输出

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

此处提供了 createUiDefinition 的当前 JSON 架构：`https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`。

有关用户界面文件示例，请参阅 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)。
