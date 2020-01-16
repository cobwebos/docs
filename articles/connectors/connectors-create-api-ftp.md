---
title: 连接到 FTP 服务器
description: 使用 Azure 逻辑应用自动创建、监视和管理 FTP 服务器上的文件的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 0314dceb23f02b723854dfc406e9440bbc14ccf6
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044284"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用创建、监视和管理 FTP 文件

使用 Azure 逻辑应用和 FTP 连接器，可以创建自动化的任务和工作流，用于通过 FTP 服务器上的帐户创建、监视、发送和接收文件以及执行其他操作，例如：

* 添加或更改文件时进行监视。
* 获取、创建、复制、更新、列出和删除文件。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器从 FTP 服务器获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的运行操作对 FTP 服务器上的文件进行管理。 还可以让其他操作使用来自 FTP 操作的输出。 例如，如果你定期从 FTP 服务器获取文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

## <a name="limitations"></a>限制

* FTP 连接器仅支持显式 FTP over SSL （FTPS），并且不兼容隐式 FTPS。

* 默认情况下，FTP 操作可以读取或写入*50 MB 或更小*的文件。 若要处理大于 50 MB 的文件，FTP 操作支持[消息分块](../logic-apps/logic-apps-handle-large-messages.md)。 "**获取文件内容**" 操作隐式使用分块。

* FTP 触发器不支持分块。 请求文件内容时，触发器仅选择 50 MB 或更小的文件。 若要获取大于 50 MB 的文件，请遵循以下模式：

  * 使用返回文件属性的 FTP 触发器，如**添加或修改文件时（仅属性）** 。

  * 使用 FTP 的 "**获取文件内容**" 操作执行触发器，该操作读取完整的文件并隐式使用分块。

* 如果你有本地 FTP 服务器，请考虑创建[集成服务环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)或使用[Azure App Service 混合连接](../app-service/app-service-hybrid-connections.md)，这两者都允许你在不使用本地数据网关的情况下访问本地数据源。

## <a name="how-ftp-triggers-work"></a>FTP 触发器的工作方式

FTP 触发器的工作方式是轮询 FTP 文件系统，并查找自上次轮询以来发生更改的任何文件。 某些工具允许保留文件更改时的时间戳。 在这种情况下，必须禁用此功能才能让触发器正常工作。 下面是一些常见设置：

| SFTP 客户端 | 行动 |
|-------------|--------|
| Winscp | 转到“选项” > “首选项” > “传输” > “编辑” > “保留时间戳” > “禁用” |
| FileZilla | 转到“传输” > “保留已传输文件的时间戳” > “禁用” |
|||

当触发器找到新文件时，会检查该新文件是否完整，以及是否未部分写入。 例如，当触发器检查文件服务器时，可能正在更改某个文件。 为了避免返回部分写入的文件，该触发器会记录具有最近更改的文件的时间戳，但不会立即返回该文件。 仅当再次轮询服务器时，触发器才会返回该文件。 有时，此行为可能会导致延迟，长达触发器轮询间隔的两倍。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* FTP 主机服务器地址和帐户凭据

  FTP 连接器需要可从 Internet 访问并设置为在 passive 模式下运行的 FTP 服务器。 你的凭据允许逻辑应用创建连接并访问你的 FTP 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 FTP 帐户的逻辑应用。 若要从 FTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 FTP 操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。

## <a name="connect-to-ftp"></a>连接到 FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到[Azure 门户](https://portal.azure.com)，然后在逻辑应用设计器中打开逻辑应用。

1. 对于 "空白逻辑应用"，请在 "搜索" 框中输入 "`ftp`" 作为筛选器。 从 "**触发器**" 列表中，选择所需的触发器。

   -或-

   对于现有的逻辑应用，在要添加操作的最后一个步骤下，选择 "**新建步骤**"，然后选择 "**添加操作**"。 在搜索框中，输入 `ftp` 作为筛选器。 从 "**操作**" 列表中，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 提供连接信息，并选择 "**创建**"。

1. 提供选定触发器或操作的信息，并继续生成逻辑应用的工作流。

## <a name="examples"></a>示例

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>添加 FTP 触发器

**添加或修改文件时（仅限属性）** 触发器在 FTP 服务器上检测到已添加或更改文件时启动逻辑应用工作流。 例如，你可以添加一个条件，用于检查文件的内容，并根据该内容是否满足指定的条件来决定是否获取该内容。 最后，你可以添加一个操作来获取文件的内容，并将该内容放入 SFTP 服务器上的另一个文件夹中。

例如，您可以使用此触发器监视 FTP 文件夹中是否有描述客户订单的新文件。 然后，你可以使用 FTP 操作（如**获取文件元数据**）获取该新文件的属性，然后使用 "**获取文件内容**" 获取该文件的内容以进行进一步处理，并将该订单存储在 orders 数据库中。

下面的示例演示如何**在添加或修改文件时使用（仅限属性）** 触发器。

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于 "空白逻辑应用"，请在 "搜索" 框中输入 "`ftp`" 作为筛选器。 在 "触发器" 列表下，选择 "此触发器：**添加或修改存档时" （仅属性）**

   ![查找并选择 FTP 触发器](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. 提供连接所需的详细信息，然后选择 "**创建**"。

   默认情况下，此连接器将以文本格式传输文件。 若要以二进制格式传输文件，例如，在使用了编码的情况下，请选择“二进制传输”。

   ![创建与 FTP 服务器的连接](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. 在 "**文件夹**" 框中，选择文件夹图标，以便显示列表。 若想查找要监视其中是否有新文件或已编辑文件的文件夹，请选择右箭头 (>)，浏览到该文件夹并将其选中。

   ![查找并选择要监视的文件夹](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   所选文件夹将出现在“文件夹”框中。

   ![所选文件夹出现在 "文件夹" 属性中](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

现在，逻辑应用已具有一个触发器，请添加在逻辑应用找到新文件或已编辑文件时要运行的操作。 对于此示例，可以添加获取新内容或更新内容的 FTP 操作。

<a name="get-content"></a>

### <a name="add-ftp-action"></a>添加 FTP 操作

"**获取文件元数据**" 操作获取 ftp 服务器上的文件的属性，"**获取文件内容**" 操作将基于 FTP 服务器上该文件的相关信息获取文件内容。 例如，你可以添加上一个示例中的触发器和这些操作，以便在添加或编辑该文件后获取该文件的内容。

1. 在触发器或任何其他操作下，选择 "**新建步骤**"。

1. 在搜索框中，输入 `ftp` 作为筛选器。 在 "操作" 列表中，选择此操作：**获取文件元数据**

   ![选择 "获取文件元数据" 操作](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. 如果已有到 FTP 服务器和帐户的连接，请转到下一步。 否则，请提供该连接的必要详细信息，然后选择 "**创建**"。

   ![创建 FTP 服务器连接](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. 出现 "**获取文件元数据**" 操作后，在 "**文件**" 框中单击，以便显示 "动态内容" 列表。 现可为先前步骤的输出结果选择属性。 在 "动态内容" 列表中的 "**获取文件元数据**" 下，选择 "**文件 Id** " 属性，该属性引用添加或更新文件的集合。

   ![查找并选择 "文件的列表 Id" 属性](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   文件**Id 属性列表**现在显示在 "**文件**" 框中。

   ![已选择 "文件列表 Id" 属性](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. 现在添加此 FTP 操作：**获取文件内容**

   ![查找并选择 "获取文件内容" 操作](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. 出现 "**获取文件内容**" 操作后，在 "**文件**" 框中单击，以便显示 "动态内容" 列表。 现可为先前步骤的输出结果选择属性。 在 "动态内容" 列表中的 "**获取文件元数据**" 下，选择 " **Id** " 属性，该属性引用已添加或更新的文件。

   ![查找并选择 "Id" 属性](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   **Id**属性现在显示在 "**文件**" 框中。

   ![选定的 "Id" 属性](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. 保存逻辑应用。

## <a name="test-your-logic-app"></a>测试逻辑应用

若要检查工作流是否返回了所需的内容，请添加另一个操作，从上传或更新的文件发送内容。

1. 在 "**获取文件内容**" 操作下，添加一个可向你发送文件内容的操作。 此示例为 Office 365 Outlook 添加 "**发送电子邮件**" 操作。

   ![添加用于发送电子邮件的操作](./media/connectors-create-api-ftp/select-send-email-action.png)

1. 操作出现后，提供信息并包含要测试的属性。 例如，在 "**获取文件内容**" 部分中选择 "**查看更多**" 后，在 "动态内容" 列表中显示 "**文件内容**" 属性。

   ![提供关于电子邮件操作的信息](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. 保存逻辑应用。 若要运行和触发逻辑应用，请在工具栏上选择 "**运行**"，然后将文件添加到逻辑应用现在所监视的 FTP 文件夹中。

## <a name="connector-reference"></a>连接器参考

有关由连接器的 OpenAPI （以前为 Swagger）说明描述的触发器、操作和限制的技术详细信息，请查看[连接器的参考页](/connectors/ftpconnector/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
