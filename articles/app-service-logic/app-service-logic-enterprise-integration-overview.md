---
title: "企业集成概述 | Microsoft Docs"
description: "使用企业集成的功能可实现使用逻辑应用的业务流程和集成方案"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 4faf01ca10f263a5ecc18f51659b5004bf4f7c36
ms.openlocfilehash: 3ca870da1a01bc216604cdd1142ba68dfd5e96e2


---
# <a name="overview-of-the-enterprise-integration-pack"></a>Enterprise Integration Pack 的概述
## <a name="what-is-the-enterprise-integration-pack"></a>什么是 Enterprise Integration Pack？
Enterprise Integration Pack 是 Microsoft 基于云的解决方案，用于无缝地启用企业到企业 (B2B) 通信。 该包使用行业标准协议（包括 [AS2](app-service-logic-enterprise-integration-as2.md)、[X12](app-service-logic-enterprise-integration-x12.md) 和 [EDIFACT](app-service-logic-enterprise-integration-edifact.md)）在业务合作伙伴之间交换消息。 可以选择使用加密和数字签名保护消息。 

该包通过将不同格式转换为两个组织的系统都可以解释并对其执行操作的格式，使使用不同协议和格式的组织可以通过电子方式交换消息。 

如果你熟悉 BizTalk Server 或 Microsoft Azure BizTalk 服务，则会发现可轻松地使用企业集成功能，因为大多数概念是类似的。 一个主要区别是企业集成使用集成帐户来简化 B2B 通信中使用的项目的存储和管理。 

在体系结构方面，Enterprise Integration Pack 基于**集成帐户**，这些帐户存储可以用于设计、部署和维护 B2B 应用的所有项目。 集成帐户基本上是基于云的容器，可以在其中存储各个项目（如架构、合作伙伴、证书、映射和协议）。 这些项目随后可以在逻辑应用中用于构建 B2B 工作流。 需要先将集成帐户链接到逻辑应用，然后才能在逻辑应用中使用这些项目。 链接它们之后，逻辑应用有权访问集成帐户的项目。  

## <a name="why-should-you-use-enterprise-integration"></a>为什么应使用企业集成？
* 借助企业集成，能够将所有项目存储在一个位置，即集成帐户。 
* 可以利用逻辑应用引擎及其所有连接器来构建 B2B 工作流并与第三方 SaaS 应用程序、本地应用程序以及自定义应用程序集成
* 还可以利用 Azure Functions

## <a name="how-to-get-started-with-enterprise-integration"></a>如何开始使用企业集成？
可以通过 **Azure 门户**上的逻辑应用设计器来使用 Enterprise Integration Pack 构建和管理 B2B 应用。  

还可以使用 [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "逻辑应用 PowerShell 主题") 来管理逻辑应用。 

以下是可以在 Azure 门户中创建应用之前需要执行的步骤概述：![概述图像](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>一些常见方案有哪些？
企业集成支持以下这些行业标准：   

* EDI - 电子数据交换  
* EAI - 企业应用程序集成  

## <a name="heres-what-you-need-to-get-started"></a>以下是开始使用需要满足的条件
* 具有集成帐户的 Azure 订阅
* Visual Studio 2015，用于创建映射和架构
* [Visual Studio 2015 2.0 的 Microsoft Azure 逻辑应用企业集成工具](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>试用
[立即试用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)以部署使用逻辑应用 B2B 功能的完全可操作的示例 AS2 发送和接收逻辑应用。

## <a name="learn-more-about"></a>了解有关以下方面的详细信息：
* [协议](app-service-logic-enterprise-integration-agreements.md "了解企业集成协议")
* [企业到企业 (B2B) 方案](app-service-logic-enterprise-integration-b2b.md "了解如何使用 B2B 功能创建逻辑应用")  
* [证书](app-service-logic-enterprise-integration-certificates.md "了解企业集成证书")
* [平面文件编码/解码](app-service-logic-enterprise-integration-flatfile.md "了解如何对平面文件内容进行编码和解码")  
* [集成帐户](app-service-logic-enterprise-integration-accounts.md "了解集成帐户")
* [映射](app-service-logic-enterprise-integration-maps.md "了解企业集成映射")
* [合作伙伴](app-service-logic-enterprise-integration-partners.md "了解企业集成合作伙伴")
* [架构](app-service-logic-enterprise-integration-schemas.md "了解企业集成架构")
* [XML 消息验证](app-service-logic-enterprise-integration-xml.md "了解如何使用逻辑应用验证 XML 消息")
* [XML 转换](app-service-logic-enterprise-integration-transform.md "了解企业集成映射")
* [企业集成连接器](../connectors/apis-list.md "了解 Enterprise Integration Pack 连接器")
* [集成帐户元数据](app-service-logic-enterprise-integration-metadata.md "了解集成帐户元数据")
* [监视 B2B 消息](app-service-logic-monitor-b2b-message.md "深入了解 B2B 消息")
* [在 OMS 门户中跟踪 B2B 消息](app-service-logic-track-b2b-messages-omsportal.md "深入了解在 OMS 门户中跟踪 B2B 消息")




<!--HONumber=Nov16_HO4-->


