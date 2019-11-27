---
title: 连接到 FTP 服务器-Azure 逻辑应用
description: 使用 Azure 逻辑应用创建、监视和管理 FTP 服务器上的文件
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ac6ae1a3b00a4e7568bd7967105f202fbf2e4f9b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547490"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用创建、监视和管理 FTP 文件

使用 Azure 逻辑应用和 FTP 连接器，可以创建自动化的任务和工作流，用于通过 FTP 服务器上的帐户创建、监视、发送和接收文件以及执行其他操作，例如：

* 添加或更改文件时进行监视。
* 获取、创建、复制、更新、列出和删除文件。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器从 FTP 服务器获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的运行操作对 FTP 服务器上的文件进行管理。 还可以让其他操作使用来自 FTP 操作的输出。 例如，如果你定期从 FTP 服务器获取文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>限制

* FTP 连接器仅支持显式 FTP over SSL (FTPS)，与隐式 FTPS 不兼容。

* 默认情况下，FTP 操作可以读取或写入 *50 MB 或更小*的文件。 若要处理大于 50 MB 的文件，FTP 操作支持[消息分块](../logic-apps/logic-apps-handle-large-messages.md)。 **获取文件内容**操作隐式使用分块。

* FTP 触发器不支持分块。 请求文件内容时，触发器仅选择 50 MB 或更小的文件。 若要获取大于 50 MB 的文件，请遵循以下模式：

  * 使用返回文件属性的 FTP 触发器，如“添加或修改文件时(仅属性)”。

  * 跟随触发器执行 FTP **获取文件内容**操作，该操作读取完整文件并隐式使用分块。

## <a name="how-ftp-triggers-work"></a>FTP 触发器的工作原理

FTP 触发器的工作原理是轮询 FTP 文件系统并查找自上次轮询后已更改的任何文件。 某些工具允许保留文件更改时的时间戳。 在这种情况下，必须禁用此功能才能让触发器正常工作。 下面是一些常见设置：

| SFTP 客户端 | 操作 |
|-------------|--------|
| Winscp | 转到“选项” **“首选项”** “传输” > “编辑” **“保留时间戳”** “禁用” >  >  >  >  |
| FileZilla | 转到“传输” **“保留已传输文件的时间戳”** “禁用” >  >  |
|||

当触发器找到新文件时，会检查该新文件是否完整，以及是否未部分写入。 例如，当触发器检查文件服务器时，可能正在更改某个文件。 为了避免返回部分写入的文件，该触发器会记录具有最近更改的文件的时间戳，但不会立即返回该文件。 仅当再次轮询服务器时，触发器才会返回该文件。 有时，此行为可能会导致延迟，长达触发器轮询间隔的两倍。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* FTP 主机服务器地址和帐户凭据

  FTP 连接器需要可从 Internet 访问并设置为在 passive 模式下运行的 FTP 服务器。 你的凭据允许逻辑应用创建连接并访问你的 FTP 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 FTP 帐户的逻辑应用。 若要从 FTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 FTP 操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。

## <a name="connect-to-ftp"></a>连接到 FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“ftp”作为筛选器。 在触发器列表下，选择所需的触发器。

   -或-

   对于现有逻辑应用，请在要添加操作的最后一个步骤下，选择“新建步骤”，然后选择“添加操作”。 在搜索框中，输入“ftp”作为筛选器。 在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 依次选择出现的加号 ( **+** ) 和“添加操作”。

1. 为连接提供所需的详细信息，然后选择“创建”。

1. 为所选触发器或操作提供必要详细信息，并继续构建逻辑应用的工作流。

## <a name="examples"></a>示例

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP 触发器：添加或修改文件时

当此触发器检测到 FTP 服务器上添加或更改了文件时，将会启动逻辑应用工作流。 例如，可以添加一个条件，用于检查文件内容，并根据该内容是否符合指定的条件，来确定是否要获取该内容。 最后可以添加一个操作，用于获取文件内容并将该内容放在 SFTP 服务器上的某个文件夹中。

**企业示例**：可以使用此触发器监视 FTP 文件夹中的新文件来描述客户订单。 然后，可以使用“获取文件内容”等 FTP 操作来获取订单内容以做进一步处理，并将该订单存储在订单数据库中。

以下是演示此触发器的示例：“添加或修改文件时”

1. 登录 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“ftp”作为筛选器。 在触发器列表下，选择以下触发器：“添加或修改字段时 - FTP”

   ![查找和选择 FTP 触发器](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. 为连接提供所需的详细信息，然后选择“创建”。

   默认情况下，此连接器将以文本格式传输文件。 若要以二进制格式传输文件，例如，在使用了编码的情况下，请选择“二进制传输”。

   ![创建 FTP 服务器连接](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. 选择“文件夹”框旁边的文件夹图标，列表将随即显示。 若想查找要监视其中是否有新文件或已编辑文件的文件夹，请选择右箭头 ( **)，浏览到该文件夹并将其选中>** 。

   ![查找并选择要监视的文件夹](./media/connectors-create-api-ftp/select-folder.png)  

   所选文件夹将出现在“文件夹”框中。

   ![所选的文件夹](./media/connectors-create-api-ftp/selected-folder.png)  

现在，逻辑应用已具有一个触发器，请添加在逻辑应用找到新文件或已编辑文件时要运行的操作。 对于此示例，可以添加获取新内容或更新内容的 FTP 操作。

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP 操作：获取内容

添加或更新文件时，此操作将通过 FTP 服务器获取该文件的内容。 例如，可通过前面的示例添加触发器，然后添加操作，用于在添加或编辑文件后获取该文件内容。

以下示例演示了此操作：**获取内容**

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

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看[连接器的参考页](/connectors/ftpconnector/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)