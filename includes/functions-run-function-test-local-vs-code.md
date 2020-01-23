---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ebcbe7e8af7e01a9b7e75282be4e4f4ceb80b806
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279574"
---
## <a name="run-the-function-locally"></a>在本地运行函数

使用 Azure Functions Core Tools 可以在本地开发计算机上运行 Azure Functions 项目。

1. 若要测试函数，请在函数代码中设置断点并按 F5 启动函数应用项目。 来自 Core Tools 的输出会显示在“终端”  面板中。

1. 在“终端”  面板中，复制 HTTP 触发的函数的 URL 终结点。 

    ![Azure 本地输出](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并执行请求。 执行将在命中断点时暂停。

1. 下面显示了当继续执行时浏览器中对 GET 请求的响应：

    ![浏览器中的函数 localhost 响应](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 若要停止调试，请按 Shift + F5。
