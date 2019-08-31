---
title: 使用 Visual Studio Code 创建 Azure 资源管理器模板
description: 了解如何安装和使用 Visual Studio Code 和 Azure 资源管理器工具扩展。
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 8d2b818846eeae44ffe05b4546a333510c638621
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196148"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>使用 Visual Studio Code 创建 Azure 资源管理器模板

Visual Studio Code 是一种轻型的多平台开源编辑器。 Azure 资源管理器模板工具扩展是用于资源管理器模板开发的插件。 扩展增加了模板的语言支持, 可为你提供 intellisense、语法突出显示、行内帮助以及许多其他语言功能。 它们共同提供了建议的模板开发体验。

## <a name="install-visual-studio-code"></a>安装 Visual Studio Code

Visual studio Code 支持 MacOS、Windows 和 Linux。  它可以从[Visual Studio Code](https://code.visualstudio.com/)安装。

## <a name="install-resource-manager-tools-extension"></a>安装资源管理器工具扩展

1. 打开 Visual Studio Code。
1. 从左侧菜单中选择 "**扩展**"。 或者从 "**视图**" 菜单中选择 "**扩展**", 打开 "扩展" 窗格。

    ![安装 Visual Studio Code 资源管理器工具扩展](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. 搜索**资源管理器**。
1. 在 " **Azure 资源管理器工具**" 下选择 "**安装**"。

## <a name="the-extension-features"></a>扩展功能

### <a name="colorization-for-template-language-expressions"></a>模板语言表达式的着色

参数、变量、函数、名称和表达式采用颜色编码, 如以下屏幕截图所示:

![Visual Studio Code 资源管理器工具扩展着色](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

使用 "模板大纲" 视图, 可以轻松地在大型模板之间导航。

### <a name="intellisense"></a>智能

资源管理器模板扩展知道函数名称、参数、变量和引用的可能完成。 键入时, IntelliSense 建议会弹出。 如果继续键入字符, 则筛选成员列表 (变量、方法等), 使之仅包含包含类型化字符的成员。 按**tab**或**enter**将插入所选成员。

- 内置函数名称

    ![Visual Studio Code 资源管理器工具扩展 intellisense 函数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- 参数引用

    ![Visual Studio Code 资源管理器工具扩展 intellisense 参数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- 变量引用

    ![Visual Studio Code 资源管理器工具扩展 intellisense 变量](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup () 属性

    ![Visual Studio Code 资源管理器工具扩展 intellisense 函数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

此外, intellisense 还适用于订阅 () 属性和对作为对象的变量的引用的属性。

### <a name="signature-help-for-function-parameters"></a>函数参数的签名帮助

当鼠标悬停在函数名称上时, 扩展会显示函数参数的签名帮助。

![Visual Studio Code 资源管理器工具扩展签名函数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>中转到变量和参数引用的定义

您可以使用**Ctrl + 单击**跳转到定义, 也可以通过使用屏幕截图中所示的上下文菜单来跳转到该定义:![Visual Studio Code 资源管理器工具扩展中转到 "定义"](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

您可以通过按**Ctrl + Alt + 单击**打开该定义。

### <a name="peek-for-variable-and-parameter-definitions"></a>查看变量和参数定义

若要打开扫视编辑器, 请使用前面屏幕截图中所示的上下文菜单。

以下屏幕截图显示了速览编辑器:

![Visual Studio Code 资源管理器工具扩展速览编辑器](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>查找变量和参数的所有引用

若要查找所有引用, 请使用上下文菜单, 如前面的屏幕截图中所示。

以下屏幕截图显示了引用的突出显示方式:

![Visual Studio Code 资源管理器工具扩展查找所有引用](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>重命名变量和参数的所有引用

若要重命名变量和参数的所有引用, 请使用上下文菜单, 如前面的屏幕截图中所示。

### <a name="hover-for-parameter-description"></a>用于参数说明的悬停

![Visual Studio Code 资源管理器工具扩展悬停定义](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>大括号匹配

一旦光标位于其中一个括号附近, 就会突出显示匹配的括号。 单击大括号时, 还会突出显示匹配的大括号, 如以下屏幕截图所示:

![Visual Studio Code 资源管理器工具扩展大括号匹配](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>显示错误和警告

扩展标识的错误包括:

- 未定义的参数引用
- 未定义的变量引用
- 无法识别的函数名
- reference () 变量定义中的函数用法
- 函数中的参数数目不正确

警告包括:

- 未使用的参数
- 未使用的变量

## <a name="next-steps"></a>后续步骤

- 若要详细了解资源管理器和资源管理器模板, 请参阅[Azure 资源管理器概述](./resource-group-overview.md)。
- 若要通过使用 Visual Studio Code 进行快速入门, 请[参阅快速入门:使用 Visual Studio Code 创建 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
