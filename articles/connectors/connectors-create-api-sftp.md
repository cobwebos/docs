---
title: 从 Azure 逻辑应用连接到 SFTP 帐户 | Microsoft Docs
description: 使用 Azure 逻辑应用自动完成用于监视、创建、管理、发送和接收 SFTP 服务器文件的任务与工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/11/2018
ms.openlocfilehash: 77a76aa4fbb051e5999053279798c1b0147ae8e9
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166774"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用监视、创建和管理 SFTP 文件

使用 Azure 逻辑应用和 SFTP 连接器，可以创建一个自动化的任务和工作流，用于通过 [SFTP](https://www.ssh.com/ssh/sftp/) 服务器上的帐户监视、创建、发送和接收文件，以及执行其他操作，例如：

* 添加或更改文件时进行监视。
* 获取、创建、复制、更新、列出和删除文件。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器从 SFTP 服务器获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的操作对 SFTP 服务器上的文件执行任务。 还可以让其他操作使用来自 SFTP 操作的输出。 例如，如果你定期从 SFTP 服务器检索文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> 对于大小在 50 MB 到 1 GB 之间的文件，请使用 [SFTP-SSH 连接器](../connectors/connectors-sftp-ssh.md)。 除非使用[区块处理大型消息](../logic-apps/logic-apps-handle-large-messages.md)，否则 SFTP 连接器仅支持 50 MB 或更小的文件。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* SFTP 主机服务器地址和帐户凭据

   你的凭据授权逻辑应用创建连接并访问你的 SFTP 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SFTP 帐户的逻辑应用。 若要从 SFTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SFTP 操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。

## <a name="connect-to-sftp"></a>连接到 SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“sftp”作为筛选器。 在触发器列表下，选择所需的触发器。 

   -或-

   对于现有逻辑应用，请在要添加操作的最后一个步骤下，选择“新建步骤”。 
   在搜索框中，输入“sftp”作为筛选器。 
   在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 (**+**)，然后选择“添加操作”。

1. 为连接提供所需的详细信息，然后选择“创建”。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="examples"></a>示例

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP 触发器：添加或修改文件时

当此触发器检测到 SFTP 服务器上添加或更改了文件时，将会启动逻辑应用工作流。 例如，可以添加一个条件，用于检查文件内容，并根据该内容是否符合指定的条件，来确定是否要获取该内容。 最后可以添加一个操作，用于获取文件内容并将该内容放在 SFTP 服务器上的某个文件夹中。 

**企业示例**：可以使用此触发器监视 SFTP 文件夹中表示客户订单的新文件。 然后，可以使用“获取文件内容”等 SFTP 操作来获取订单内容以做进一步处理，并将该订单存储在订单数据库中。

### <a name="sftp-action-get-content"></a>SFTP 操作：获取内容

此操作从 SFTP 服务器上的文件中获取内容。 例如，可以在前面的示例中添加触发器，并添加文件内容必须符合的条件。 如果条件为 true，则可以运行获取内容的操作。 

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/sftpconnector/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)