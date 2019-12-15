---
title: 使用区块链数据管理器来更新 Azure Cosmos DB - Azure 区块链服务
description: 使用适用于 Azure 区块链服务的区块链数据管理器向 Azure Cosmos DB 发送区块链数据
ms.date: 12/04/2019
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 79c39d9883b5ba618e368b0ff6d3e95f1af5bd96
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977382"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>教程：使用区块链数据管理器向 Azure Cosmos DB 发送数据

在本教程中，将区块链数据管理器用于 Azure 区块链服务，以便在 Azure Cosmos DB 中记录区块链事务数据。 区块链数据管理器捕获、转换区块链账本数据并将其传送到 Azure 事件网格主题。 通过 Azure 事件网格，可以使用 Azure 逻辑应用连接器在 Azure Cosmos DB 数据库中创建文档。 完成教程后，可以在 Azure Cosmos DB 数据资源管理器中浏览区块链事务数据。

[![区块链事务详细信息](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建区块链数据管理器实例
> * 添加区块链应用程序以解码事务属性和事件
> * 创建 Azure Cosmos DB 帐户和数据库来存储事务数据
> * 创建 Azure 逻辑应用，将 Azure 事件网格主题连接到 Azure Cosmos DB
> * 将事务发送到区块链账本
> * 查看 Azure Cosmos DB 中的已解码事务数据

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完整[快速入门：使用 Azure 门户创建区块链成员](create-member.md)或[快速入门：通过 Azure CLI 创建 Azure 区块链服务区块链成员](create-member-cli.md)
* 完整[快速入门：使用 Visual Studio Code 连接到 Azure 区块链服务联盟网络](connect-vscode.md)。 本快速入门将引导你安装[适用于 Ethereum 的 Azure 区块链开发工具包](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)并设置区块链开发环境。
* 完成[教程：使用 Visual Studio Code 创建、生成和部署智能合同](send-transaction.md)。 本教程演示如何创建示例智能合同。
* 创建[事件网格主题](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件网格中的事件处理程序](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>创建实例

区块链数据管理器实例连接并监控 Azure 区块链服务事务节点。 实例从事务节点捕获所有原始块和原始事务数据。 出站连接将区块链数据发送到 Azure 事件网格。 创建实例时，可以配置单个出站连接。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 转到在先决条件[快速入门：使用 Azure 门户创建区块链成员](create-member.md)中创建的 Azure 区块链服务成员。 选择“区块链数据管理器”  。
1. 选择 **添加** 。

    ![添加“区块链数据管理器”](./media/data-manager-cosmosdb/add-instance.png)

    输入以下详细信息：

    设置 | 示例 | 说明
    --------|---------|------------
    名称 | mywatcher | 为连接的区块链数据管理器输入唯一的名称。
    事务节点 | myblockchainmember | 选择在先决条件中创建的 Azure 区块链服务成员的默认事务节点。
    连接名称 | cosmosdb | 输入用于发送区块链事务数据的出站连接的唯一名称。
    事件网格终结点 | myTopic | 选择在先决条件中创建的事件网格主题。 注意：区块链数据管理器实例和事件网格主题必须位于同一订阅中。

1. 选择“确定”  。

    创建区块链数据管理器实例需要不到一分钟的时间。 部署实例后，它会自动启动。 正在运行的区块链数据管理器实例从事务节点捕获区块链事件，并将数据发送到事件网格。

## <a name="add-application"></a>添加应用程序

添加“helloblockchain”  区块链应用程序，以便区块链数据管理器解码事件和属性状态。 区块链数据管理器需要智能合同 ABI 和字节码文件来添加应用程序。

### <a name="get-contract-abi-and-bytecode"></a>获取合同 ABI 和字节码

合同 ABI 定义智能合同接口。 其中描述了如何与智能合同交互。 可以使用[适用于 Ethereum 扩展的 Azure 区块链开发工具包](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)将合同 ABI 复制到剪贴板。

1. 在“Visual Studio Code 资源管理器”窗格中，展开在先决条件[教程：   使用 Visual Studio Code 创建、生成和部署智能合同](send-transaction.md)中创建的“helloblockchain”Solidity 项目的“生成/合同”文件夹。
1. 右键单击合同元数据 JSON 文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 选择“复制合同 ABI”。 

    ![选择了“复制合同 ABI”的 Visual Studio Code 窗格](./media/data-manager-cosmosdb/abi-devkit.png)

    合同 ABI 将复制到剪贴板。

1. 将 abi  数组保存为 JSON 文件。 例如：abi.json  。 在稍后的步骤中会使用此文件。

区块链数据管理器需要为智能合同部署的字节码。 部署的字节码不同于智能合同字节码。 使用 Azure 区块链开发工具包扩展将字节码复制到剪贴板。

1. 在 Visual Studio Code 资源管理器窗格中，展开 Solidity 项目的 **build/contracts** 文件夹。
1. 右键单击合同元数据 JSON 文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 选择“复制事务字节码”  。

    ![选择了“复制事务字节码”的 Visual Studio Code 窗格](./media/data-manager-cosmosdb/bytecode-devkit.png)

    字节码将复制到剪贴板。

1. 将“字节码”值保存为 JSON 文件  。 例如：“bytecode.json”  。 在稍后的步骤中会使用此文件。

下面的示例演示的是如何在 VS Code 编辑器中打开“abi.json”  和“bytecode.json”  。 文件应看起来很相似。

![abi.json 和 bytecode.json 文件的示例](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>创建合同 ABI 和字节码 URL

添加应用程序时，区块链数据管理器要求由 URL 访问合同 ABI 和字节码文件。 可以使用 Azure 存储帐户提供可私下访问的 URL。

#### <a name="create-storage-account"></a>创建存储帐户

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>上传合同文件

1. 为存储帐户新建容器。 选择“容器”>“容器”  。

    ![创建存储帐户容器](./media/data-manager-cosmosdb/create-container.png)

    | 设置 | 说明 |
    |---------|-------------|
    | 名称  | 为该容器命名。 例如，“smartcontract”  |
    | 公共访问级别 | 选择“专用(没有匿名访问权限)”  |

1. 选择“确定”  创建容器。
1. 先选择该容器，然后选择“上传”  。
1. 选择在[获取合同 ABI 和字节码](#get-contract-abi-and-bytecode)部分中创建的两个 JSON 文件。

    ![上传 blob](./media/data-manager-cosmosdb/upload-blobs.png)

    选择“上传”。 

#### <a name="generate-url"></a>生成 URL

对于各个 blob，生成共享访问签名。

1. 选择“ABI JSON blob”。
1. 选择“生成 SAS” 
1. 设置所需的访问签名到期时间，然后选择“生成 blob SAS 令牌和 URL”  。

    ![生成 SAS 令牌](./media/data-manager-cosmosdb/generate-sas.png)

1. 复制“Blob SAS URL”  ，以便在下一部分使用。
1. 重复字节码 JSON blob 的[生成 URL](#generate-url) 步骤。

### <a name="add-helloblockchain-application-to-instance"></a>将 helloblockchain 应用程序添加到实例

1. 从实例列表中选择区块链数据管理器实例。
1. 选择“区块链应用程序”  。
1. 选择 **添加** 。

    ![添加区块链应用程序](./media/data-manager-cosmosdb/add-application.png)

    输入区块链应用程序的名称以及智能合同 ABI 和字节码 URL。

    设置 | 说明
    --------|------------
    名称 | 输入要跟踪的区块链应用程序的唯一名称。
    合同 ABI | 合同 ABI 文件的 URL 路径。 有关详细信息，请参阅[创建合同 ABI 和字节码 URL](#create-contract-abi-and-bytecode-url)。
    合同字节码 | 字节码文件的 URL 路径。 有关详细信息，请参阅[创建合同 ABI 和字节码 URL](#create-contract-abi-and-bytecode-url)。

1. 选择“确定”  。

    创建应用程序后，应用程序将显示在区块链应用程序的列表中。

    ![区块链应用程序列表](./media/data-manager-cosmosdb/artifact-list.png)

可以删除 Azure 存储帐户，也可以使用它来配置更多区块链应用程序。 如果要删除 Azure 存储帐户，可以删除该资源组。 删除资源组也会删除相关联的存储帐户，以及与资源组相关联的任何其他资源。

## <a name="create-azure-cosmos-db"></a>创建 Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>添加数据库和容器

可以使用 Azure 门户中的数据资源管理器来创建数据库和容器。

1. 在 Azure Cosmos DB 帐户页上的左侧导航栏中选择“数据资源管理器”，然后选择“新建容器”。  
1. 在“添加容器”窗格中，输入新容器的设置。 

    ![添加容器设置](./media/data-manager-cosmosdb/add-container.png)

    | 设置 | 说明
    |---------|-------------|
    | 数据库 ID | 输入“blockchain-data”  作为新数据库的名称。 |
    | 吞吐量 | 将吞吐量保留为每秒 **400** 个请求单位 (RU/s)。 如果想要减少延迟，以后可以增加吞吐量。|
    | 容器 ID | 输入“Messages”  作为新容器的名称。 |
    | 分区键 | 使用“/MessageType”  作为分区键。 |

1. 选择“确定”  。 数据资源管理器将显示新建的数据库和容器。

## <a name="create-logic-app"></a>创建逻辑应用

需要集成系统和服务时，Azure 逻辑应用可帮助你计划和自动化业务流程和工作流。 可以使用逻辑应用将事件网格连接到 Azure Cosmos DB。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “集成” > “逻辑应用”    。
1. 提供有关在何处创建逻辑应用的详细信息。 完成后，选择“创建”  。

    有关创建逻辑应用的详细信息，请参阅[使用 Azure 逻辑应用创建自动化工作流](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在 Azure 部署你的应用后，请选择你的逻辑应用资源。
1. 在逻辑应用设计器中的“模板”下，选择“空白逻辑应用”。  

### <a name="add-event-grid-trigger"></a>添加事件网格触发器

每个逻辑应用都必须从触发器开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来启动并运行工作流。 使用 Azure 事件网格触发器将区块链事务数据从事件网格发送到 Cosmos DB。

1. 在逻辑应用设计器中，搜索并选择“Azure 事件网格”  连接器。
1. 从“触发器”  选项卡中，选择“当资源事件发生时”  。
1. 创建与事件网格主题的 API 连接。

    ![事件网格触发器设置](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | 设置 | 说明
    |---------|-------------|
    | 订阅 | 选择包含事件网格主题的订阅。 |
    | 资源类型 | 选择“Microsoft.EventGrid.Topics”  。 |
    | 资源名称 | 选择区块链数据管理器向其发送事务数据消息的事件网格主题的名称。 |

### <a name="add-cosmos-db-action"></a>添加 Cosmos DB 操作

添加操作以在每个事务的 Cosmos DB 中创建文档。 使用事务消息类型作为分区键以便对消息进行分类。

1. 选择“新建步骤”。 
1. 在“选择操作”  上，搜索“Azure Cosmos DB”  。
1. 选择“Azure Cosmos DB”>“操作”>“创建或更新文档”  。
1. 创建与 Cosmos DB 数据库的 API 连接。

    ![Cosmos DB 连接设置](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | 设置 | 说明
    |---------|-------------|
    | 连接名称 | 选择包含事件网格主题的订阅。 |
    | DocumentDB 帐户 | 选择在[创建 Azure Cosmos DB 帐户](#create-azure-cosmos-db)部分中创建的 DocumentDB 帐户。 |

1. 输入先前在[添加数据库和容器](#add-a-database-and-container)部分中创建的 Azure Cosmos DB 的“数据库 ID”  和“集合 ID”  。

1. 选择“文档”设置  。 在“添加动态内容”  弹出窗口中，选择“表达式”  ，然后复制并粘贴以下表达式：

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    表达式将获取消息的数据部分，并将 ID 设置为时间戳值。

1. 选择“添加新参数”  并选择“分区键值”  。
1. 将“分区键值”  设为 `"@{triggerBody()['data']['MessageType']}"`。 该值必须用双引号括起来。

    ![具有 Cosmos DB 设置的逻辑应用设计器](./media/data-manager-cosmosdb/create-action.png)

    该值将分区键设置为事务消息类型。

1. 选择“保存”。 

逻辑应用监视事件网格主题。 当从区块链数据管理器发送新的事务消息时，逻辑应用会在 Cosmos DB 中创建一个文档。

## <a name="send-a-transaction"></a>发送事务

接下来，将事务发送到区块链账本以测试所创建的内容。 使用在先决条件[教程：  使用 Visual Studio Code 创建、生成和部署智能合同](send-transaction.md)中创建的“sendrequest.js”脚本。

在 VS Code 的终端窗格中，使用 Truffle 针对联盟区块链网络执行该脚本。 在终端窗格菜单栏中选择“终端”选项卡，并从下拉列表中选择“PowerShell”。  

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

将 \<blockchain network\> 替换为 **truffle-config.js** 中定义的区块链网络名称。

![添加事务](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>查看事务数据

现在，你已将区块链数据管理器连接到 Azure Cosmos DB，你可以在 Cosmos DB 数据资源管理器中查看区块链事务消息。

1. 转到 Cosmos DB 数据资源管理器视图。 例如，“cosmosdb-blockchain”>“数据资源管理器”>“区块链-数据”>“消息”>“项目”  。

    ![Cosmos DB 数据资源管理器](./media/data-manager-cosmosdb/data-explorer.png)

    数据资源管理器列出在 Cosmos DB 数据库中创建的区块链数据消息。

1. 通过选择“项 ID”浏览消息，并查找具有匹配事务哈希的消息。

    [![区块链事务详细信息](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    原始事务消息包含有关该事务的详细信息。 但是，属性信息已加密。

    由于已将 HelloBlockchain 智能合同添加到区块链数据管理器实例，因此还会发送“ContractProperties”  消息类型，其中包含已解码的属性信息。

1. 为事务查找“ContractProperties”  消息。 它应该是列表中的下一条消息。

    [![区块链事务详细信息](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    **DecodedProperties** 数组包含事务的属性。

祝贺你！ 已成功使用区块链数据管理器和 Azure Cosmos DB 创建事务消息资源管理器。

## <a name="clean-up-resources"></a>清理资源

当不再需要时，可以删除用于本教程的资源和资源组。 删除资源组的步骤：

1. 在 Azure 门户中，导航至左侧导航窗格中的“资源组”  ，然后选择要删除的资源组。
1. 选择“删除资源组”  。 输入资源组名称确认删除并选择“删除”  。

## <a name="next-steps"></a>后续步骤

详细了解如何与区块链账本集成。

> [!div class="nextstepaction"]
> [将 Ethereum 区块链连接器与 Azure 逻辑应用配合使用](ethereum-logic-app.md)
