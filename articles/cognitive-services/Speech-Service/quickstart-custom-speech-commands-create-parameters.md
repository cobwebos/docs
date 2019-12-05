---
title: 快速入门：使用参数创建自定义命令（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，你将向自定义命令应用程序添加参数。
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: d1c709beb225dff0ae616a23eb67eb57f4643ac9
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812763"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>快速入门：使用参数创建自定义命令（预览）

在[前面的文章](./quickstart-custom-speech-commands-create-new.md)中，我们创建了一个新的自定义命令项目来响应没有参数的命令。

在本文中，我们将利用参数扩展此应用程序，以便它可以处理打开和关闭多个设备的操作。

## <a name="create-parameters"></a>Create Parameters

1. 打开[我们之前创建](./quickstart-custom-speech-commands-create-new.md)的项目
1. 由于该命令现在将处理 on 和 off，因此请将该命令重命名为 "TurnOnOff"
   - 将鼠标悬停在该命令的名称上，然后选择 "编辑" 图标以更改该名称
1. 创建一个新参数，用于表示用户是要打开还是关闭设备
   - 选择 "参数" 部分旁边的 `+` 图标

   > [!div class="mx-imgBorder"]
   > ![创建参数](media/custom-speech-commands/create-on-off-parameter.png)

   | 设置            | 建议的值 | 描述                                                                                               |
   | ------------------ | --------------- | --------------------------------------------------------------------------------------------------------- |
   | 名称               | 麦克风           | 参数的描述性名称                                                                     |
   | 为全局          | 取消选中       | 指示此参数的值是否全局应用于项目中的所有命令的复选框 |
   | 需要           | 已选中         | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值          |
   | 响应模板  | 启用还是禁用？      | 在未知情况下询问此参数值的提示                                       |
   | Type               | 字符串          | 参数的类型，例如数字、字符串或日期时间                                               |
   | 配置      | 字符串列表     | 对于字符串，字符串列表将输入限制为一组可能值                                      |
   | 字符串列表值 | on, off         | 对于字符串列表参数，为可能的值集及其同义词                                |

   - 接下来，再次选择 "`+`" 图标以添加一个表示设备名称的第二个参数。 对于本示例，电视和风扇

   | 设置            | 建议的值   | 描述                                                                                               |
   | ------------------ | ----------------- | --------------------------------------------------------------------------------------------------------- |
   | 名称               | SubjectDevice     | 参数的描述性名称                                                                     |
   | 为全局          | 取消选中         | 指示此参数的值是否全局应用于项目中的所有命令的复选框 |
   | 需要           | 已选中           | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值          |
   | 响应模板  | 哪个设备？     | 在未知情况下询问此参数值的提示                                       |
   | Type               | 字符串            | 参数的类型，例如数字、字符串或日期时间                                               |
   | 配置      | 字符串列表       | 对于字符串，字符串列表将输入限制为一组可能值                                      |
   | 字符串列表值 | 电视、风扇           | 对于字符串列表参数，为可能的值集及其同义词                                |
   | 同义词（电视）      | 电视，telly | 字符串列表参数的每个可能值的可选同义词                                      |

## <a name="add-sample-sentences"></a>添加示例句子

对于参数，添加涵盖所有可能组合的示例句子很有用。 例如：

1. 完整参数信息-`"turn {OnOff} the {SubjectDevice}"`
1. 部分参数信息-`"turn it {OnOff}"`
1. 无参数信息-`"turn something"`

包含不同信息量的示例句子允许自定义命令应用程序解析包含部分信息的一次性解析和多轮解析。

明确这一点后，编辑示例句子以使用下面建议的参数。

> [!TIP]
> 示例句子编辑器使用大括号来引用参数。 - `turn {OnOff} the {SubjectDevice}` 使用 tab 自动补全来引用前面创建的参数。

> [!div class="mx-imgBorder"]
> 带参数的 ![示例句子](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>向完成规则添加参数

修改在[前面的快速入门](./quickstart-custom-speech-commands-create-new.md)中创建的完成规则：

1. 添加新条件，并选择 "必需参数"。 同时选择 `OnOff` 和 `SubjectDevice`
1. 编辑语音响应操作以使用 `OnOff` 和 `SubjectDevice`：

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>试试看

打开测试聊天面板，尝试几个交互。

- 输入：关闭电视
- 输出：确定，关闭电视

- 输入：关闭电视
- 输出：确定，关闭电视

- 输入：将其关闭
- 输出：哪些设备？
- 输入：电视
- 输出：确定，关闭电视

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用 Speech SDK （预览版）连接到自定义命令应用程序](./quickstart-custom-speech-commands-speech-sdk.md)

