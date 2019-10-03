---
title: 用于 B2B 企业集成的 XML 消息 - Azure 逻辑应用 | Microsoft Docs
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中针对 B2B 解决方案处理、验证、转换和扩展 XML 消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996556"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>带有 Enterprise Integration Pack 的 Azure 逻辑应用中的 XML 消息和平面文件

使用逻辑应用可以处理发送和接收的 XML 消息。 此功能是 Enterprise Integration Pack 随附的。 对于那些具有 BizTalk Server 使用背景的用户来说，Enterprise Integration Pack 提供的功能是类似的，可以转换和验证消息、使用平面文件，甚至可以使用 XPath 扩展或提取消息中的特定属性。 

对于不熟悉该空间的那些用户来说，这些功能扩展的是用户如何在工作流中处理消息。 例如，如果使用的是企业到企业方案和特定的 XML 架构，则可使用 Enterprise Integration Pack 增强公司处理这些消息的能力。 

Enterprise Integration Pack 包括： 

* [XML 验证](logic-apps-enterprise-integration-xml-validation.md "了解 XML 消息验证") - 针对特定架构验证传入或传出 XML 消息。
* [XML 转换](../logic-apps/logic-apps-enterprise-integration-transform.md "了解 XML 消息转换和映射") - 基于用户的要求或合作伙伴的要求转换或自定义 XML 消息。
* [平面文件编码和平面文件解码](logic-apps-enterprise-integration-flatfile.md "了解平面文件编码/解码") - 对平面文件进行编码或解码。 例如，SAP 接受和发送平面文件格式的 IDOC 文件。 许多集成平台（包括逻辑应用）创建 XML 消息。 因此，可以创建一个逻辑应用，以便使用平面文件编码器将 XML 文件“转换”成平面文件。 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - 扩充消息以及从消息中提取特定属性。 然后，可以使用提取的属性将消息路由到目标或中间终结点。

## <a name="try-it-out"></a>试用
[部署完全正常运行的逻辑应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)（GitHub 示例） 通过 Azure 逻辑应用中使用的 XML 功能。

## <a name="learn-more"></a>了解详细信息
[了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")
