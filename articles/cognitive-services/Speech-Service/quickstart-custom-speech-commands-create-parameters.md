---
title: 快速入门：使用参数创建自定义命令（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，您将向自定义命令应用程序添加参数。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348533"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>快速入门：使用参数创建自定义命令（预览）

在[上一篇文章中](./quickstart-custom-speech-commands-create-new.md)，我们创建了一个新的自定义命令项目来响应没有参数的命令。

在本文中，我们将使用参数扩展此应用程序，以便它可以处理打开和关闭多个设备。

## <a name="create-parameters"></a>Create Parameters

1. 打开[我们以前创建](./quickstart-custom-speech-commands-create-new.md)的项目
1. 由于命令现在将打开和关闭，请重命名命令为"打开"
   - 将鼠标悬停在命令的名称上，然后选择编辑图标以更改名称
1. 创建新参数以表示用户是打开还是关闭设备
   - 选择`+`"参数"部分旁边的图标

   > [!div class="mx-imgBorder"]
   > ![创建参数](media/custom-speech-commands/create-on-off-parameter.png)

   | 设置            | 建议的值     | 描述                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | “属性”               | 开关               | 参数的描述性名称                                                                     |
   | 是全局的          | unchecked           | 指示此参数的值是否全局应用于项目中的所有命令复选框 |
   | 必选           | checked             | 选中复选框，指示在完成命令之前是否需要此参数的值          |
   | 响应模板  | "-开还是关？"      | 提示在不知道此参数时请求该参数的值                                       |
   | 类型               | String              | 参数的类型，如数字、字符串或日期时间                                               |
   | Configuration      | 字符串列表         | 对于字符串，字符串列表将输入限制为一组可能的值                                      |
   | 字符串列表值 | on, off             | 对于字符串列表参数，可能的值集及其同义词                                |

   - 接下来，`+`再次选择图标以添加第二个参数以表示设备的名称。 对于此示例，电视和风扇

   | 设置            | 建议的值       | 描述                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | “属性”               | 主题设备         | 参数的描述性名称                                                                     |
   | 是全局的          | unchecked             | 指示此参数的值是否全局应用于项目中的所有命令复选框 |
   | 必选           | checked               | 选中复选框，指示在完成命令之前是否需要此参数的值          |
   | 响应模板  | "-哪种设备？     | 提示在不知道此参数时请求该参数的值                                       |
   | 类型               | String                | 参数的类型，如数字、字符串或日期时间                                               |
   | Configuration      | 字符串列表           | 对于字符串，字符串列表将输入限制为一组可能的值                                      |
   | 字符串列表值 | 电视， 风扇               | 对于字符串列表参数，可能的值集及其同义词                                |
   | 同义词（电视）      | 电视， 电视     | 字符串列表参数的每个可能值的可选同义词                                      |

## <a name="add-sample-sentences"></a>添加示例句子

使用参数，添加涵盖所有可能组合的示例句子非常有用。 例如：

1. 完整参数信息 -`"turn {OnOff} the {SubjectDevice}"`
1. 部分参数信息 -`"turn it {OnOff}"`
1. 无参数信息 -`"turn something"`

具有不同信息量的示例句子允许自定义命令应用程序使用部分信息同时解析单次分辨率和多转分辨率。

考虑到这一点，编辑示例句子以按照下面建议使用参数。

> [!TIP]
> 在"示例句子"编辑器中使用大括号来引用您的参数。 - `turn {OnOff} the {SubjectDevice}`使用选项卡完成引用以前创建的参数。

> [!div class="mx-imgBorder"]
> ![具有参数的句子示例](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>将参数添加到"完成"规则

修改您在[上一个快速入门](./quickstart-custom-speech-commands-create-new.md)中创建的完成规则：

1. 添加新条件并选择"必需"参数。 同时选择`OnOff`和`SubjectDevice`
1. 编辑"语音响应"操作以`OnOff`使用`SubjectDevice`和 ：

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>试用

打开"测试聊天"面板并尝试一些交互。

- 输入：关闭电视
- 输出：好的，关闭电视

- 输入：关闭电视
- 输出：好的，关闭电视

- 输入：将其关闭
- 输出：哪种设备？
- 输入：电视
- 输出：好的，关闭电视

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用自定义命令与自定义语音（预览）](./quickstart-custom-speech-commands-select-custom-voice.md)
