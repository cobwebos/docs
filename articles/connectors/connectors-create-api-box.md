---
title: 连接到 Box - Azure 逻辑应用 | Microsoft Docs
description: 使用 Box REST API 和 Azure 逻辑应用创建和管理文件
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: aef9423a7281f85029195156a1563d9cfed3029c
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229056"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>使用 Azure 逻辑应用在 Box 中创建和管理文件

本文介绍如何使用 Box 连接器从逻辑应用内部创建和管理 Box 中的文件。 这样，你就可以创建逻辑应用来自动执行用于管理文件和其他操作的任务和工作流，例如：

* 根据从 Box 中获取的数据生成业务流。 

* 创建或更新文件时触发自动化任务和工作流。

* 运行复制文件、删除文件等操作。 

  当这些操作得到响应时，它们会使输出可用于其他操作。 
  例如，某个文件在 Box 上发生更改后，可以使用 Office 365 通过电子邮件发送该文件。

## <a name="prerequisites"></a>先决条件

* 一个 [Box 帐户](https://www.box.com/home)

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 要在其中访问 Box 帐户的逻辑应用。 若要通过 Box 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。
如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/box/)。 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)