---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 17b7626f79d7d356e3e8f3440e4a6526f2df776d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68593858"
---
Visual Studio 中的 Azure Functions 项目模板创建一个项目，该项目可发布到 Azure 中的函数应用。 可使用函数应用将函数分组为一个逻辑单元，以用于管理、部署和共享资源。

1. 在 Visual Studio 的“文件”菜单中，依次选择“新建” > “项目”    。

1. 在“创建新项目”  对话框中，搜索 `functions`，选择“Azure Functions”  模板，然后选择“下一步”  。

1. 输入项目的名称，并选择“创建”  。 函数应用名称必须可以充当 C# 命名空间，因此请勿使用下划线、连字符或任何其他的非字母数字字符。

1. 在“新建 Azure Functions 应用程序”  中，使用以下选项：

    + **Azure Functions v2 (.NET Core)** 1
    + **HTTP 触发器**
    + **存储帐户**：**存储模拟器**
    + **授权级别**：**匿名** 

    | 选项      | 建议的值  | 说明                      |
    | ------------ |  ------- |----------------------------------------- |
    | Functions 运行时 | **Azure Functions 2.x <br />(.NET Core)** | 此设置创建使用 Azure Functions 运行时版本 2.x（支持 .NET Core）的函数项目。 Azure Functions 1.x 支持 .NET Framework。 有关详细信息，请参阅[面向 Azure Functions 运行时版本](../articles/azure-functions/functions-versions.md)。   |
    | 函数模板 | **HTTP 触发器** | 此设置创建由 HTTP 请求触发的函数。 |
    | **存储帐户**  | **存储模拟器** | HTTP 触发器不使用 Azure 存储帐户连接。 所有其他触发器类型需要有效的存储帐户连接字符串。 由于 Functions 需要存储帐户，因此在将项目发布到 Azure 时会分配或创建一个存储帐户。 |
    | **授权级别** | **匿名** | 在未提供密钥的情况下，任何客户端都可以触发创建的函数。 通过此授权设置可以轻松测试新函数。 有关密钥和授权的详细信息，请参阅 [HTTP 和 Webhook 绑定](../articles/azure-functions/functions-bindings-http-webhook.md)中的[授权密钥](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)。 |
    
    > [!NOTE]
    > 请确保将**授权级别**设置为 `Anonymous`。 如果选择默认级别为 `Function`，需要在请求中提供[函数密钥](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)才能访问函数终结点。
    
4. 选择“创建”以创建函数项目和 HTTP 触发的函数  。
