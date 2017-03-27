---
title: "Azure Functions 概述 | Microsoft Docs"
description: "了解如何使用 Azure Functions 以分钟为单位优化异步工作负荷。"
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: cfowler;mahender;glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2fd12dd32ed3c8479c7460cbc0a1cac3330ff4f4
ms.openlocfilehash: 045ec40811468eeffb25ed28ec5bfc84f074c19d
ms.lasthandoff: 03/01/2017


---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions 简介  
Azure Functions 是用于在云中轻松运行小段代码或“函数”的一个解决方案。 用户可以只编写解决现有问题所需的代码，而无需担心要运行该代码的整个应用程序或基础结构。 Functions 可使开发更有效率，并可以使用自己所选的开发语言，如 C#、F#、Node.js、Python 或 PHP。 只需为代码运行的时间付费，并可信任 Azure 会根据需要进行调整。 使用 Azure Functions，可在 Microsoft Azure 上开发无服务器应用程序。

本主题提供有关 Azure Functions 的高级概述。 如果要立即投入和开始使用 Azure Functions，请从 [创建第一个 Azure 函数](functions-create-first-azure-function.md)开始。 如果要查找有关 Functions 的更多技术信息，请参阅 [开发人员参考](functions-reference.md)。

## <a name="features"></a>功能
下面是 Azure Functions 的一些主要功能：

* **所选语言** - 可使用 C#、F#、Node.js、Python、PHP、批处理、bash 或任何可执行文件编写函数。
* **按使用付费定价模型** - 仅为运行代码所用的时间付费。 请参阅[定价部分](#pricing)中的使用托管计划选项。  
* **引入自己的依赖项** - Functions 支持 NuGet 和 NPM，因此用户可以使用自己的常用库。  
* **集成安全性** - 使用 OAuth 提供程序（如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帐户）保护 HTTP 触发的函数。  
* **简化集成** - 轻松利用 Azure 服务和软件即服务 (SaaS) 产品/服务。 有关一些示例，请参阅[集成部分](#integrations)。  
* **灵活开发** - 直接在门户中编写函数代码，或者通过 GitHub、Visual Studio Team Services 和其他 [受支持的开发工具](../app-service-web/web-sites-deploy.md#deploy-using-an-ide)设置持续集成和部署代码。  
* **开放源代码** - Functions 运行时是一个开放源代码， [可在 GitHub 上找到](https://github.com/azure/azure-webjobs-sdk-script)。  

## <a name="what-can-i-do-with-functions"></a>使用 Functions 可以做什么？
Azure Functions 是一个理想的解决方案，用于处理数据、集成系统、使用物联网 (IoT) 以及生成简单的 API 和微服务。 对于以下任务请考虑使用 Functions：例如，图像或订单处理、文件维护，或者要按计划运行的任何任务。 

Functions 提供模板，以帮助用户从主要方案开始，包括以下模板：

* **BlobTrigger** - Azure 存储 blob 添加到容器时，处理这些 blob。 可以使用此函数调整图像大小。
* **EventHubTrigger** - 响应传送到 Azure 事件中心的事件。 在应用程序检测、用户体验或工作流处理以及物联网 (IoT) 方案中特别有用。
* **Generic webhook** - 处理来自支持 webhook 的任何服务的 webhook HTTP 请求。
* **GitHub webhook** - 响应 GitHub 存储库中发生的事件。 有关示例，请参阅 [创建 webhook 或 API 函数](functions-create-a-web-hook-or-api-function.md)。
* **HTTPTrigger** - 使用 HTTP 请求触发执行代码。
* **QueueTrigger** - 当消息到达 Azure 存储队列时，响应这些消息。 有关示例，请参阅[创建与 Azure 服务绑定的 Azure 函数](functions-create-an-azure-connected-function.md)。
* **ServiceBusQueueTrigger** - 通过侦听消息队列将代码连接到其他 Azure 服务或本地服务。 
* **ServiceBusTopicTrigger** - 通过订阅主题将代码连接到其他 Azure 服务或本地服务。 
* **TimerTrigger** - 按预定义的计划执行清除或其他批处理任务。 有关示例，请参阅 [创建事件处理函数](functions-create-an-event-processing-function.md)。

Azure Functions 支持 *触发器*（用于启动代码执行）和*绑定*（用于简化针对输入和输出数据进行的编码）。 有关 Azure Functions 提供的触发器和绑定的详细说明，请参阅 [Azure Functions 触发器和绑定开发人员参考](functions-triggers-bindings.md)。

## <a name="a-nameintegrationsaintegrations"></a><a name="integrations"></a>集成
Azure Functions 可与各种 Azure 和第三方服务集成。 这些服务可以触发函数开始执行，或者可用作代码的输入和输出。 Azure Functions 支持以下服务集成。 

* Azure DocumentDB
* Azure 事件中心 
* Azure 移动应用（表）
* Azure 通知中心
* Azure 服务总线（队列和主题）
* Azure 存储（blob、队列和表） 
* GitHub (webhook)
* 本地（使用服务总线）
* Twilio（短信）

## <a name="a-namepricingahow-much-does-functions-cost"></a><a name="pricing"></a>Functions 的费用是多少？
Azure Functions 具有以下两种定价计划，请选择最适合需要的一种： 

* **使用计划** - 用户的函数运行时，Azure 提供所有所需的计算资源。 用户不必担心资源管理，只需为自己的代码运行的时间付费。 
* **应用服务计划** - 运行函数的方式就像运行 web 应用、移动应用和 API 应用一样。 如果已对其他应用程序使用应用服务，可以按相同的计划运行自己的函数，而不用另外付费。 

完整的定价详细信息可在 [Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)中找到。 有关缩放函数的详细信息，请参阅 [如何缩放 Azure Functions](functions-scale.md)。

## <a name="next-steps"></a>后续步骤
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)  
  使用 Azure Functions 快速入门立即投入并创建第一个函数。 
* [Azure Functions 开发人员参考](functions-reference.md)  
  提供有关 Azure Functions 运行时的更多技术信息，并为编码函数及定义触发器和绑定提供参考。
* [测试 Azure Functions](functions-test-a-function.md)  
  介绍可用于测试函数的各种工具和技巧。
* [如何缩放 Azure Functions](functions-scale.md)  
  讨论 Azure Functions 提供的服务计划（包括使用托管计划）以及如何选择合适的计划。 
* [详细了解 Azure 应用服务](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions 利用 Azure 应用服务平台执行核心功能，例如部署、环境变量和诊断。 


