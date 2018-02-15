---
title: "Azure Functions 运行时版本概述"
description: "Azure Functions 支持多个版本的运行时。 了解这些版本之间的差异以及如何选择最适合你的版本。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 运行时版本概述

 Azure Functions 运行时有两个主版本：1.x 和 2.x。 本文介绍如何选择要使用的主版本。

> [!IMPORTANT] 
> 运行时 1.x 是批准在生产环境中使用的唯一版本。

| 运行时 | 状态 |
|---------|---------|
|1.x|正式发布 (GA)|
|2.x|预览|

有关如何针对特定版本配置函数应用或开发环境的信息，请参阅[如何面向 Azure Functions 运行时版本](set-runtime-version.md)和[在本地编码和测试 Azure Functions](functions-run-local.md)。

## <a name="cross-platform-development"></a>跨平台开发

运行时 1.x 仅支持在门户中或在 Windows 上进行开发和托管。 运行时 2.x 在 .NET Core 上运行，这意味着它可以在 .NET Core 支持的所有平台上运行，包括 macOS 和 Linux。 这可实现 1.x 不支持的跨平台开发和托管方案。

## <a name="languages"></a>语言

运行时 2.x 使用新的语言扩展性模型。 最初，JavaScript 和 Java 利用此新模型。 Azure Functions 1.x 实验性语言尚未更新为使用此新模型，因此它们在 2.x 中不受支持。 下表指示每个运行时版本支持的编程语言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

有关详细信息，请参阅[支持的语言](supported-languages.md)。

## <a name="bindings"></a>绑定 

运行时 2.x 使用新的[绑定扩展性模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，该模型可提供以下优势：

* 支持第三方绑定扩展。
* 运行时和绑定分离。 这允许对绑定扩展进行版本控制和单独发布。 例如，可以选择升级到依赖于基础 SDK 的较新版本的扩展版本。
* 更轻便的执行环境，其中运行时仅知道和加载正在使用的绑定。

所有内置 Azure Functions 绑定已采用此模型，默认情况下不再包含，但计时器触发器和 HTTP 触发器除外。 通过 Visual Studio 等工具或通过门户创建函数时，将自动安装这些扩展。

下表指示每个运行时版本支持的绑定。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>2.x 中的已知问题

有关 2.x 中的绑定支持和其他功能差异的详细信息，请参阅[运行时 2.0 已知问题](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本地开发环境中指定 2.0 运行时](functions-run-local.md)

> [!div class="nextstepaction"]
> [查看运行时版本的发行说明](https://github.com/Azure/azure-webjobs-sdk-script/releases)