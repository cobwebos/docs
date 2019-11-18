---
title: 使用 Visual Studio Code 创建模板
description: 了解如何安装和使用 Visual Studio Code 及 Azure 资源管理器工具扩展。
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 3f0eaf44a536cfb54796744068fee8a70135597a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149392"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>使用 Visual Studio Code 创建 Azure 资源管理器模板

Visual Studio Code 是一个轻型的多平台开源编辑器。 Azure 资源管理器模板工具扩展是用于资源管理器模板开发的插件。 该扩展增加了模板的语言支持，可提供 Intellisense、语法突出显示、内联帮助以及其他许多语言功能。 这些功能共同提供了推荐的模板开发体验。

## <a name="install-visual-studio-code"></a>安装 Visual Studio Code

Visual studio Code 支持 MacOS、Windows 和 Linux。  可以从 [Visual Studio Code](https://code.visualstudio.com/) 网站安装它。

## <a name="install-resource-manager-tools-extension"></a>安装资源管理器工具扩展

1. 打开 Visual Studio Code。
1. 在左侧菜单中选择“扩展”。 或者，在“视图”菜单中选择“扩展”打开“扩展”窗格。

    ![安装 Visual Studio Code 资源管理器工具扩展](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. 搜索“资源管理器”。
1. 在“Azure 资源管理器工具”下选择“安装”。

## <a name="the-extension-features"></a>扩展功能

### <a name="colorization-for-template-language-expressions"></a>模板语言表达式的色彩含义

参数、变量、函数、名称和表达式的颜色编码如以下屏幕截图所示：

![Visual Studio Code 资源管理器工具扩展 - 色彩含义](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

在模板大纲视图中可以轻松浏览大型模板。

### <a name="intellisense"></a>Intellisense

资源管理器模板扩展知道函数名称、参数、变量和引用的可能填写方式。 键入时，会弹出 IntelliSense 建议。 如果你继续键入字符，成员（变量、方法等）列表将会筛选，仅包括带有所键入字符的成员。 按 **Tab** 或 **Enter** 键会插入所选成员。

- 内置函数名称

    ![Visual Studio Code 资源管理器工具扩展 - Intellisense 函数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- 参数引用

    ![Visual Studio Code 资源管理器工具扩展 - Intellisense 参数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- 变量引用

    ![Visual Studio Code 资源管理器工具扩展 - Intellisense 变量](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup() 属性

    ![Visual Studio Code 资源管理器工具扩展 - Intellisense 函数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

此外，Intellisense 还可处理 subscription() 属性，以及对变量（属于对象）的引用的属性。

### <a name="signature-help-for-function-parameters"></a>函数参数的签名帮助

当鼠标悬停在函数名称上时，该扩展会显示函数参数的签名帮助。

![Visual Studio Code 资源管理器工具扩展 - 签名函数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>转到变量和参数引用的定义

您可以使用**Ctrl + 单击**跳转到该定义，或者使用上下文菜单（如屏幕截图： ![Visual Studio Code 资源管理器工具扩展转到 "定义"](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

可以在**按住 Ctrl+Alt 的同时单击**，在另一侧打开定义。

### <a name="peek-for-variable-and-parameter-definitions"></a>速览变量和参数定义

若要打开速览编辑器，请使用前一屏幕截图中所示的上下文菜单。

以下屏幕截图显示了速览编辑器：

![Visual Studio Code 资源管理器工具扩展 - 速览编辑器](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>查找变量和参数的所有引用

若要查找所有引用，请使用前一屏幕截图中所示的上下文菜单。

以下屏幕截图显示了引用的突出显示方式：

![Visual Studio Code 资源管理器工具扩展 - 查找所有引用](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>重命名变量和参数的所有引用

若要重命名变量和参数的所有引用，请使用前一屏幕截图中所示的上下文菜单。

### <a name="hover-for-parameter-description"></a>悬停鼠标显示参数说明

![Visual Studio Code 资源管理器工具扩展 - 将鼠标悬停在定义上](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>括号匹配

将光标靠近某个匹配括号时，会立即突出显示该括号。 单击某个括号时，还会突出显示匹配的括号，如以下屏幕截图所示：

![Visual Studio Code 资源管理器工具扩展 - 括号匹配](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>显示错误和警告

该扩展识别的错误包括：

- 未定义的参数引用
- 未定义的变量引用
- 无法识别的函数名称
- 变量定义中的 reference() 函数用法
- 函数中的参数数目不正确

警告包括：

- 未使用的参数
- 未使用的变量

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建 Azure 资源管理器模板，请参阅[教程：创建和部署第一个 Azure 资源管理器模板](template-tutorial-create-first-template.md)。
- 若要通过使用 Visual Studio Code 进行快速入门，请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
