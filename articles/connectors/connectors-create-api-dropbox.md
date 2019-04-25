---
title: 连接到 Dropbox 的 Azure 逻辑应用
description: 使用 Dropbox REST API 和 Azure 逻辑应用上传和管理文件
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312523"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>上传和管理文件在 Dropbox 中的使用 Azure 逻辑应用

使用 Dropbox 连接器和 Azure 逻辑应用，可以创建自动化工作流，上传和管理你的 Dropbox 帐户中的文件。 

本文介绍如何连接到 Dropbox，从逻辑应用，以及如何将 Dropbox**时创建的文件**触发器和 Dropbox**使用路径获取文件内容**操作。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 一个 [Dropbox 帐户](https://www.dropbox.com/)，可以在注册后免费获取。 帐户凭据是在逻辑应用和 Dropbox 帐户之间创建连接所必需的。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 对于本示例，需要一个空白逻辑应用。

## <a name="add-trigger"></a>添加触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 在搜索框下，选择“所有”。 在搜索框中，输入“dropbox”作为筛选器。
从触发器列表中选择此触发器：**创建文件时**

   ![选择 Dropbox 触发器](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. 使用 Dropbox 帐户凭据登录，并授权 Azure 逻辑应用访问你的 Dropbox 数据。

1. 提供触发器的所需信息。 

   在此示例中，选择想要跟踪文件创建的文件夹。 若要浏览您的文件夹，选择文件夹图标旁**文件夹**框。

## <a name="add-action"></a>添加操作

现在，添加任何新文件中获取的内容的操作。

1. 在触发器下，选择“下一步”。 

1. 在搜索框下，选择“所有”。 在搜索框中，输入“dropbox”作为筛选器。
在操作列表中选择此操作：**使用路径获取文件内容**

1. 如果已尚未获得授权访问 Dropbox 的 Azure 逻辑应用，现在授予访问权限。

1. 若要浏览到你想要使用，旁边的文件路径**文件路径**框中，选择省略号 (**...**) 按钮。 

   此外可以单击内**文件路径**框中，并从动态内容列表中，选择**文件路径**，其值是可用作输出从上一节中添加的触发器。

1. 完成后，保存逻辑应用。

1. 若要触发逻辑应用，请在 Dropbox 中创建新文件。

## <a name="connector-reference"></a>连接器参考

有关技术详细信息，如触发器、 操作和限制，如所述的连接器的 OpenAPI (以前称为 Swagger) 文件，请参阅[连接器的参考页](/connectors/dropbox/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
