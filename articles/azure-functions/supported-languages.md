---
title: Azure Functions 中支持的语言
description: 了解支持哪些语言 (GA) 以及哪些语言是实验性的或处于预览状态。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 2de2ebdea41ca35e853b37ab804e516eb7f4df9f
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494442"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions 中支持的语言

本文介绍为可用于 Azure Functions 的语言提供的支持级别。

## <a name="levels-of-support"></a>支持级别

有三个支持级别：

* **正式发布 (GA)** - 完全支持并获得批准在生产中使用。
* **预览** - 尚不支持，但将来应达到 GA 状态。
* **实验性** - 不支持，将来可能会弃用；不保证最终达到预览或 GA 状态。

## <a name="languages-in-runtime-1x-and-2x"></a>运行时 1.x 和 2.x 中的语言

[这两个版本的 Azure Functions 运行时](functions-versions.md)都可用。 1.x 运行时是 GA 版。 它是唯一批准用于生产应用程序的运行时。 2.x 运行时当前为预览版，因此它支持的语言处于预览状态。 下表显示每个运行时版本支持的语言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>实验性语言

1.x 版中的实验性语言扩展性不好，并且不支持所有绑定。 例如，Python 速度慢，因为每次调用函数时，Functions 运行时都运行 *python.exe*。 并且虽然 Python 支持 HTTP 绑定，但它无法访问请求对象。

对 PowerShell 的实验性支持仅限于 5.1 版，因为运行函数应用的 VM 上只默认安装了该版本。 如果要运行 PowerShell 脚本，请考虑 [Azure 自动化](https://azure.microsoft.com/services/automation/)。

如果要使用仅在 1.x 中可用的某种语言，将停留在 1.x 运行时。 但不要对所依赖的任何内容使用实验性语言，因此对其没有官方支持。 可以通过[创建 GitHub 问题](https://github.com/Azure/azure-webjobs-sdk-script/issues)请求帮助，但不应为实验性语言问题建立支持案例。 

2.x 版运行时不支持实验性语言。 只有在生产环境中支持该语言时，才会添加对新语言的支持。 

### <a name="language-extensibility"></a>语言扩展性

2.x 运行时旨在提供[语言扩展性](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)。 最早的基于此扩展性模型的语言是在 2.x 中以预览版提供的 Java。

## <a name="next-steps"></a>后续步骤

若要了解如何在 Azure Functions 中使用 GA 或预览版语言之一的详细信息，请参阅以下资源：

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)
