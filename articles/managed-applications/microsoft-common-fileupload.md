---
title: Azure FileUpload UI 元素 | Microsoft Docs
description: 介绍了 Azure 门户的 Microsoft.Common.FileUpload UI 元素。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: bfde11a189348472942248a6b90fd5ef1b5a1c89
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI 元素
一个允许用户指定要上传的一个或多个文件的控件。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>备注
- `constraints.accept` 指定在浏览器的文件对话框中显示的文件类型。 有关允许的值，请参阅 [HTML5 规范](http://www.w3.org/TR/html5/forms.html#attr-input-accept)。 默认值为 **null**。
- 如果 `options.multiple` 设置为 **true**，则会允许用户在浏览器的文件对话框中选择多个文件。 默认值为 **false**。
- 此元素支持基于 `options.uploadMode` 的值以两种模式上传文件。 如果指定了 **file**，则输出会以 blob 形式包含文件的内容。 如果指定了 **url**，则文件会被上传到一个临时位置，并且输出会包含 blob 的 URL。 临时 blob 在 24 小时后会被清除。 默认值为 **file**。
- `options.openMode` 的值决定了如何读取文件。 如果文件应当为纯文本，请指定 **text**；否则，请指定 **binary**。 默认值为 **text**。
- 如果 `options.uploadMode` 设置为 **file** 并且 `options.openMode` 设置为 **binary**，则输出将是 base64 编码的。
- `options.encoding` 指定在读取文件时要使用的编码。 默认值为 **UTF-8**，并且仅当 `options.openMode` 设置为 **text** 时使用。

## <a name="sample-output"></a>示例输出
如果 options.multiple 为 false 并且 options.uploadMode 为 file，则输出会以 JSON 字符串形式包含文件的内容：

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

如果 options.multiple 为 true 并且 options.uploadMode 为 file，则输出会以 JSON 数组形式包含文件的内容：

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

如果 options.multiple 为 false 并且 options.uploadMode 为 url，则输出会以 JSON 字符串形式包含 URL：

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

如果 options.multiple 为 true 并且 options.uploadMode 为 url，则输出会以 JSON 数组形式包含 URL 列表：
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

测试 CreateUiDefinition 时，某些浏览器（例如 Google Chrome）在浏览器控制台中会截断由 Microsoft.Common.FileUpload 元素生成的 URL。 可能需要右键单击各个链接来复制完整 URL。


## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
