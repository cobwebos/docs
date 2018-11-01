---
title: 从 Azure 逻辑应用连接到 SFTP 帐户 | Microsoft Docs
description: 使用 Azure 逻辑应用自动完成用于监视、创建、管理、发送和接收 SFTP 服务器文件的任务与工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: 2250c6952aeac7b10dcb1a1a35419941e5cad507
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233202"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>使用 Azure 逻辑应用和 SFTP-SSH 连接器监视、创建和管理 SFTP 文件

使用 Azure 逻辑应用和 SFTP-SSH 连接器，可以创建一个自动化的任务和工作流，用于通过 [SFTP](https://www.ssh.com/ssh/sftp/) 服务器上的帐户监视、创建、发送和接收文件，以及执行其他操作，例如：

* 添加或更改文件时进行监视。
* 获取、创建、复制、重命名、更新、列出和删除文件。
* 创建文件夹。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器从 SFTP 服务器获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的操作对 SFTP 服务器上的文件执行任务。 还可以让其他操作使用来自 SFTP 操作的输出。 例如，如果你定期从 SFTP 服务器检索文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件。
如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>SFTP-SSH 与 SFTP

下面是 SFTP-SSH 连接器与 [SFTP](../connectors/connectors-create-api-sftp.md) 连接器之间的几个关键区别。 SFTP-SSH 连接器提供以下功能：

* 使用 <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET**</a> 库，该库是用于 .NET 的开放源代码 Secure Shell (SSH) 库。

* 提供对高达 1 GB 的大型文件的支持。 该连接器可以读取或写入大小最大为 1 GB 的文件。

* 提供“创建文件夹”操作，用于在 SFTP 服务器上的指定路径中创建文件夹。

* 提供“重命名文件”操作，用于在 SFTP 服务器上重命名文件。

* 缓存与 SFTP 服务器的连接，用于提高性能并减少服务器上的连接尝试次数。 

  通过设置 SFTP 服务器上的 <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> 属性可以控制用于缓存连接的持续时间。 

## <a name="how-trigger-polling-works"></a>触发器轮询的工作原理

SFTP-SSH 触发器的工作原理是轮询 SFTP 文件系统并查找自上次轮询后已更改的任何文件。 通过某些工具可以在文件发生更改时保留时间戳，因此在这些情况下，需要禁用此功能才能使你的触发器正常工作。 下面是一些常见设置：

| SFTP 客户端 | 操作 | 
|-------------|--------| 
| Winscp | 选项 → 首选项... → 传输 → 编辑... → 保留时间戳 → 禁用 |
| FileZilla | 传输 → 保留已传输文件的时间戳 → 禁用 | 
||| 

当触发器找到新文件时，会检查该新文件是否完整，以及是否未部分写入。 例如，当触发器检查文件服务器时，可能正在更改某个文件。 为了避免返回部分写入的文件，该触发器会记录具有最近更改的文件的时间戳，但不会立即返回该文件。 仅当再次轮询服务器时，触发器才会返回该文件。 有时，此行为可能会导致延迟，长达触发器轮询间隔的两倍。 

请求文件内容时，触发器不会检索大于 50 MB 的文件。 若要选取大于 50 MB 的文件，请遵循此模式：

* 使用可返回文件属性的触发器，如“添加或修改文件时(仅属性)”。 
* 按照触发器执行读取完整文件的操作，如“使用路径获取文件内容”。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 你的 SFTP 主机服务器地址和帐户凭据，用于授权逻辑应用创建连接并访问你的 SFTP 帐户。

  按照多行格式将 SSH 私钥的完整内容复制并粘贴到“SSH 私钥”属性。 
  下面是展示如何通过使用 Notepad.exe 提供 SSH 私钥的示例步骤：
    
  1. 在 Notepad.exe 中打开 SSH 私钥文件
  2. 在“编辑”菜单上，选择“全选”。
  3. 选择“编辑” > “复制”。
  4. 创建连接时，在“SSH 私钥”属性中粘贴密钥。 请勿手动编辑“SSH 私钥”属性。

     连接器使用 SSH.NET 库，该库支持以下 SSH 私钥格式并且仅支持 MD5 指纹：

     * RSA 
     * DSA

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
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)