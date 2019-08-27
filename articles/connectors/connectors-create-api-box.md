---
title: "\"连接到\" 框-Azure 逻辑应用"
description: 使用 Box REST API 和 Azure 逻辑应用创建和管理文件
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 5fbc64194182c41a70eb27a4049234973c39fffe
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050927"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>使用 Azure 逻辑应用在 Box 中创建和管理文件

本文介绍如何使用 Box 连接器从逻辑应用内部创建和管理 Box 中的文件。 这样，你就可以创建逻辑应用来自动执行用于管理文件和其他操作的任务和工作流，例如：

* 根据从 Box 中获取的数据生成业务流。

* 创建或更新文件时触发自动化任务和工作流。

* 运行复制文件或删除文件的操作。

  当这些操作得到响应时，它们会使输出可用于其他操作。 
  例如，某个文件在 Box 上发生更改后，可以使用 Office 365 通过电子邮件发送该文件。

## <a name="prerequisites"></a>先决条件

* 一个 [Box 帐户](https://www.box.com/home)

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 要在其中访问 Box 帐户的逻辑应用。 若要通过 Box 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。
如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 OpenAPI（以前为 Swagger）文件所述），请参阅[连接器的参考页](/connectors/box/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)