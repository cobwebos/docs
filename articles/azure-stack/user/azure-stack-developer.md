---
title: "开发 Azure 堆栈的应用 |Microsoft 文档"
description: "了解 Azure 堆栈上的原型制作应用程序中的开发注意事项"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 062c17173f87eec8e0eaa3f74323cbf8a8f48571
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-stack"></a>为 Azure Stack 进行开发
你可以开始开发应用程序今天，即使你没有对 Azure 堆栈环境的访问。 因为 Azure 堆栈提供了在你的数据中心中运行的 Microsoft Azure 服务，可以使用类似的工具和流程来开发针对 Azure 堆栈，就像使用 Azure。  稍加准备和从以下主题的指南，可以使用 Azure 来模拟 Azure 堆栈环境：

* 在 Azure 中，你可以创建 Azure 资源管理器模板也是可部署到 Azure 堆栈的指令。  请参阅[模板注意事项](azure-stack-develop-templates.md)有关开发你的模板以确保可移植性的指南。
* 没有服务可用性和 Azure 和 Azure 堆栈之间的服务版本控制中增量。 你可以使用[Azure 堆栈策略模块](azure-stack-policy-module.md)限制对什么是 Azure 堆栈中可用的 Azure 服务可用性和资源类型。 约束可用服务将帮助你的应用程序依赖于服务可供 Azure 堆栈。
* [Azure 堆栈快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)是如何开发你的模板，以便他们可以部署到 Azure 和 Azure 堆栈的常见方案示例。


