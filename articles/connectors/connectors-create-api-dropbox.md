---
title: 连接到 Dropbox
description: 使用 Azure 逻辑应用自动执行上载和管理 Dropbox 中文件的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665745"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 Dropbox 中上载和管理文件

使用 Dropbox 连接器和 Azure 逻辑应用，您可以创建自动工作流来上载和管理 Dropbox 帐户中的文件。 

本文演示如何从逻辑应用连接到 Dropbox，然后在**创建文件触发器时**添加 Dropbox，以及**使用路径操作获取文件内容**的 Dropbox 获取文件内容。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个 [Dropbox 帐户](https://www.dropbox.com/)，可以在注册后免费获取。 帐户凭据是在逻辑应用和 Dropbox 帐户之间创建连接所必需的。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 对于本示例，需要一个空白逻辑应用。

## <a name="add-trigger"></a>添加触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 在搜索框下，选择“所有”****。 在搜索框中，输入“dropbox”作为筛选器。
在触发器列表中选择以下触发器：“创建文件时”****

   ![选择 Dropbox 触发器](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. 使用 Dropbox 帐户凭据登录，并授权 Azure 逻辑应用访问你的 Dropbox 数据。

1. 提供触发器的所需信息。 

   在此示例中，选择要跟踪文件创建的文件夹。 要浏览文件夹，请选择 **"文件夹**"框旁边的文件夹图标。

## <a name="add-action"></a>添加操作

现在添加从任何新文件获取内容的操作。

1. 在触发器下，选择“下一步”。**** 

1. 在搜索框下，选择“所有”****。 在搜索框中，输入“dropbox”作为筛选器。
从操作列表中选择此操作：**使用路径获取文件内容**

1. 如果尚未授权 Azure 逻辑应用访问 Dropbox，则立即授权访问。

1. 要浏览到要使用的文件路径，请选择省略号 （**...）** 按钮旁边的文件**路径**。 

   您还可以在 **"文件路径**"框中单击，并从动态内容列表中选择 **"文件路径**"，其值可从上一节中添加的触发器的输出中获取。

1. 完成后，保存逻辑应用。

1. 要触发逻辑应用，请在 Dropbox 中创建新文件。

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/dropbox/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
