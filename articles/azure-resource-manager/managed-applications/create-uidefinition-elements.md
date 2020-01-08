---
title: 创建 UI 定义元素
description: 介绍了为 Azure 门户构造 UI 定义时要使用的元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 0ceb8f5762bb6bc987757845426a0f2b380264f1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650664"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition 元素

本文介绍了 CreateUiDefinition 的所有受支持元素的架构和属性。 

## <a name="schema"></a>架构

大多数元素的架构如下所示：

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| 属性 | 需要 | Description |
| -------- | -------- | ----------- |
| name | 是 | 一个内部标识符，用于引用元素的特定实例。 元素名称最常用于 `outputs` 中，其中指定元素的输出值映射到模板的参数。 还可以使用它将元素的输出值绑定到其他元素的 `defaultValue`。 |
| type | 是 | 要为元素呈现的 UI 控件。 有关支持的类型的列表，请参阅[元素](#elements)。 |
| label | 是 | 元素的显示文本。 某些元素类型包含多个标签，因此，值可能是包含多个字符串的对象。 |
| defaultValue | 否 | 元素的默认值。 某些元素类型支持复杂的默认值，因此，值可能是对象。 |
| toolTip | 否 | 要在元素的工具提示中显示的文本。 与 `label` 类似，某些元素支持多个工具提示字符串。 可以使用 Markdown 语法嵌入内联链接。
| constraints | 否 | 用来自定义元素的验证行为的一个或多个属性。 constraints 支持的属性因元素类型而异。 某些元素类型不支持自定义验证行为，因此没有 constraints 属性。 |
| 选项 | 否 | 用于自定义元素行为的其他属性。 与 `constraints` 类似，支持的属性因元素类型而异。 |
| visible | 否 | 指示是否显示此元素。 如果为 `true`，则会显示此元素及其相应的子元素。 默认值是 `true`。 可使用[逻辑函数](create-uidefinition-functions.md#logical-functions)动态控制此属性的值。

## <a name="elements"></a>元素

每个元素的文档都包含此元素的 UI 示例、架构、行为备注（通常涉及验证和支持的自定义）以及示例输出。

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>后续步骤

有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
