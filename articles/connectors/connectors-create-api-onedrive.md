---
title: 访问和管理 Microsoft OneDrive 中的文件
description: 通过在 Azure 逻辑应用中创建自动化工作流，在 OneDrive 中上载和管理文件
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 9fe4988b5499943f4b11ec5d640209ceb68e84ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87040229"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用访问和管理 OneDrive 连接器中的文件

通过使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 和 [OneDrive 连接器](/connectors/onedriveconnector/)，你可以创建自动化任务和工作流来管理文件，包括上传、获取、删除文件等。 利用 OneDrive，你可以执行以下任务：

* 通过在 OneDrive 中存储文件生成工作流，或更新 OneDrive 中的现有文件。 
* 在 OneDrive 内创建或更新文件时，使用触发器启动工作流。
* 使用创建文件、删除文件等操作。 例如，收到带有附件的新 Office 365 电子邮件时（触发器），在 OneDrive 中新建文件（操作）。

本文演示如何在逻辑应用中使用 OneDrive 连接器，还列出了触发器和操作。

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../logic-apps/logic-apps-overview.md)和[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="connect-to-onedrive"></a>连接到 OneDrive

在逻辑应用能够访问任何服务前，需要先创建到该服务的*连接*。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 OneDrive，首先需要 OneDrive *连接*。 若要创建连接，请输入通常用于访问要连接到的服务的凭据。 因此，在 OneDrive 中，将凭据输入到 OneDrive 帐户以创建连接。

### <a name="create-the-connection"></a>创建连接

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>使用触发器

触发器是用于启动在逻辑应用中定义的工作流的事件。 触发器以希望的间隔和频率“轮询”服务。 [了解有关触发器的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 在逻辑应用设计器中，键入 `onedrive` 以获取触发器列表：  

   ![标题为 "显示 Microsoft 托管 A P I" 的对话框包含一个包含 "onedrive" 的框。 下面是四个触发器的列表。 其中第一个是 "OneDrive-创建文件时"。 选择第二个 "OneDrive-修改文件时"。](./media/connectors-create-api-onedrive/onedrive-1.png)

2. 选择“修改文件时”****。 如果连接已存在，选择“显示选取器”按钮选择文件夹。

   ![标题为 "修改文件时" 的对话框有一个标题为 "文件夹" 的框，其中包含关联的浏览按钮。](./media/connectors-create-api-onedrive/sample-folder.png)

   如果提示登录，则输入登录详细信息以创建连接。 本文中的[创建连接](connectors-create-api-onedrive.md#create-the-connection)列出了相关步骤。

   在此示例中，逻辑应用在更新所选文件夹中的文件时运行。 要查看此触发器的结果，请添加另一个向你发送电子邮件的操作。 例如，添加在更新文件时向你发送电子邮件的 Office 365 Outlook“发送电子邮件”操作**。

3. 选择“编辑”**** 按钮并设置“频率”**** 和“间隔”**** 值。 例如，如果希望触发器每 15 分钟轮询一次，将“频率”**** 设置为“分钟”****，将“间隔”**** 设置为“15”****。 

   ![标题为 "修改文件时" 的对话框显示标记为 "文件夹"、"频率"、"间隔"、"时区" 和 "开始时间" 的五个框。 "频率" 和 "时区" 字段有下拉列表。](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **保存**更改（工具栏的左上角）。 逻辑应用将保存，并且可能自动启用。

## <a name="use-an-action"></a>使用操作

操作是指在逻辑应用中定义的工作流所执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 选择加号。 可看到多个选项：“添加操作”****、“添加条件”**** 或“更多”**** 选项之一。

   ![屏幕截图显示四个按钮： "+ 新建步骤"、"添加操作"、"添加条件" 和 ".。。更多 "。](./media/connectors-create-api-onedrive/add-action.png)

2. 选择“添加操作”****。

3. 在 "搜索" 框中，键入 `onedrive` 以获取所有可用操作的列表。

   ![标题为 "显示 Microsoft 托管 A P I" 的对话框包含一个包含 "onedrive" 的框。 下面是八个操作的列表。 第一个是 "OneDrive-创建文件"，并且已选中。](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. 在我们的示例中，选择“OneDrive - 创建文件”****。 如果连接已存在，选择“文件夹路径”**** 放入文件、输入“文件名”****，并选择所需的“文件内容”****：  

   ![标题为 "创建文件" 的对话框显示了三个标签为 "文件夹路径"、"文件名" 和 "文件夹内容" 的框。 "文件夹路径" 框旁边有一个目录浏览按钮。](./media/connectors-create-api-onedrive/sample-action.png)

   如果系统提示输入连接信息，请按照本主题中所述，输入详细信息以 [创建连接](#create-the-connection) 。

   在此示例中，将在 OneDrive 文件夹中创建一个新文件。 可使用来自其他触发器的输出创建 OneDrive 文件。 例如，添加 Office 365 Outlook“新电子邮件到达时”** 触发器。 然后添加可在 ForEach 内使用“附件”和“内容类型”字段的 OneDrive“创建文件”操作，在 OneDrive 中新建文件**。

   ![标题为 "For each" 的对话框有一个名为 "选择前面步骤的输出"，其中包含 "附件" 的框。 有一个 "创建文件" 对话框，其中包含 "For each" 框的其余部分，其中的框标记为 "文件夹路径"、"文件名" 和 "文件内容"。 ](./media/connectors-create-api-onedrive/foreach-action.png)

5. **保存**更改（工具栏的左上角）。 逻辑应用将保存，并且可能自动启用。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/onedriveconnector/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="next-steps"></a>后续步骤

* [适用于 Azure 逻辑应用的连接器](apis-list.md)