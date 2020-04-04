---
title: 连接到 FTP 服务器
description: 使用 Azure 逻辑应用自动执行在 FTP 服务器上创建、监视和管理文件的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 5b61b51e79c71736e18aaa63ab032c05c512c8d7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656334"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用创建、监视和管理 FTP 文件

使用 Azure 逻辑应用和 FTP 连接器，可以创建自动化的任务和工作流，用于通过 FTP 服务器上的帐户创建、监视、发送和接收文件以及执行其他操作，例如：

* 添加或更改文件时进行监视。
* 获取、创建、复制、更新、列出和删除文件。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器从 FTP 服务器获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的运行操作对 FTP 服务器上的文件进行管理。 还可以让其他操作使用来自 FTP 操作的输出。 例如，如果你定期从 FTP 服务器获取文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件。 如果您是逻辑应用的新功能，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

## <a name="limitations"></a>限制

* FTP 连接器仅支持 TLS/SSL （FTPS） 的显式 FTP，并且与隐式 FTPS 不兼容。

* 默认情况下，FTP 操作可以读取或写入 *50 MB 或更小*的文件。 若要处理大于 50 MB 的文件，FTP 操作支持[消息分块](../logic-apps/logic-apps-handle-large-messages.md)。 **获取文件内容**操作隐式使用分块。

* FTP 触发器不支持分块。 请求文件内容时，触发器仅选择 50 MB 或更小的文件。 若要获取大于 50 MB 的文件，请遵循以下模式：

  * 使用返回文件属性的 FTP 触发器，如“添加或修改文件时(仅属性)”****。

  * 跟随触发器执行 FTP **获取文件内容**操作，该操作读取完整文件并隐式使用分块。

* 如果您有本地 FTP 服务器，请考虑创建[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)或使用 Azure[应用服务混合连接](../app-service/app-service-hybrid-connections.md)，这两个连接都允许您访问本地数据源而无需使用本地数据网关。

## <a name="how-ftp-triggers-work"></a>FTP 触发器的工作原理

FTP 触发器的工作原理是轮询 FTP 文件系统并查找自上次轮询后已更改的任何文件。 某些工具允许保留文件更改时的时间戳。 在这种情况下，必须禁用此功能才能让触发器正常工作。 下面是一些常见设置：

| SFTP 客户端 | 操作 |
|-------------|--------|
| Winscp | 转到**选项** > **首选项** > **传输** > **Edit** > **Disable**编辑**保留时间戳**禁用 >  |
| FileZilla | 转到**传输** > **保留已传输文件** > 的时间戳**禁用** |
|||

当触发器找到新文件时，会检查该新文件是否完整，以及是否未部分写入。 例如，当触发器检查文件服务器时，可能正在更改某个文件。 为了避免返回部分写入的文件，该触发器会记录具有最近更改的文件的时间戳，但不会立即返回该文件。 仅当再次轮询服务器时，触发器才会返回该文件。 有时，此行为可能会导致延迟，长达触发器轮询间隔的两倍。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* FTP 主机服务器地址和帐户凭据

  FTP 连接器需要可从 Internet 访问并设置为在 passive 模式下运行的 FTP 服务器**。 你的凭据允许逻辑应用创建连接并访问你的 FTP 帐户。

* [有关如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 FTP 帐户的逻辑应用。 若要从 FTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 FTP 操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。****

## <a name="connect-to-ftp"></a>连接到 FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 Azure[门户](https://portal.azure.com)，并在逻辑应用设计器中打开逻辑应用。

1. 对于空白逻辑应用，在搜索框中，输入`ftp`作为筛选器。 从 **"触发器"** 列表中，选择所需的触发器。

   \- 或 -

   对于现有逻辑应用，在要添加操作的最后一步下，选择 **"新建步骤**"，然后选择"**添加操作**"。 在搜索框中，输入 `ftp` 作为筛选器。 从 **"操作"** 列表中，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择显示的加号**+**（），然后选择 **"添加操作**"。

1. 提供连接信息，然后选择 **"创建**"。

1. 提供所选触发器或操作的信息，并继续构建逻辑应用的工作流。

## <a name="examples"></a>示例

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>添加 FTP 触发器

**当文件被添加或修改（仅属性）** 触发器检测到在 FTP 服务器上添加或更改文件时，触发器将启动逻辑应用工作流。 例如，您可以添加一个条件，检查文件的内容，并根据该内容是否符合指定条件决定是否获取该内容。 最后，您可以添加获取文件内容的操作，并将该内容放入 SFTP 服务器上的不同文件夹中。

例如，可以使用此触发器监视 FTP 文件夹，以获取描述客户订单的新文件。 然后，可以使用 FTP 操作（如**获取文件元数据**）来获取该新文件的属性，然后使用**Get 文件内容**从该文件获取内容以进行进一步处理，并将该顺序存储在订单数据库中。

下面是一个示例，演示如何使用 **"何时添加或修改文件（仅限属性）"** 触发器。

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，在搜索框中，输入`ftp`作为筛选器。 在触发器列表下，选择此触发器：**添加或修改归档的触发器时（仅限属性）**

   ![查找并选择 FTP 触发器](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. 提供连接的必要详细信息，然后选择 **"创建**"。

   默认情况下，此连接器将以文本格式传输文件。 若要以二进制格式传输文件，例如，在使用了编码的情况下，请选择“二进制传输”****。

   ![创建与 FTP 服务器的连接](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. 在 **"文件夹**"框中，选择文件夹图标，以便显示列表。 要查找要监视新文件或已编辑文件的文件夹，请选择直角箭头 （），**>** 然后选择该文件夹。

   ![查找并选择要监视的文件夹](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   所选文件夹将出现在“文件夹”框中****。

   ![所选文件夹将显示在"文件夹"属性中](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”****。

现在，逻辑应用已具有一个触发器，请添加在逻辑应用找到新文件或已编辑文件时要运行的操作。 对于此示例，可以添加获取新内容或更新内容的 FTP 操作。

<a name="get-content"></a>

### <a name="add-ftp-action"></a>添加 FTP 操作

**Get 文件元数据**操作获取 FTP 服务器上的文件的属性，**而 Get 文件内容**操作基于 FTP 服务器上有关该文件的信息获取文件内容。 例如，您可以在添加或编辑该文件后，从前面的示例中添加触发器和这些操作来获取文件的内容。

1. 在触发器或任何其他操作下，选择 **"新建步骤**"。

1. 在搜索框中，输入 `ftp` 作为筛选器。 在操作列表下，选择此操作：**获取文件元数据**

   ![选择"获取文件元数据"操作](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. 如果已有到 FTP 服务器和帐户的连接，请转到下一步。 否则，请提供该连接的必要详细信息，然后选择 **"创建**"。

   ![创建 FTP 服务器连接](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. **显示"获取文件元数据**"操作后，单击 **"文件**"框，以便显示动态内容列表。 现可为先前步骤的输出结果选择属性。 在动态内容列表中，在 **"获取文件元数据**"下，选择"**文件 Id 列表"** 属性，该属性引用添加或更新文件的集合。

   ![查找并选择"文件 ID 列表"属性](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   "**文件 ID 列表"** 属性现在显示在 **"文件"** 框中。

   ![选定的"文件 ID 列表"属性](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. 现在添加此 FTP 操作：**获取文件内容**

   ![查找并选择"获取文件内容"操作](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. **显示"获取文件内容"** 操作后，单击 **"文件**"框，以便显示动态内容列表。 现可为先前步骤的输出结果选择属性。 在动态内容列表中，在 **"获取文件元数据**"下，选择 **"Id"** 属性，该属性引用已添加或更新的文件。

   ![查找并选择"Id"属性](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   **Id**属性现在显示在 **"文件"** 框中。

   ![选定的"Id"属性](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. 保存逻辑应用。

## <a name="test-your-logic-app"></a>测试逻辑应用

要检查工作流是否返回预期内容，请添加另一个从上载或更新文件中向您发送内容的操作。

1. 在 **"获取文件内容**"操作下，添加一个可以向您发送文件内容的操作。 本示例添加 Office 365 Outlook**的电子邮件发送操作**。

   ![添加用于发送电子邮件的操作](./media/connectors-create-api-ftp/select-send-email-action.png)

1. 出现操作后，请提供信息并包含要测试的属性。 例如，包括 **"文件内容**"属性，该属性在选择"**获取文件内容"** 部分**See more**后显示在动态内容列表中。

   ![提供关于电子邮件操作的信息](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. 保存逻辑应用。 要在工具栏上运行和触发逻辑应用，请选择 **"运行**"，然后将文件添加到逻辑应用现在监视的 FTP 文件夹中。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，例如连接器的 Swagger 文件所述的触发器、操作和限制，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/ftpconnector/)。

> [!NOTE]
> 对于[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的 ISE 标记版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
