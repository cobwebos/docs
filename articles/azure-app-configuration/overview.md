---
title: 什么是 Azure 应用配置 | Microsoft Docs
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
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885015"
---
# <a name="what-is-azure-app-configuration"></a>什么是 Azure 应用配置

Azure 应用配置提供用于集中管理应用程序设置的服务。 新式程序（特别是在云中运行的程序）通常具有在本质上呈分布式的许多组件。 跨这些组件分散配置设置可能导致应用程序部署过程中出现难以解决的错误。 借助应用配置，可以存储应用程序的所有设置并在同一个位置保护其访问。

## <a name="why-use-app-configuration"></a>为什么使用应用配置

基于云的应用程序通常在多个区域中的多个虚拟机或容器上运行，并且使用多个外部服务。 创建强大且可伸缩的此类分布式应用程序是一个真正的难题。 出现了许多编程方法，以帮助开发者处理生成这些应用程序的不断增加的复杂性。 例如，12 因素应用详细描述了许多经过测试的体系结构模式和用于云应用程序的最佳做法。 本指南中的一个关键建议是将配置从代码中分离出来。 这意味着应用程序的配置（如设置）应与其可执行文件不相关，且从其运行时环境或外部源读取。

虽然任何应用程序可以利用它，但以下是应利用应用配置的应用程序类型的不错示例：

* 基于 AKS、Service Fabric 或在一个或多个地理区域中部署的其他容器化应用的微服务。
* 无服务器应用，包括 Azure Functions 或其他事件驱动的无状态计算应用。
* 持续部署管道。

应用配置具有以下优势：

* 可以在几分钟内设置的完全托管的服务。
* 灵活的密钥表示和映射。
* 使用标签进行标记。
* 设置的时间点重播。
* 比较自定义维度上的两组配置。
* 通过 Azure 管理的标识实现安全性增强。
* 完成数据加密（静态或在传输过程中）。
* 与常用框架的本机集成。

## <a name="how-to-use-app-configuration"></a>如何使用应用配置

将应用配置存储区添加到应用程序的最简单方法是通过 Microsoft 提供的客户端库。 以下显示可用的最佳方法，具体取决于编程语言和框架：

| 编程语言和框架 | 如何连接 |
|---|---|
| .NET Core 和 ASP.NET Core | 适用于 .NET Core 的应用配置配置提供程序。 |
| .NET 和 ASP.NET | 适用于 .NET 的应用配置配置生成器。 |
| **Java Spring** | 适用于 Spring Cloud 的应用配置配置客户端。 |
| 其他 | 应用配置 REST API。 |

## <a name="next-steps"></a>后续步骤

* [快速入门：创建 ASP.NET Web 应用](quickstart-aspnet-core-app.md)  
