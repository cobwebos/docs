---
title: 使用 Azure 存储资源管理器管理 Azure Cosmos DB 资源
description: 了解如何使用 Azure 存储资源管理器连接到 Azure Cosmos DB 并管理其资源。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 938968599f1824416666818a46cc73a1d33c5341
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987746"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>使用 Azure 存储资源管理器管理 Azure Cosmos DB 资源

可以使用 Azure 存储资源管理器连接到 Azure Cosmos DB。 通过此功能，你可以连接到在 Azure 上托管的 Azure Cosmos DB 帐户，也可以从 Windows、macOS 或 Linux 连接到主权云。

使用相同的工具在一个位置管理不同的 Azure 实体。 可以管理 Azure Cosmos DB 实体、操作数据、更新存储过程和触发器以及其他 Azure 实体（如存储 blob 和队列）。

Azure 存储资源管理器支持为 SQL、MongoDB、Graph 和表 Api 配置的 Cosmos 帐户。 有关详细信息，请参阅 [Azure 存储资源管理器中的 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) 。

## <a name="prerequisites"></a>必备条件

具有 SQL API 或 MongoDB Azure Cosmos DB API 的 Cosmos 帐户。 如果没有帐户，可以在 Azure 门户中创建一个。 有关详细信息，请参阅 [Azure Cosmos DB：使用 .Net 构建 SQL API web 应用和 Azure 门户](create-sql-api-dotnet.md) 。

## <a name="installation"></a>安装

若要安装最新的 Azure 存储资源管理器位，请参阅 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。 我们支持 Windows、Linux 和 macOS 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 安装 **Azure 存储资源管理器**后，请在左窗格中选择 " **插件** " 图标。

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

1. 选择“添加 Azure 帐户”****，然后选择“登录”****。

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

1. 在“Azure 登录”对话框中，选择“登录”，然后输入 Azure 凭据。

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="显示左窗格中的插件图标的屏幕截图。" **应用**"。

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 窗格将更新并显示所选订阅中的帐户。

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

    你的 **Cosmos DB 帐户** 已连接到你的 Azure 订阅。

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>使用连接字符串连接到 Azure Cosmos DB

可以使用连接字符串连接到 Azure Cosmos DB。 此方法仅支持 SQL 和表 Api。 按照下列步骤操作以连接到连接字符串：

1. 在左侧树中找到 " **本地和附加** "，右键单击 **Cosmos DB 帐户**"，然后选择" **连接到 Cosmos DB**"。

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 以连接 Azure Cosmos DB 帐户。

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>使用本地模拟器连接到 Azure Cosmos DB

使用以下步骤通过模拟器连接到 Azure Cosmos DB。 此方法仅支持 SQL 帐户。

1. 安装 Cosmos DB 模拟器，然后将其打开。 有关如何安装模拟器的详细说明，请参阅 [Cosmos DB 模拟器](https://docs.microsoft.com/azure/cosmos-db/local-emulator)。

1. 在左侧树中找到 " **本地和附加** "，右键单击 **Cosmos DB 帐户**"，然后选择" **连接到 Cosmos DB 模拟器**"。

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 以连接 Azure Cosmos DB 帐户。

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 资源管理

使用下列操作来管理 Azure Cosmos DB 帐户：

* 在 Azure 门户中打开帐户。
* 将资源添加到快速访问列表。
* 搜索和刷新资源。
* 创建和删除数据库。
* 创建和删除集合。
* 创建、编辑、删除和筛选文档。
* 管理存储过程、触发器和用户定义的函数。

### <a name="quick-access-tasks"></a>快速访问任务

您可以在 "资源管理器" 窗格上右键单击某个订阅以执行多个快速操作任务，例如：

* 右键单击 Azure Cosmos DB 帐户或数据库，然后选择 " **在门户中打开** "，以便在浏览器上的 Azure 门户中管理资源。

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 可在所选路径下启用关键字搜索。

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

### <a name="database-and-collection-management"></a>数据库和集合管理

#### <a name="create-a-database"></a>创建数据库

1. 右键单击 Azure Cosmos DB 帐户，然后选择 " **创建数据库**"。

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 完成。

#### <a name="delete-a-database"></a>删除数据库

1. 右键单击该数据库，然后选择 " **删除数据库**"。 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

1. 在弹出窗口中选择 **"是"** 。 数据库节点会删除，并且 Azure Cosmos DB 帐户会自动刷新。

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

#### <a name="create-a-collection"></a>创建集合

1. 右键单击你的数据库，然后选择 " **创建集合**"。

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 窗口中，输入请求的信息，如 **集合 ID** 和 **存储容量**等。 选择“确定”，以完成操作。

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="显示左窗格中的插件图标的屏幕截图。" **无限制** "，以便可以指定分区键，然后选择 **"确定"** 完成操作。

   > [!NOTE]
   > 如果在创建集合时使用分区键，则创建完成后，将无法更改集合中的分区键值。

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

#### <a name="delete-a-collection"></a>删除集合

- 右键单击集合，选择“删除集合”，然后在弹出窗口中选择“是”。

    集合节点会删除，并且数据库会自动刷新。

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

### <a name="document-management"></a>文档管理

#### <a name="create-and-modify-documents"></a>创建和修改文档

- 打开左侧窗格中的 " **文档** "，选择 " **新建文档**"，在右窗格中编辑内容，然后选择 " **保存**"。
- 还可以更新现有文档，然后选择“保存”。 若要放弃更改，请选择 " **放弃**"。

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

#### <a name="delete-a-document"></a>删除文档

* 选择 " **删除** " 按钮以删除所选文档。

#### <a name="query-for-documents"></a>查询文档

* 若要编辑文档筛选器，请输入 [SQL 查询](how-to-sql-query.md)，然后选择 " **应用**"。

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

### <a name="graph-management"></a>图形管理

#### <a name="create-and-modify-a-vertex"></a>创建和修改顶点

* 若要创建新顶点，请从左窗格打开 " **关系图** "，选择 " **新建顶点**"，编辑内容，然后选择 **"确定"**。
* 若要修改现有顶点，请在右侧窗格中选择 "笔" 图标。

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

#### <a name="delete-a-graph"></a>创建图形

* 若要删除某个顶点，请选择顶点名称旁边的回收站图标。

#### <a name="filter-for-graph"></a>筛选图形

* 若要编辑图形筛选器，请输入 [gremlin 查询](gremlin-support.md)，然后选择 " **应用筛选器**"。

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

### <a name="table-management"></a>表管理

#### <a name="create-and-modify-a-table"></a>创建和修改表

* 创建新表：
   1. 在左窗格中，打开 " **实体**"，然后选择 " **添加**"。
   1. 在 " **添加实体** " 对话框中，编辑内容。
   1. 选择 " **添加属性** " 按钮以添加属性。
   1. 选择“插入”****。

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="显示左窗格中的插件图标的屏幕截图。" **更新**"。

   

#### <a name="import-and-export-table"></a>导入和导出表

* 若要导入，请选择 " **导入** " 按钮，然后选择一个现有表。
* 若要导出，请选择 " **导出** " 按钮，然后选择一个目标。

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

#### <a name="delete-entities"></a>删除实体

* 选择实体，然后选择 " **删除** " 按钮。

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

#### <a name="query-a-table"></a>查询表

- 选择 " **查询** " 按钮，输入查询条件，然后选择 " **执行查询** " 按钮。 若要关闭查询窗格，请选择 " **关闭查询** " 按钮。

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理存储过程、触发器和 UDF

* 创建存储过程：
  1. 在左侧树中，右键单击 " **存储过程**"，然后选择 " **创建存储过程**"。
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 以取消更改。

* **触发器**和**UDF**的操作与**存储过程**类似。

## <a name="troubleshooting"></a>疑难解答

下面是在存储资源管理器中使用 Azure Cosmos DB 时出现的常见问题的解决方案。

### <a name="sign-in-issues"></a>登录问题

首先，重新启动你的应用程序，看看是否能解决问题。 如果问题仍然存在，请继续进行故障排除。

#### <a name="self-signed-certificate-in-certificate-chain"></a>证书链中的自签名证书

出现此错误的原因有几个，两个最常见的原因是：

* 你位于 *透明代理*之后。 与你的 IT 部门一样，有人会截获 HTTPS 流量，对其进行解密，然后使用自签名证书对其进行加密。

* 正在运行软件，例如防病毒软件。 该软件会将自签名的 TLS/SSL 证书注入到收到的 HTTPS 消息中。

当存储资源管理器查找自签名证书时，它不知道它收到的 HTTPS 消息是否已被篡改。 如果你有自签名证书的副本，则可以告诉存储资源管理器信任该证书。 如果你不确定是谁注入了证书，则可以按照以下步骤尝试查找：

1. 安装 OpenSSL：

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html)：任何光源版本都是正常的。
     - macOS 和 Linux：应包含在操作系统中。

1. 运行 OpenSSL：
    * Windows：依次单击 "安装" 目录和 " **/bin/**"，然后双击 **openssl.exe**。
    * Mac 和 Linux：从终端中执行 **openssl** 。
1. 执行 `s_client -showcerts -connect microsoft.com:443`。
1. 查找自签名证书。 如果你不确定，这是自签名的，则查找使用者 ( "s：" ) 和颁发者 ( "i：" ) 相同的任何位置。
1. 如果找到任何自签名证书，请从中复制所有内容并将其粘贴 **-----开始证书-----** ， **-----最终证书-----** 到新的。每个文件的 .CER 文件。
1. 打开存储资源管理器，然后 "**编辑**  >  **SSL 证书**" "  >  **导入证书**"。 使用文件选取器查找、选择和打开。你创建的 .CER 文件。

如果找不到任何自签名证书，可以发送反馈以获得更多帮助。

#### <a name="unable-to-retrieve-subscriptions"></a>无法检索订阅

如果在登录后无法检索订阅，请尝试以下建议：

* 验证你的帐户是否有权访问订阅。 为此，请登录到 [Azure 门户](https://portal.azure.com/)。
* 请确保已登录到正确的环境：
  * [Azure](https://portal.azure.com/)
  * [Azure 中国](https://portal.azure.cn/)
  * [Azure 德国](https://portal.microsoftazure.de/)
  * [Azure 美国政府版](https://portal.azure.us/)
  * 自定义环境/Azure Stack
* 如果在代理后面，请确保已正确配置存储资源管理器代理。
* 删除帐户，然后重新添加。
* 从主目录中删除以下文件 (例如： C:\Users\ContosoUser) ，然后再次添加该帐户：
  * .adalcache
  * .devaccounts
  * .extaccounts
* 按 F12 键打开开发人员控制台。 登录时，请观看控制台，查看是否有任何错误消息。

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

#### <a name="unable-to-see-the-authentication-page"></a>无法查看身份验证页

如果无法看到身份验证页面：

* 登录页面可能需要一些时间才能加载，具体取决于连接速度。 请等待至少一分钟，然后再关闭身份验证对话框。
* 如果在代理后面，请确保已正确配置存储资源管理器代理。
* 在 "开发人员工具" 控制台 (F12) 上，查看响应，查看是否可以找到有关身份验证不起作用的任何线索。

#### <a name="cant-remove-an-account"></a>无法删除帐户

如果无法删除帐户，或 "重新身份验证" 链接不执行任何操作：

* 从主目录中删除以下文件，然后重新添加帐户：
  * .adalcache
  * .devaccounts
  * .extaccounts

* 若要删除附加了 SAS 的存储资源，请删除：
  * %AppData%/StorageExplorer 文件夹（对于 Windows）
  * /Users/<your_name>/Library/application support 支持/StorageExplorer macOS
  * ~/.config/StorageExplorer（对于 Linux）
  
  > [!NOTE]
  > 如果删除这些文件， **则必须重新输入所有凭据**。

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS 代理问题

在 ASE 中配置 HTTP/HTTPS 代理时，无法列出左侧树中的 Azure Cosmos DB 节点。 您可以使用 Azure 门户的 Azure Cosmos DB 数据资源管理器。

### <a name="development-node-under-local-and-attached-node-issue"></a>“本地和附加”节点下的“开发”节点问题

在左侧树中的 "**本地" 和 "附加**" 节点下选择 "**开发**" 节点后，没有响应。 此行为是预期的行为。

:::image type="content" source="./media/storage-explorer/development.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>在 **本地和附加** 的节点中附加 Azure Cosmos DB 帐户错误

如果在 **本地和附加** 的节点中附加了 Azure Cosmos DB 帐户后出现以下错误，请确保使用的是正确的连接字符串。

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="显示左窗格中的插件图标的屏幕截图。":::

### <a name="expand-azure-cosmos-db-node-error"></a>展开 Azure Cosmos DB 节点错误

尝试展开左侧树中的节点时，可能会看到以下错误。

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="显示左窗格中的插件图标的屏幕截图。" 节点下，请检查帐户是否已删除。 如果是这样，则需要手动删除该节点。

## <a name="next-steps"></a>后续步骤

* 观看此视频以了解如何在 Azure 存储资源管理器中使用 Azure Cosmos DB： [使用 Azure 存储资源管理器中的 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[存储资源管理器入门](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)中了解有关存储资源管理器和连接更多服务的详细信息。
