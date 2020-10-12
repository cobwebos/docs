---
title: 在 Visual Studio Code 中调试 Azure API 管理策略 |Microsoft Docs
description: 了解如何使用 Azure API 管理 Visual Studio Code 扩展调试 Azure API 管理策略
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 4eb32243df219d721d7baae80984c45d0fc4cf25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341302"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>在 Visual Studio Code 中调试 Azure API 管理策略

Azure API 管理中的[策略](api-management-policies.md)提供了强大的功能，可帮助 API 发布者解决跨切削问题，例如身份验证、授权、限制、缓存和转换。 策略是一组语句，在请求或 API 的响应时按顺序执行。 

本文介绍如何使用 [适用于 Visual Studio Code 的 AZURE Api 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)调试 API 管理策略。 

> [!NOTE]
> 此功能目前以公共预览版提供。

## <a name="prerequisites"></a>必备条件

首先按照本 [教程](get-started-create-service-instance.md) 创建 API 管理开发人员层实例。

安装[适用于 Visual Studio Code 的 AZURE API 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement) [Visual Studio Code](https://code.visualstudio.com/)和最新版本。 

## <a name="restrictions-and-limitations"></a>限制和局限

此功能仅在 API 管理的开发人员层中提供。 每个 API 管理实例仅支持一个并发调试会话。

## <a name="initiate-a-debugging-session"></a>启动调试会话

1. 启动 Visual Studio Code
2. 导航到 Azure 扩展下的 API 管理扩展
3. 查找要调试的 API 管理实例
4. 查找要调试的 API 和操作
5. 右键单击该操作，然后选择 "**启动策略调试**"

此时，扩展将尝试使用 API 管理网关启动并建立调试会话。

![启动调试](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>发送测试请求
在建立调试会话时，扩展将打开一个新的编辑器，使我们能够使用 [REST 客户端扩展](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)创建测试 HTTP 请求并将其发送到此操作。

你会注意到， **Apim-调试** 标头已添加到请求中。 此标头是必需的，并且值必须设置为服务级别的 "所有访问" 订阅密钥才能触发 API 管理网关上的调试功能。

根据测试方案，在编辑器中修改 HTTP 请求。 然后单击 " **发送请求** "，将测试请求发送到 API 管理网关。

![发送测试请求](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>调试策略
发送测试 HTTP 请求后，扩展将打开 "调试" 窗口，其中显示了此操作的有效策略，并在第一个有效策略处停止。 

![调试策略](media/api-management-debug-policies/main-window.png)

若要遵循策略管道，你可以单步执行单个策略，或在策略中设置断点，并直接在策略中设置一个断点。 

在 " **变量** " 面板中，您可以检查系统创建的变量和用户创建的变量的值。 在 " **断点** " 面板中，可以看到已设置的所有断点的列表。 在 " **调用堆栈** " 面板中，可以看到当前的有效策略范围。 

如果在执行策略的过程中出现错误，则会在策略发生时看到该错误的详细信息。 

![exceptions](media/api-management-debug-policies/exception.png)

> [!TIP]
> 完成后，请务必通过单击 " **停止** " 按钮来退出调试会话。


## <a name="next-steps"></a>后续步骤

+ 详细了解 Visual Studio Code 的 [API 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)。 
+ [GitHub 存储库](https://github.com/Microsoft/vscode-apimanagement)中的报告问题

