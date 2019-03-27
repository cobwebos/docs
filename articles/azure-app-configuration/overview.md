---
title: 什么是 Azure 应用程序配置？ | Microsoft Docs
description: Azure 应用配置服务的概述。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 8c2c31b4d87ac2db123c0cae63679e3773734b4f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226277"
---
# <a name="what-is-azure-app-configuration"></a>什么是 Azure 应用程序配置？

Azure 应用程序配置提供一项可集中管理应用程序设置的服务。 新式程序，尤其是在云端运行的程序，通常具有多个事实上已分发的组件。 跨这些组件分散配置设置可能导致应用程序部署过程中出现难以解决的错误。 使用应用程序配置来存储应用程序的所有设置并在一个位置保证其访问权限的安全。

应用程序配置可在预览版期间免费使用。 若要进行试用，请[注册](https://aka.ms/azconfig/register)预览版。

## <a name="why-use-app-configuration"></a>为何要使用应用程序配置？

基于云的应用程序通常在多个区域中的多个虚拟机或容器上运行，并且使用多个外部服务。 创建可靠且可伸缩的分发式应用程序是一项挑战。 

各种编程方法可帮助开发人员应对应用程序构建日益复杂的问题。 例如，12 要素应用描述了很多经过良好测试的架构模式和云应用程序的最佳配用做法。 本指南中的一个关键建议是将配置从代码中分离出来。 在本例中，应用程序的配置设置应保留在其可执行文件的外部，并从其运行时环境或外部源进行读取。

当应用程序可使用应用程序配置时，可参见以下示例，了解使用它可让哪些类型的应用程序受益：

* 基于 Azure Kubernetes 服务的微服务、Azure Service Fabric，或者部署在一个或多个地理区域的其他容器化应用
* 无服务器应用，其中包括 Azure Functions 和其他事件驱动型无状态计算应用
* 持续部署管道

应用配置具有以下优势：

* 几分钟内即可设置的完全托管的服务
* 灵活的密钥表示形式和映射
* 使用标签进行标记
* 设置的时间点重播
* 可比较自定义维度上的两组配置
* 通过 Azure 托管的标识增强了安全性
* 完整数据加密（无论是静态数据还是运输中的数据）
* 与热门框架的本机集成

应用程序配置可实现 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)，后者用于存储应用程序密钥。 借助应用程序配置，可更轻松地实现以下场景：

* 针对不同的环境和地理区域集中管理和分发分层配置数据
* 动态配置更改，无需重新部署或重启应用程序
* 特色管理

## <a name="use-app-configuration"></a>使用应用程序配置

将应用配置存储区添加到应用程序的最简单方法是通过 Microsoft 提供的客户端库。 根据编程语言和框架，你可使用以下最佳方法。

| 编程语言和框架 | 如何连接 |
|---|---|
| .NET Core 和 ASP.NET Core | 面向 .NET Core 的应用程序配置提供程序 |
| .NET 和 ASP.NET | 面向 .NET 的应用程序配置生成器 |
| Java Spring | 面向 Spring Cloud 的应用程序配置客户端 |
| 其他 | 应用程序配置 REST API |

## <a name="next-steps"></a>后续步骤

* [快速入门：创建 ASP.NET Web 应用](quickstart-aspnet-core-app.md) 
