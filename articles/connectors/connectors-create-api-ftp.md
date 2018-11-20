---
title: 连接到 FTP 服务器 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Azure 逻辑应用创建、监视和管理 FTP 服务器上的文件
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: a14f045193c01b8c26019314ddde4c2116d8bad6
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232811"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用创建、监视和管理 FTP 文件

使用 Azure 逻辑应用和 FTP 连接器，可以创建自动化的任务和工作流，用于通过 FTP 服务器上的帐户创建、监视、发送和接收文件以及执行其他操作，例如：

* 添加或更改文件时进行监视。
* 获取、创建、复制、更新、列出和删除文件。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器从 FTP 服务器获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的操作对 FTP 服务器上的文件执行任务。 还可以让其他操作使用来自 FTP 操作的输出。 例如，如果你定期从 FTP 服务器检索文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> 除非使用[区块处理大型消息](../logic-apps/logic-apps-handle-large-messages.md)，否则 FTP 连接器仅支持 50 MB 或更小的文件。 
>
> 此外，FTP 连接器仅支持显式 FTP over SSL (FTPS)，与隐式 FTPS 不兼容。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* FTP 主机服务器地址和帐户凭据

  FTP 连接器需要可从 Internet 访问并设置为在 passive 模式下运行的 FTP 服务器。 你的凭据授权逻辑应用创建连接并访问你的 FTP 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 FTP 帐户的逻辑应用。 若要从 FTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 FTP 操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。

## <a name="connect-to-ftp"></a>连接到 FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“ftp”作为筛选器。 在触发器列表下，选择所需的触发器。 

   -或-

   对于现有逻辑应用，请在要添加操作的最后一个步骤下，选择“新建步骤”，然后选择“添加操作”。 
   在搜索框中，输入“ftp”作为筛选器。 
   在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 (**+**)，然后选择“添加操作”。

1. 为连接提供所需的详细信息，然后选择“创建”。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="examples"></a>示例

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP 触发器：添加或修改文件时

当此触发器检测到 FTP 服务器上添加或更改了文件时，将会启动逻辑应用工作流。 例如，可以添加一个条件，用于检查文件内容，并根据该内容是否符合指定的条件，来确定是否要获取该内容。 最后可以添加一个操作，用于获取文件内容并将该内容放在 SFTP 服务器上的某个文件夹中。 

**企业示例**：可以使用此触发器监视 FTP 文件夹中表示客户订单的新文件。 然后，可以使用“获取文件内容”等 FTP 操作来获取订单内容以做进一步处理，并将该订单存储在订单数据库中。

有效且正常工作的逻辑应用需要一个触发器和至少一个操作。 因此请确保在添加触发器后添加一个操作。

以下是演示此触发器的示例：“添加或修改文件时”

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“ftp”作为筛选器。 在触发器列表下，选择以下触发器：“添加或修改字段时 - FTP”

   ![查找和选择 FTP 触发器](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. 为连接提供所需的详细信息，然后选择“创建”。

   ![创建 FTP 服务器连接](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. 选择“文件夹”框旁边的文件夹图标，列表将随即显示。 若想查找要监视其中是否有新文件或已编辑文件的文件夹，请选择右箭头 (>)，浏览到该文件夹并将其选中。

   ![查找并选择要监视的文件夹](./media/connectors-create-api-ftp/select-folder.png)  

   所选文件夹将出现在“文件夹”框中。

   ![所选的文件夹](./media/connectors-create-api-ftp/selected-folder.png)  

现在，逻辑应用已具有一个触发器，请添加在逻辑应用找到新文件或已编辑文件时要运行的操作。 对于此示例，可以添加获取新内容或更新内容的 FTP 操作。

### <a name="ftp-action-get-content"></a>FTP 操作：获取内容

添加或更新文件时，此操作将通过 FTP 服务器获取该文件的内容。 例如，可通过前面的示例添加触发器，然后添加操作，用于在添加或编辑文件后获取该文件内容。 

1. 在该触发器或其他任何操作下，选择“新建步骤”。 

1. 在搜索框中，输入“ftp”作为筛选器。 在操作列表下，选择此操作：“获取文件内容 - FTP”

   ![选择 FTP 操作](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. 如果已有到 FTP 服务器和帐户的连接，请转到下一步。 如果没有，请为连接提供所需的详细信息，然后选择“创建”。 

   ![创建 FTP 服务器连接](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. 打开“获取文件内容”操作后，在“文件”框内部单击，以便显示动态内容列表。 现可为先前步骤的输出结果选择属性。 在动态内容列表中，选择“文件内容”属性，该属性包含已添加或已更新文件的内容。  

   ![查找并选择文件](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   “文件内容”属性现将显示在“文件”框中。

   ![所选的“文件内容”属性](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. 保存逻辑应用。 要测试工作流，请将文件添加到逻辑应用现在监视的 FTP 文件夹中。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/ftpconnector/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)