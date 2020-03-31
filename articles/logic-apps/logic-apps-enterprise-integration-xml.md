---
title: XML 消息和平面文件
description: 使用企业集成包在 Azure 逻辑应用中处理、验证和转换 XML 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792149"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>带有 Enterprise Integration Pack 的 Azure 逻辑应用中的 XML 消息和平面文件

在[Azure 逻辑应用中](logic-apps-overview.md)，可以使用企业集成包处理发送和接收的 XML 消息。 如果您使用了 BizTalk 服务器，企业集成包提供了类似的功能来转换和验证邮件、处理平面文件，甚至使用 XPath 来丰富或从邮件中提取特定属性。 如果您是此空间的新增功能，这些功能将扩展逻辑应用工作流中处理消息的方式。 例如，如果您有企业对企业 （B2B） 方案并使用特定的 XML 架构，则可以使用企业集成包来增强公司处理这些消息的方式。

例如，企业集成包包括以下功能：

* [XML 验证](logic-apps-enterprise-integration-xml-validation.md)：根据特定架构验证传入或传出的 XML 消息。

* [XML 转换](logic-apps-enterprise-integration-transform.md)：使用映射根据您的要求或合作伙伴的要求转换或自定义 XML 消息。

* [平面文件编码和平面文件解码](logic-apps-enterprise-integration-flatfile.md)：编码或解码平面文件。

  例如，SAP 接受和发送平面文件格式的 IDOC 文件。 许多集成平台（包括逻辑应用）创建 XML 消息。 因此，可以创建一个逻辑应用，以便使用平面文件编码器将 XML 文件“转换”成平面文件。

* [XPath](workflow-definition-language-functions-reference.md#xpath)： 丰富消息并从消息中提取特定属性。 然后，可以使用提取的属性将消息路由到目标或中间终结点。

## <a name="sample"></a>示例

通过使用 Azure 逻辑应用中的 XML 功能[部署完全运行的逻辑应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)（GitHub 示例）。

## <a name="next-steps"></a>后续步骤

了解有关[企业集成包的更多](logic-apps-enterprise-integration-overview.md)
