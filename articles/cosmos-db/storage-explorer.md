---
title: 使用 Azure 存储资源管理器管理 Azure Cosmos DB 资源
description: 了解如何使用 Azure 存储资源管理器连接到 Azure Cosmos DB 并管理其资源。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 2cf1ab2b9ec7e1094a0afa34973db3441c22ee5b
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982762"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>使用 Azure 存储资源管理器处理数据

通过在 Azure 存储资源管理器中使用 Azure Cosmos DB，用户可以管理 Azure Cosmos DB 实体、操作数据、更新存储过程和触发器以及其他 Azure 实体（如存储 blob 和队列）。 现在可以使用相同工具在一个位置管理不同 Azure 实体。 目前，Azure 存储资源管理器支持为 SQL、MongoDB、图形和表 API 配置的 Cosmos 帐户。


## <a name="prerequisites"></a>先决条件

具有采用了 SQL API 或 Azure Cosmos DB 的用于 MongoDB 的 API 的 Cosmos 帐户。 如果你没有帐户，则可以按照 [Azure Cosmos DB：使用 .NET 和 Azure 门户生成 SQL API Web 应用](create-sql-api-dotnet.md)中所述，在 Azure 门户中创建一个。

## <a name="installation"></a>安装

在此处安装最新 Azure 存储资源管理器 BITS：[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)，现在我们支持 Windows、Linux 和 MAC 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 安装 **Azure 存储资源管理器**之后，单击左侧的**插件**图标，如下图中所示：

   ![插件图标](./media/storage-explorer/plug-in-icon.png)

2. 选择“添加 Azure 帐户”****，然后单击“登录”****。

   ![连接到 Azure 订阅](./media/storage-explorer/connect-to-azure-subscription.png)

2. 在“Azure 登录”  对话框中，选择“登录”  ，然后输入 Azure 凭据。

    ![登录](./media/storage-explorer/sign-in.png)

3. 从列表中选择你的订阅，然后单击“应用”  。

    ![应用](./media/storage-explorer/apply-subscription.png)

    资源管理器窗格会更新，并显示所选订阅中的帐户。

    ![帐户列表](./media/storage-explorer/account-list.png)

    现已成功将 **Cosmos DB 帐户**连接到 Azure 订阅。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB

连接到 Azure Cosmos DB 的一种替代方法是使用连接字符串。 按照以下步骤可使用连接字符串进行连接。

1. 在左侧树中找到“本地和附加”  ，右键单击“Cosmos DB 帐户”  ，然后选择“连接到 Cosmos DB...” 

    ![通过连接字符串连接到 Cosmos DB](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. 目前仅支持 SQL 和表 API。 选择“API”，粘贴**连接字符串**，输入**帐户标签**，单击“下一步”查看摘要，然后单击“连接”以连接 Azure Cosmos DB 帐户。   有关检索主连接字符串的信息，请参阅[获取连接字符串](manage-with-powershell.md#list-keys)。

    ![连接字符串](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>使用本地模拟器连接到 Azure Cosmos DB

使用以下步骤通过模拟器连接到 Azure Cosmos DB（目前仅支持 SQL 帐户）。

1. 安装并启动模拟器。 有关如何安装模拟器的信息，请参阅 [Cosmos DB 模拟器](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. 在左侧树中找到“本地和附加”  ，右键单击“Cosmos DB 帐户”  ，然后选择“连接到 Cosmos DB 模拟器...” 

    ![通过模拟器连接到 Cosmos DB](./media/storage-explorer/emulator-entry.png)

3. 目前仅支持 SQL API。 粘贴**连接字符串**，输入**帐户标签**，单击“下一步”查看摘要，然后单击“连接”以连接 Azure Cosmos DB 帐户。   有关检索主连接字符串的信息，请参阅[获取连接字符串](manage-with-powershell.md#list-keys)。

    ![通过模拟器对话框连接到 Cosmos DB](./media/storage-explorer/emulator-dialog.png)


## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 资源管理

你可以通过执行以下操作来管理 Azure Cosmos DB 帐户：
* 在 Azure 门户中打开帐户
* 将资源添加到快速访问列表
* 搜索和刷新资源
* 创建和删除数据库
* 创建和删除集合
* 创建、编辑、删除和筛选文档
* 管理存储过程、触发器和用户定义的函数

### <a name="quick-access-tasks"></a>快速访问任务

通过在资源管理器窗格中右键单击订阅，可以执行许多快速操作任务：

* 右键单击 Azure Cosmos DB 帐户或数据库，可以选择“在门户中打开”  ，然后在 Azure 门户上通过浏览器管理资源。

     ![在门户中打开](./media/storage-explorer/open-in-portal.png)

* 还可以将 Azure Cosmos DB 帐户、数据库、集合添加到“快速访问”  。
* 通过“从此处搜索”  可以在所选路径下进行关键字搜索。

    ![从此处搜索](./media/storage-explorer/search-from-here.png)

### <a name="database-and-collection-management"></a>数据库和集合管理
#### <a name="create-a-database"></a>创建数据库
-   右键单击 Azure Cosmos DB 帐户，选择“创建数据库”  ，输入数据库名称，然后按 **Enter** 以完成。

    ![创建数据库](./media/storage-explorer/create-database.png)

#### <a name="delete-a-database"></a>删除数据库
- 右键单击数据库，单击“删除数据库”  ，然后在弹出窗口中单击“是”  。 数据库节点会删除，并且 Azure Cosmos DB 帐户会自动刷新。

    ![删除数据库 1](./media/storage-explorer/delete-database1.png)

    ![删除数据库 2](./media/storage-explorer/delete-database2.png)

#### <a name="create-a-collection"></a>创建集合
1. 右键单击数据库，选择“创建集合”  ，然后提供以下信息，如“集合 ID”  、“存储容量”  等。单击“确定”  完成。

    ![创建集合 1](./media/storage-explorer/create-collection.png)

    ![创建集合 2](./media/storage-explorer/create-collection2.png)

2. 选择“无限制”以便能够指定分区键，然后单击“确定”完成操作。  

    如果在创建集合时使用分区键，则一旦创建完成，便无法对集合更改分区键值。

    ![分区键](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>删除集合
- 右键单击集合，单击“删除集合”  ，然后在弹出窗口中单击“是”  。

    集合节点会删除，并且数据库会自动刷新。

    ![删除集合](./media/storage-explorer/delete-collection.png)

### <a name="document-management"></a>文档管理

#### <a name="create-and-modify-documents"></a>创建和修改文档
- 若要创建新文档，请打开左窗口中的“文档”  ，单击“新建文档”  ，在右窗格中编辑内容，然后单击“保存”  。 还可以更新现有文档，然后单击“保存”  。 可以通过单击“放弃”  来放弃更改。

    ![文档](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>删除文档
- 单击“删除”  按钮以删除所选文档。

#### <a name="query-for-documents"></a>查询文档
- 通过输入 [SQL 查询](how-to-sql-query.md)来编辑文档筛选器，然后单击“应用”  。

    ![文档筛选器](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>图形管理

#### <a name="create-and-modify-vertex"></a>创建和修改顶点
1. 若要创建新顶点，请在左窗口中打开“图形”，单击“新建顶点”，编辑内容，然后单击“确定”。   
2. 若要修改现有顶点，请在右窗格中单击铅笔图标。

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>创建图形
- 若要删除某个顶点，单击顶点名称旁边的回收站图标。

#### <a name="filter-for-graph"></a>筛选图形
- 通过输入 [gremlin 查询](gremlin-support.md)来编辑图形筛选器，然后单击“应用筛选器”  。

    ![图形筛选器](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>表管理

#### <a name="create-and-modify-table"></a>创建和修改表
1. 若要创建新表，请在左窗口中打开“实体”，单击“添加”，在“添加实体”对话框中编辑内容，单击“添加属性”按钮添加属性，然后单击“插入”。     
2. 若要修改表，请单击“编辑”，修改内容，然后单击“更新”。  

    ![表](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>导入和导出表
1. 若要导入，请单击“导入”按钮并选择现有的表。 
2. 若要导出，请单击“导出”按钮并选择目标。 

    ![表导入和导出](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>删除实体
- 选择实体，然后单击“删除”按钮。 

    ![表删除](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>查询表
- 单击“查询”按钮，输入查询条件，然后单击“执行查询”按钮。   单击“关闭查询”按钮关闭“查询”窗格。 

    ![表查询](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理存储过程、触发器和 UDF
* 若要创建存储过程，请在左侧树中，右键单击“存储过程”  ，选择“创建存储过程”  ，在左侧输入名称，在右侧窗口中输入存储过程脚本，然后单击“创建”  。
* 还可以双击，进行更新，然后单击“更新”  保存，从而编辑现有存储过程，或单击“放弃”  以取消更改。

    ![存储过程](./media/storage-explorer/stored-procedure.png)
* 适用于**触发器**和 **UDF** 的操作与**存储过程**类似。

## <a name="troubleshooting"></a>故障排除

[Azure 存储资源管理器中的 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) 是一个独立的应用，用于连接到 Azure Cosmos DB 帐户，而该帐户托管在 Windows、macOS 或 Linux 版本的 Azure 和主权云上。 它可以用来管理 Azure Cosmos DB 实体、操作数据、更新存储过程和触发器以及其他 Azure 实体（如存储 Blob 和队列）。

以下解决方案适用于存储资源管理器中 Azure Cosmos DB 的常见问题。

### <a name="sign-in-issues"></a>登录问题

继续之前，请尝试重启应用程序，看问题是否能够解决。

#### <a name="self-signed-certificate-in-certificate-chain"></a>证书链中的自签名证书

出现此错误有多个原因，最常见的两个原因是：

+ 你位于“透明代理”之后，这意味着某人（例如 IT 部门）在拦截 HTTPS 流量，将其解密后又使用自签名证书对其加密  。

+ 你正在运行的软件（如防病毒软件）正在将自签名 TLS/SSL 证书注入收到的 HTTPS 消息中。

存储资源管理器在遇到其中一个这样的“自签名证书”时，将再也无法判断收到的 HTTPS 消息是否已被篡改。 但若拥有一份自签名证书的副本，则可让存储资源管理器信任它。 若无法确定谁在注入证书，则可自行尝试通过以下步骤找到它：

1. 安装 OpenSSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html)（任意轻量版本均可）
     - Mac 和 Linux：应包含在操作系统中
2. 运行 OpenSSL
    - Windows:转到安装目录，然后转到“/bin/”，然后双击“openssl.exe”   。
    - Mac 和 Linux：从终端执行“openssl” 
3. 执行 `s_client -showcerts -connect microsoft.com:443`
4. 查找自签名证书。 若不确定哪个证书为自签名，则请查找使用者（“s:”）和证书颁发者（“i:”）相同的任意位置。
5.  找到任何自签名证书后，将每个证书中从“-----BEGIN CERTIFICATE-----”（含）到“-----END CERTIFICATE-----”（含）的部分复制和粘贴到新的 .cer 文件。  
6.  打开存储资源管理器，然后转到“编辑”   >   “SSL 证书” >   “导入证书”。 使用文件选取器查找、选择和打开所创建的 .cer 文件。

若通过上述步骤无法找到任何自签名证书，可通过发送反馈以获取更多帮助。

#### <a name="unable-to-retrieve-subscriptions"></a>无法检索订阅

若成功登录后无法检索订阅，请执行以下操作：

- 通过登录 [Azure 门户](https://portal.azure.com/)验证帐户是否有权访问该订阅
- 请确保使用正确的环境登录（[Azure](https://portal.azure.com/)、[Azure 中国](https://portal.azure.cn/)、[Azure 德国](https://portal.microsoftazure.de/)、[Azure 美国政府](https://portal.azure.us/)或自定义环境/Azure Stack）
- 如果使用代理，请确保已正确配置存储资源管理器代理
- 尝试移除并重新添加帐户
- 尝试从主目录删除以下文件（例如：C:\Users\ContosoUser），然后重新添加帐户：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 登录查询任何错误消息时，请查看开发人员工具控制台（F12）

![console](./media/storage-explorer/console.png)

#### <a name="unable-to-see-the-authentication-page"></a>无法查看身份验证页

如果无法看到身份验证页面：

- 根据连接速度不同，加载登录页面可能需要一会儿，请至少等待一分钟再关闭身份验证对话框
- 如果使用代理，请确保已正确配置存储资源管理器代理
- 按 F12 键显示开发人员控制台。 从开发人员控制台查看响应，看能否找到身份验证不起作用的任何线索

#### <a name="cannot-remove-account"></a>无法删除帐户

如果无法移除帐户，或重新验证链接不起作用

- 尝试从主目录删除以下文件，然后重新添加帐户：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 若要删除附加了 SAS 的存储资源，请删除：
  - %AppData%/StorageExplorer 文件夹（对于 Windows）
  - Mac 中的 /Users/<your_name>/Library/Application SUpport/StorageExplorer
  - ~/.config/StorageExplorer（对于 Linux）
  - 删除这些文件之后，**需要重新输入所有凭据**


### <a name="httphttps-proxy-issue"></a>Http/Https 代理问题

在 ASE 中配置 http/https 代理时，无法列出左侧树中的 Azure Cosmos DB 节点。 目前可以在 Azure 门户中使用 Azure Cosmos DB 数据资源管理器作为解决方法。

### <a name="development-node-under-local-and-attached-node-issue"></a>“本地和附加”节点下的“开发”节点问题

在左侧树中单击“本地和附加”节点下的“开发”节点时没有响应。  此行为是预期的行为。 下一版本会支持 Azure Cosmos DB 本地模拟器。

![开发节点](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>在“本地和附加”节点中附加 Azure Cosmos DB 帐户错误

如果在“本地和附加”节点中附加 Azure Cosmos DB 帐户时看到以下错误，则请检查是否使用了正确的连接字符串。

![在“本地和附加”中附加 Azure Cosmos DB 错误](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>展开 Azure Cosmos DB 节点错误

尝试展开左侧的树节点时可能会看到以下错误。

![展开错误](./media/storage-explorer/expand-error.png)

请尝试以下建议：

- 检查 Azure Cosmos DB 帐户是否正在进行预配，然后在帐户成功创建以后再次进行尝试。
- 如果帐户位于“快速访问”节点或“本地和附加”节点下，则请检查该帐户是否已删除。 如果是这样，则需手动删除节点。

## <a name="contact-us"></a>联系我们

如果任何解决方案都不适合你，请将电子邮件发送到 Azure Cosmos DB 开发工具[cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)团队（），其中包含有关问题的详细信息，以解决问题。

## <a name="next-steps"></a>后续步骤

* 观看以下视频，了解如何在 Azure 存储资源管理器中使用 Azure Cosmos DB：[在 Azure 存储资源管理器中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[存储资源管理器入门](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)中了解有关存储资源管理器和连接更多服务的详细信息。
