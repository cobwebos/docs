---
title: 开发适用于 Azure Stack 的应用 | Microsoft Docs
description: 有关 Azure 堆栈上的原型制作应用程序的开发注意事项
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199188"
---
# <a name="develop-for-azure-stack"></a>为 Azure Stack 进行开发

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

即使没有 Azure Stack 环境的访问权限，也可以立即开始开发应用程序。 因为 Azure 堆栈提供了在你的数据中心中运行的 Microsoft Azure 服务，可以使用类似的工具和流程来开发针对 Azure 堆栈，就像使用 Azure。 使用一些准备工作，并使用以下主题中的指南，你可以使用 Azure 来模拟 Azure 堆栈环境。

* 在 Azure 中，你可以创建 Azure 资源管理器模板部署到 Azure 堆栈。 请参阅[模板注意事项](azure-stack-develop-templates.md)有关开发模板以确保可移植性的指南。
* 有了服务的可用性和服务版本控制 Azure 和 Azure 堆栈之间的差异。 可以使用 [Azure Stack 策略模块](azure-stack-policy-module.md)来限制 Azure Stack 可用的 Azure 服务可用性和资源类型。 约束服务可确保你的应用程序依赖于服务可供 Azure 堆栈。
* [Azure 堆栈快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)是常见的方案示例演示如何开发可部署到 Azure 和 Azure 堆栈的模板。
