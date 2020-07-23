---
title: XML 消息和平面文件
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中处理、验证和转换 XML 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74792149"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>带有 Enterprise Integration Pack 的 Azure 逻辑应用中的 XML 消息和平面文件

在 [Azure 逻辑应用](logic-apps-overview.md)中，你可以使用 Enterprise Integration Pack 处理你发送和接收的 XML 消息。 如果你使用过 BizTalk Server，你会看到 Enterprise Integration Pack 提供了类似的功能来转换和验证消息、使用平面文件，甚至可以使用 XPath 扩展或提取消息中的特定属性。 如果你不熟悉此空间，这些功能扩展了你在逻辑应用的工作流中处理消息的方式。 例如，如果你采用了企业到企业 (B2B) 方案和特定的 XML 架构，则可使用 Enterprise Integration Pack 来增强公司处理这些消息的能力。

例如，Enterprise Integration Pack 包括以下功能：

* [XML 验证](logic-apps-enterprise-integration-xml-validation.md)：针对特定架构验证传入或传出 XML 消息。

* [XML 转换](logic-apps-enterprise-integration-transform.md)：使用映射根据你的要求或合作伙伴的要求转换或自定义 XML 消息。

* [平面文件编码和平面文件解码](logic-apps-enterprise-integration-flatfile.md)：对平面文件进行编码或解码。

  例如，SAP 接受和发送平面文件格式的 IDOC 文件。 许多集成平台（包括逻辑应用）创建 XML 消息。 因此，可以创建一个逻辑应用，以便使用平面文件编码器将 XML 文件“转换”成平面文件。

* [XPath](workflow-definition-language-functions-reference.md#xpath)：扩充消息以及从消息中提取特定属性。 然后，可以使用提取的属性将消息路由到目标或中间终结点。

## <a name="sample"></a>示例

使用 Azure 逻辑应用中的 XML 功能[部署完全正常运行的逻辑应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)（GitHub 示例）。

## <a name="next-steps"></a>后续步骤

详细了解 [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
