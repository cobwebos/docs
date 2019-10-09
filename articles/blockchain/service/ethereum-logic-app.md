---
title: 如何将 Ethereum 区块链连接器与 Azure 逻辑应用配合使用
description: 如何将 Ethereum 区块链连接器与 Azure 逻辑应用配合使用，以触发智能合同函数并响应智能合同事件。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720670"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>如何将 Ethereum 区块链连接器与 Azure 逻辑应用配合使用

将 [Ethereum 区块链连接器](https://docs.microsoft.com/connectors/blockchainethereum/)与 [Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)配合使用可以执行智能合同操作和响应智能合同事件。 例如，可以创建一个基于 REST 的微服务用于从区块链账本返回信息。 使用逻辑应用可以接受查询区块链账本中存储的信息的 HTTP 请求。

## <a name="prerequisites"></a>先决条件

* 完成可选的前提教程[快速入门：使用 Visual Studio Code 连接到 Azure 区块链服务联盟网络](connect-vscode.md)。 本快速入门将引导你安装[适用于 Ethereum 的 Azure 区块链开发工具包](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)并设置区块链开发环境。

## <a name="create-a-logic-app"></a>创建逻辑应用

需要集成系统和服务时，Azure 逻辑应用可帮助你计划、自动化业务流程和工作流。 首先，创建使用 Ethereum 区块链连接器的逻辑。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”>“集成”>“逻辑应用”。 
1. 在“创建逻辑应用”下，提供有关在何处创建逻辑应用的详细信息。  完成后，选择“创建”  。

    有关创建 Azure 逻辑应用的详细信息，请参阅[创建逻辑应用](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在 Azure 部署你的应用后，请选择你的逻辑应用资源。
1. 在逻辑应用设计器中的“模板”下，选择“空白逻辑应用”。  

每个逻辑应用都必须从触发器开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来启动并运行工作流。

Ethereum 区块链连接器有一个触发器和多个操作。 使用哪个触发器或操作取决于具体的方案。

根据如下所述选择相应的部分：

* 如果区块链上发生某个事件时工作流将会触发，请[使用事件触发器](#use-the-event-trigger)。
* 如果工作流查询或部署智能合同，请[使用操作](#use-actions)。
* 如果工作流遵循常用方案，请[使用开发人员工具包生成工作流](#generate-a-workflow)。

## <a name="use-the-event-trigger"></a>使用事件触发器

如果希望逻辑应用在智能合同事件发生后运行，请使用 Ethereum 区块链事件触发器。 例如，可以在调用智能合同函数时发送电子邮件。

1. 在逻辑应用设计器中，选择 Ethereum 区块链连接器。
1. 在“触发器”选项卡中，选择“发生智能合同事件时”。  
1. 更改或创建与 Azure 区块链服务的 [API 连接](#create-an-api-connection)。
1. 输入要检查其事件的智能合同的相关详细信息。

    ![事件触发器属性](./media/ethereum-logic-app/event-properties.png)

    | 属性 | 说明 |
    |----------|-------------|
    | **合同 ABI** | 合同应用程序二进制接口 (ABI) 定义智能合同接口。 如何[获取合同 ABI](#get-contract-abi)。 |
    | **智能合同地址** | 合同地址是 Ethereum 区块链上的智能合同目标地址。 如何[获取合同地址](#get-contract-address)。 |
    | **事件名称** | 选择要检查的智能合同事件。 该事件触发逻辑应用。 |
    | **间隔**和**频率** | 选择检查事件的频率。 |

1. 选择“保存”。 

若要完成逻辑应用，可添加一个新步骤用于根据 Ethereum 区块链事件触发器执行操作。 例如发送电子邮件。

## <a name="use-actions"></a>使用操作

如果你希望逻辑应用针对区块链账本执行操作，请使用 Ethereum 区块链操作。 例如，创建一个基于 REST 的微服务，用于在向逻辑应用发出 HTTP 请求时调用智能合同函数。

连接器操作需要触发器。 可以使用 Ethereum 区块链连接器操作作为某个触发器后面的下一步骤。 例如，微服务的 HTTP 请求触发器。

1. 在逻辑应用设计器中，选择某个触发器后面的“新建步骤”。 
1. 选择 Ethereum 区块链连接器。
1. 在“操作”选项卡中选择一个可用的操作。 

    ![操作属性](./media/ethereum-logic-app/action-properties.png)

1. 更改或创建与 Azure 区块链服务的 [API 连接](#create-an-api-connection)。
1. 根据所选的操作，提供有关智能合同函数的以下详细信息。

    | 属性 | 说明 |
    |----------|-------------|
    | **合同 ABI** | 合同应用程序二进制接口 (ABI) 定义智能合同接口。 如何[获取合同 ABI](#get-contract-abi)。 |
    | **合同字节码** | 编译的智能合同字节码。 如何[获取合同字节码](#get-contract-bytecode)。 |
    | **智能合同地址** | 合同地址是 Ethereum 区块链上的智能合同目标地址。 如何[获取合同地址](#get-contract-address)。 |
    | **智能合同函数名称** | 选择操作的智能合同函数名称。 此列表中填充了合同 ABI 中的详细信息。 |

    选择智能合同函数名称后，可能会看到函数参数的必填字段。 输入方案所需的值或动态内容。

现在可以使用你的逻辑应用。 触发逻辑应用事件后，Ethereum 区块链操作将会运行。 例如，某个 HTTP 请求触发器运行 Ethereum 区块链操作来查询智能合同状态值，从而生成返回该值的 HTTP 响应。

## <a name="generate-a-workflow"></a>生成工作流

“适用于 Ethereum Visual Studio Code 的 Azure 区块链开发工具包”扩展可为常用方案生成逻辑应用工作流。 有四个可用方案：

* 将数据发布到 Azure SQL 数据库
* 将事件发布到 Azure 事件网格或 Azure 服务总线
* 报告发布
* 基于 REST 的微服务

 Azure 区块链开发工具包使用 Truffle 来简化区块链开发。 若要基于智能合同生成逻辑应用，需要对智能合同使用 Truffle 解决方案。 还需要连接到 Azure 区块链服务联盟网络。 有关详细信息，请参阅[使用 Visual Studio Code 连接到 Azure 区块链服务联盟网络快速入门](connect-vscode.md)。

例如，以下步骤根据快速入门 **HelloBlockchain** 智能合同生成基于 REST 的微服务逻辑应用：

1. 在 VS Code 资源管理器侧栏中，展开解决方案中的“contracts”文件夹。 
1. 右键单击“HelloBlockchain.sol”，然后从菜单中选择“生成智能合同的微服务”。  

    ![生成逻辑应用](./media/ethereum-logic-app/generate-logic-app.png)

1. 在命令面板中，选择“逻辑应用”。 
1. 输入**合同地址**。 有关详细信息，请参阅[如何获取合同地址](#get-contract-address)。
1. 选择逻辑应用的 Azure 订阅和资源组。

    逻辑应用配置和代码文件是在 **generatedLogicApp** 目录中生成的。

1. 查看 **generatedLogicApp/HelloBlockchain** 目录。 每个智能合同函数、事件和属性都有一个逻辑应用 JSON 文件。
1. 打开 **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** 文件并复制内容。

    ![RequestMessage 属性的 JSON](./media/ethereum-logic-app/requestmessage.png)

1. 在逻辑应用中，选择“逻辑应用代码视图”。  将现有 JSON 替换为生成的逻辑应用 JSON。

    ![替换代码视图中的逻辑应用配置](./media/ethereum-logic-app/code-view.png)

1. 选择“设计器”切换到设计器视图。 
1. 逻辑应用包含方案的基本步骤。 但是，你需要更新 Ethereum 区块链连接器的配置详细信息。
1. 选择“连接”步骤，并更改或创建与 Azure 区块链服务的 [API 连接](#create-an-api-connection)。 

    ![微服务逻辑应用](./media/ethereum-logic-app/microservice-logic-app.png)

1. 现在可以使用你的逻辑应用。 若要测试基于 REST 的微服务，请向逻辑应用请求 URL 发出 HTTP POST 请求。 复制“收到 HTTP 请求时”步骤中的“HTTP POST URL”。  

    ![HTTP POST URL](./media/ethereum-logic-app/post-url.png)

1. 使用 cURL 创建 HTTP POST 请求。 将占位符文本 **\<HTTP POST URL\>** 替换为在上一步骤中复制的 URL。

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    cURL 命令将从逻辑应用返回响应。 在本例中，响应为 **RequestMessage** 智能合同函数的输出。

    ![RequestMessage 属性输出](./media/ethereum-logic-app/curl.png)

有关使用开发工具包的详细信息，请参阅[适用于 Ethereum 的 Azure 区块链开发工具包 Wiki](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) 页。

## <a name="create-an-api-connection"></a>创建 API 连接

Ethereum 区块链连接器要求与区块链建立 API 连接。 可将 API 连接器用于多个逻辑应用。 有些属性是必需的，而有些属性不是必需的，具体取决于方案。

> [!IMPORTANT]
> 在区块链中创建事务需要使用私钥或帐户地址和密码。 只需要一种形式的身份验证。 不需要同时提供私钥和帐户详细信息。 查询合同不需要事务。 如果使用查询合同状态的操作，则不需要提供私钥或帐户地址和密码。

若要与 Azure 区块链服务成员建立连接，可能需要提供以下列表中的属性，具体取决于方案。

| 属性 | 说明 |
|----------|-------------|
|**连接名称** | API 连接的名称。 必需。 |
|**Ethereum RPC 终结点** | Azure 区块链服务事务节点的 HTTP 地址。 必需。 如何[获取 RPC 终结点](#get-rpc-endpoint)。 |
|**私钥** | Ethereum 帐户私钥。 事务需要私钥或帐户地址和密码。 如何[获取私钥](#get-private-key)。 |
|**Account address** | Azure 区块链服务成员帐户地址。 事务需要私钥或帐户地址和密码。 如何[获取帐户地址](#get-account-address)。 |
|**帐户密码** | 帐户密码是在创建成员时设置的。 有关重置密码的信息，请参阅 [Ethereum 帐户](consortium.md#ethereum-account)。|

## <a name="get-rpc-endpoint"></a>获取 RPC 终结点

需要使用 Azure 区块链服务 RPC 终结点地址连接到区块链网络。 可以使用适用于 Ethereum 的 Azure 区块链开发工具包或 Azure 门户获取终结点地址。

**使用开发工具包：**

1. 在 Visual Studio Code 中的“Azure 区块链服务”下，右键单击“联盟”。 
1. 选择“复制 RPC 终结点”。 

    ![复制 RPC 终结点](./media/ethereum-logic-app/devkit-rpc.png)

    RPC 终结点将复制到剪贴板。

**使用 Azure 门户：**

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到你的 Azure 区块链服务成员。 选择“事务节点”  和默认事务节点链接。

    ![选择默认事务节点](./media/ethereum-logic-app/transaction-nodes.png)

1. 选择“连接字符串”>“访问密钥”  。
1. 从“HTTPS (访问密钥 1)”或“访问密钥 2”中复制终结点地址。 

    ![连接字符串](./media/ethereum-logic-app/connection-string.png)

    RPC 终结点是一个 HTTPS URL，其中包含 Azure 区块链服务成员事务节点的地址和访问密钥。

## <a name="get-private-key"></a>获取私钥

在将事务发送到区块链时，可以使用 Ethereum 帐户私钥进行身份验证。 Ethereum 帐户公钥和私钥是基于 12 个单词的助记键生成的。 适用于 Ethereum 的 Azure 区块链开发工具包将在你连接到 Azure 区块链服务联盟成员时生成助记键。 可以使用开发工具包扩展来获取终结点地址。

1. 在 Visual Studio Code 中打开命令面板 (F1)。
1. 选择“Azure 区块链:  检索私钥”。
1. 选择连接到联盟成员时保存的助记键。

    ![选择助记键](./media/ethereum-logic-app/private-key.png)

    私钥将复制到剪贴板。

## <a name="get-account-address"></a>获取帐户地址

在将事务发送到区块链时，可以使用成员帐户和密码进行身份验证。 密码是在创建成员时设置的。

1. 在 Azure 门户中，转到 Azure 区块链服务的概述页。
1. 复制**成员帐户**地址。

    ![复制成员帐户](./media/ethereum-logic-app/member-account.png)

有关帐户地址和密码的详细信息，请参阅 [Ethereum 帐户](consortium.md#ethereum-account)。

## <a name="get-contract-abi"></a>获取合同 ABI

合同应用程序二进制接口 (ABI) 定义智能合同接口。 其中描述了如何与智能合同交互。 可以使用适用于 Ethereum 的 Azure 区块链开发工具包或者从 Solidity 编译器合同元数据文件获取合同 ABI。

**使用开发工具包：**

如果使用开发工具包或 Truffle 生成了智能合同，则可以使用扩展将合同 ABI 复制到剪贴板。

1. 在 Visual Studio Code 资源管理器窗格中，展开 Solidity 项目的“build/contracts”文件夹。 
1. 右键单击合同元数据 JSON 文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 选择“复制合同 ABI”。 

    ![使用开发工具包复制合同 ABI](./media/ethereum-logic-app/abi-devkit.png)

    合同 ABI 将复制到剪贴板。

**使用合同元数据文件：**

1. 打开 Solidity 项目的 **build/contracts** 文件夹中包含的合同元数据文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 在 JSON 文件中找到 **abi** 节。
1. 复制 **abi** JSON 数组。

    ![元数据中的合同 ABI 节](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>获取合同字节码

合同字节码是 Ethereum 虚拟机执行的已编译智能合同。 可以使用适用于 Ethereum 的 Azure 区块链开发工具包或者从 Solidity 编译器获取合同字节码。

**使用开发工具包：**

如果使用开发工具包或 Truffle 生成了智能合同，则可以使用扩展将合同字节码复制到剪贴板。

1. 在 Visual Studio Code 资源管理器窗格中，展开 Solidity 项目的“build/contracts”文件夹。 
1. 右键单击合同元数据 JSON 文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 选择“复制合同字节码”。 

    ![使用开发工具包复制合同字节码](./media/ethereum-logic-app/bytecode-devkit.png)

    合同字节码将复制到剪贴板。

**使用合同元数据文件：**

1. 打开 Solidity 项目的 **build/contracts** 文件夹中包含的合同元数据文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 在 JSON 文件中找到 **bytecode** 元素。
1. 复制 **bytecode** 值。

    ![使用元数据复制字节码](./media/ethereum-logic-app/bytecode-metadata.png)

**使用 Solidity 编译器：**

使用命令 `solc --bin <smart contract>.sol` 生成合同字节码。

## <a name="get-contract-address"></a>获取合同地址

合同地址是 Ethereum 区块链上的智能合同目标地址。 使用此地址发送事务或查询智能合同的状态。 可以从 Truffle 迁移输出或合同元数据文件获取合同地址。

**使用 Truffle 迁移输出：**

Truffle 在部署智能合同后会显示合同地址。 复制输出中的**合同地址**。

![Truffle 输出中的合同地址](./media/ethereum-logic-app/contract-address-truffle.png)

**使用合同元数据文件：**

1. 打开 Solidity 项目的 **build/contracts** 文件夹中包含的合同元数据文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 在 JSON 文件中找到 **networks** 节。
1. 专用网络由整数网络 ID 标识。 在 network 节中找到 address 值。
1. 复制 **address** 值。

![元数据中的合同地址](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>后续步骤

观看[使用 Azure 逻辑应用连接区块链的常见方案](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)。
