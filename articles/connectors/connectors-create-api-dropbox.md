---
title: 连接到 Dropbox
description: 使用 Dropbox REST API 和 Azure 逻辑应用上传和管理文件
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: b38f9b9626e5eec0272256aacc71f7503a006dd6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789819"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用上传和管理 Dropbox 中的文件

通过 Dropbox 连接器和 Azure 逻辑应用，你可以创建自动工作流，以在 Dropbox 帐户中上传和管理文件。 

本文介绍如何从逻辑应用连接到 Dropbox，然后在**创建文件时**添加 dropbox，并**使用 path 操作获取文件内容**。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个[Dropbox 帐户](https://www.dropbox.com/)，可以免费注册。 你的帐户凭据是在逻辑应用和 Dropbox 帐户之间创建连接所必需的。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 对于本示例，需要一个空白逻辑应用。

## <a name="add-trigger"></a>添加触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 在搜索框下，选择“所有”。 在搜索框中，输入“dropbox”作为筛选器。
在触发器列表中选择以下触发器：“创建文件时”

   ![选择 Dropbox 触发器](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. 使用 Dropbox 帐户凭据登录，并授权 Azure 逻辑应用访问你的 Dropbox 数据。

1. 提供触发器的所需信息。 

   在此示例中，选择要跟踪文件创建的文件夹。 若要浏览文件夹，请选择 "**文件夹**" 框旁边的文件夹图标。

## <a name="add-action"></a>添加操作

现在，添加一个从任何新文件获取内容的操作。

1. 在触发器下，选择“下一步”。 

1. 在搜索框下，选择“所有”。 在搜索框中，输入“dropbox”作为筛选器。
从 "操作" 列表中，选择此操作：**使用路径获取文件内容**

1. 如果尚未授权 Azure 逻辑应用访问 Dropbox，请立即授予访问权限。

1. 若要浏览到要使用的文件路径，请在 "**文件路径**" 框旁边，选择省略号（ **...** ）按钮。 

   您也可以在 "**文件路径**" 框中单击，然后从 "动态内容" 列表中选择 "**文件路径**"，其值可用作上一部分中添加的触发器的输出。

1. 完成后，保存逻辑应用。

1. 若要触发逻辑应用，请在 Dropbox 中创建新的文件。

## <a name="connector-reference"></a>连接器参考

如连接器的 OpenAPI （以前的 Swagger）文件所述的技术详细信息（如触发器、操作和限制），请参阅[连接器的参考页](/connectors/dropbox/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
