---
title: 如何：将参数配置为外部实体实体
titleSuffix: Azure Cognitive Services
description: 本文介绍如何配置字符串参数以引用通过 web 端点公开的目录。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284177"
---
# <a name="add-configurations-to-commands-parameters"></a>向命令参数添加配置

本文详细介绍高级参数配置，包括：

 - 如何将参数值归属于在外部定义为自定义命令应用程序的集
 - 如何将验证子句添加到参数的值上

## <a name="prerequisites"></a>必备条件

你必须已完成以下文章中的步骤：

> [!div class="checklist"]
> * [如何：用简单命令创建应用程序](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：向命令添加参数](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>将参数配置为外部目录实体

在本部分中，将配置字符串类型参数以引用通过 web 终结点承载的外部目录。 这使你无需对自定义命令应用程序进行编辑即可单独更新外部目录。 此方法在目录条目数量很大的情况下非常有用。

使用**TurnOnOff**命令中的**SubjectDevice**参数。 此参数的当前配置**接受来自内部目录的预定义输入**。 这是指参数配置中定义的设备的静态列表。 我们想要将此内容移到可以单独更新的外部数据源。

为此，请首先添加新的 web 终结点。 在左侧窗格中转到 " **web 终结点**" 部分，并添加具有以下配置的新 Web 终结点。

| 设置 | 建议的值 |
|----|----|
| 名称 | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| 方法 | GET |


如果 URL 的建议值对你不起作用，则需要配置并托管一个简单的 web 终结点，该终结点将返回一个 json，其中包含可控制的设备列表。 Web 终结点应返回格式为的 json，如下所示：
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


接下来，转到 " **SubjectDevice**参数设置" 页，将属性更改为以下项。

| 设置 | 建议的值 |
| ----| ---- |
| 配置 | 接受来自外部目录的预定义输入 |                               
| 目录终结点 | getDevices |
| 方法 | GET |

然后选择“保存”。

> [!IMPORTANT]
> 除非在左窗格的 " **web 终结点**" 部分中设置了 web 终结点，否则不会显示用于将参数配置为接受来自外部目录的输入的选项。

### <a name="try-it-out"></a>试试看

选择 "**定型**" 并等待训练完成。 训练完成后，请选择 "**测试**"，然后尝试几个交互。

* 输入：开启
* 输出：要控制哪个设备？
* 输入：光源
* 输出：好，打开灯

> [!NOTE]
> 请注意，现在可以如何控制在 web 终结点上托管的所有设备。 你仍需要对应用程序进行定型，以便测试新的更改并重新发布应用程序。

## <a name="add-validation-to-parameters"></a>向参数添加验证

**验证**是适用于某些参数类型的构造，这些参数类型允许你对参数的值配置约束，并且如果值不在约束范围内，则提示进行更正。 有关扩展验证构造的参数类型的完整列表，请参阅[引用](./custom-commands-references.md)

使用**SetTemperature**命令测试验证。 使用以下步骤为**温度**参数添加验证。

1. 在左侧窗格中选择 " **SetTemperature** " 命令。
1. 在中间窗格中选择 "**温度**"。
1. 在右窗格中选择 "**添加验证**"。
1. 在 "**新建验证**" 窗口中，按如下所示配置验证，然后选择 "**创建**"。


    | 参数配置 | 建议的值 | 说明 |
    | ---- | ---- | ---- |
    | 最小值 | `60` | 对于 Number 参数，此参数可以采用的最小值 |
    | 最大值 | `80` | 对于 Number 参数，此参数可以采用的最大值 |
    | 故障响应 |  简单编辑器 > 第一变化 >`Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 验证失败时提示要求输入新值 |

    > [!div class="mx-imgBorder"]
    > ![添加范围验证](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>试试看

1. 选择位于右窗格顶部的 "**定型**" 图标。

1. 完成培训后，请选择 "**测试**"，然后尝试进行几个交互：

    - 输入：将温度设置为72度
    - 输出：正常，将温度设置为72度
    - 输入：将温度设置为45度
    - 输出：抱歉，只能设置60到80度之间的温度
    - 输入：改为72度
    - 输出：正常，将温度设置为72度

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：添加交互规则](./how-to-custom-commands-add-interaction-rules.md)
