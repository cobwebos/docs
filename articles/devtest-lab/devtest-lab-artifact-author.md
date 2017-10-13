---
title: "为开发测试实验室虚拟机创建自定义项目 | Microsoft Docs"
description: "了解如何创作自己的项目以用于 Azure 开发测试实验室。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 679819618452d65847c6163569e04945ba8a414d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>为开发测试实验室虚拟机创建自定义项目

请观看以下视频，了解本文所述步骤：

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>概述
预配 VM 后，可使用项目来部署和设置应用程序。 项目由项目定义文件和存储在 Git 存储库的文件夹中的其他脚本文件组成。 项目定义文件包含 JSON 和可用于指定要在 VM 上安装的内容的表达式。 例如，可以定义项目名称、要运行的命令，以及运行命令时可用的参数。 可以按名称引用项目定义文件中的其他脚本文件。

## <a name="artifact-definition-file-format"></a>项目定义文件格式
以下示例显示组成定义文件基本结构的各个部分：

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| 元素名称 | 必需？ | 说明 |
| --- | --- | --- |
| $schema |否 |JSON 架构文件的位置。 JSON 架构文件可帮助测试定义文件的有效性。 |
| title |是 |实验室中显示的项目的名称。 |
| description |是 |实验室中显示的项目的说明。 |
| iconUri |否 |实验室中显示的图表的 URI。 |
| targetOsType |是 |在其中安装项目的 VM 的操作系统。 支持的选项为：Windows 和 Linux。 |
| parameters |否 |在计算机上运行项目安装命令时提供的值。 可帮助对项目进行自定义。 |
| runCommand |是 |在 VM 上执行的项目安装命令。 |

### <a name="artifact-parameters"></a>项目参数
在定义文件的参数部分中，指定用户在安装项目时可以输入的值。 可以在项目安装命令中引用这些值。

若要定义参数，请使用以下结构：

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| 元素名称 | 必需？ | 说明 |
| --- | --- | --- |
| type |是 |参数值的类型。 请参阅以下允许类型列表。 |
| displayName |是 |在实验室中显示给用户的参数的名称。 | |
| description |是 |实验室中显示的参数的说明。 |

允许的类型是：

* 字符串（任何有效的 JSON 字符串）
* int（任何有效的 JSON 整数）
* bool（任何有效的 JSON 布尔值）
* 数组（任何有效的 JSON 数组）

## <a name="artifact-expressions-and-functions"></a>项目表达式和函数
可以使用表达式和函数构建项目安装命令。
表达式用方括号（[和]）括起来，并在安装项目时求值。 表达式可以出现在 JSON 字符串值的任何位置。 表达式始终返回另一个 JSON 值。 如果需要使用以方括号 ([) 开头的文本字符串，则必须使用两个方括号 ([[)。
通常情况下，使用具有函数的表达式来构造值。 如同在 JavaScript 中一样，函数调用的格式为 **functionName(arg1,arg2,arg3)**。

以下列表显示常见函数：

* parameters(parameterName)：返回运行项目命令时提供的参数值。
* concat(arg1,arg2,arg3, …..)：合并多个字符串值。 此函数可结合使用各种类型的参数。

以下示例显示如何使用表达式和函数构造值：

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>创建自定义项目

1. 安装 JSON 编辑器。 需要一个 JSON 编辑器来处理项目定义文件。 建议使用 [Visual Studio Code](https://code.visualstudio.com/)，它适用于 Windows、Linux 和 OS X。
2. 获取示例 artifactfile.json 定义文件。 查看开发测试实验室团队在我们的 [GitHub 存储库](https://github.com/Azure/azure-devtestlab)中创建的项目。 我们已创建资源丰富的项目库，可帮助你创建自己的项目。 下载项目定义文件并对其进行更改以创建自己的项目。
3. 利用 IntelliSense。 使用 IntelliSense 查看可用于构建项目定义文件的有效元素。 此外还可以查看元素值的不同选项。 例如，编辑“targetOsType”元素时，IntelliSense 显示 Windows 或 Linux 两种选择。
4. 将项目存储在 [ 存储库](devtest-lab-add-artifact-repo.md)中。
   
   1. 为每个项目创建单独的目录。 目录名称应与项目名称相同。
   2. 将项目定义文件 (artifactfile.json) 存储在创建的目录中。
   3. 存储从项目安装命令引用的脚本。
      
      下面是项目文件夹的外观示例：
      
      ![项目文件夹示例](./media/devtest-lab-artifact-author/git-repo.png)
5. 将项目存储库添加到实验室中。 请参阅[添加 Git 存储库以存储项目和模板](devtest-lab-add-artifact-repo.md)。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>相关文章
* [如何诊断开发测试实验室中的项目失败](devtest-lab-troubleshoot-artifact-failure.md)
* [使用开发测试实验室中的资源管理器模板将 VM 加入到现有 Active Directory 域中](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>后续步骤
* 了解如何 [将 Git 项目存储库添加到实验室](devtest-lab-add-artifact-repo.md)。

