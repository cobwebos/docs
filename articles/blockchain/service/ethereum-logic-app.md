---
title: 在 Azure 逻辑应用中使用以太坊区块链连接器
description: 在 Azure 逻辑应用中使用以太坊区块链连接器来触发智能协定函数并响应智能协定事件。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: bb23d6b9b42e1c51646765255870a14a1b5d39f7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579942"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>在 Azure 逻辑应用中使用以太坊区块链连接器

在[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)中使用[以太坊区块链连接器](https://docs.microsoft.com/connectors/blockchainethereum/)来执行智能协定操作并响应智能协定事件。 例如，假设要创建一个从区块链分类帐返回信息的基于 REST 的微服务。 使用逻辑应用，可以接受查询存储在区块链分类帐中的信息的 HTTP 请求。

## <a name="prerequisites"></a>先决条件

完成可选的先决条件[快速入门：使用 Visual Studio Code 连接到 Azure 区块链 Service 联合会网络](connect-vscode.md)。 快速入门指南介绍了如何安装[用于以太坊的 Azure 区块链开发工具包](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)并设置区块链开发环境。

## <a name="create-a-logic-app"></a>创建逻辑应用

当你需要集成系统和服务时，Azure 逻辑应用可帮助你安排和自动执行业务流程和工作流。 首先，使用以太坊区块链连接器创建逻辑。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” **“集成”** “逻辑应用” >  > 。
1. 在 "**创建逻辑应用**" 下，提供有关在何处创建逻辑应用的详细信息。 完成后，选择“创建”。

    有关创建逻辑应用的详细信息，请参阅[通过 Azure 逻辑应用创建自动化工作流](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在 Azure 部署应用后，选择逻辑应用资源。
1. 在逻辑应用设计器中的 "**模板**" 下，选择 "**空白逻辑应用**"。

每个逻辑应用都必须从触发器开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来启动并运行工作流。

以太坊区块链连接器具有一个触发器和多个操作。 使用哪种触发器或操作取决于方案。

如果你的工作流：

* 在区块链上发生事件时触发，[使用事件触发器](#use-the-event-trigger)。
* 查询或部署智能协定，请[使用操作](#use-actions)。
* 遵循常见方案，[使用开发人员工具包生成工作流](#generate-a-workflow)。

## <a name="use-the-event-trigger"></a>使用事件触发器

如果希望逻辑应用在智能协定事件发生后运行，请使用以太坊区块链事件触发器。 例如，你希望在调用智能协定函数时发送电子邮件。

1. 在逻辑应用设计器中，选择以太坊区块链连接器。
1. 从 "**触发器**" 选项卡中，选择**何时发生智能协定事件**。
1. 更改或创建与 Azure 区块链服务[的 API 连接](#create-an-api-connection)。
1. 输入有关要检查事件的智能协定的详细信息。

    ![具有事件触发器属性的逻辑应用设计器](./media/ethereum-logic-app/event-properties.png)

    | 属性 | 说明 |
    |----------|-------------|
    | **协定 ABI** | 协定应用程序二进制接口（ABI）定义智能协定接口。 有关详细信息，请参阅[获取协定 ABI](#get-the-contract-abi)。 |
    | **智能协定地址** | 协定地址是以太坊区块链上的智能协定目标地址。 有关详细信息，请参阅[获取协定地址](#get-the-contract-address)。 |
    | **事件名称** | 选择要检查的智能协定事件。 此事件将触发逻辑应用。 |
    | **间隔**和**频率** | 选择要检查事件的频率。 |

1. 选择“保存”。

若要完成逻辑应用，可添加一个新步骤，该步骤基于以太坊区块链事件触发器执行操作。 例如，发送一封电子邮件。

## <a name="use-actions"></a>使用操作

如果希望逻辑应用在区块链分类帐上执行操作，请使用以太坊区块链操作。 例如，你想要创建一个基于 REST 的微服务，它在向逻辑应用发出 HTTP 请求时调用智能协定函数。

连接器操作需要触发器。 可以使用以太坊区块链 connector 操作作为触发器后的下一步，如针对微服务的 HTTP 请求触发器。

1. 在逻辑应用设计器中，选择 "**新步骤**"。
1. 选择以太坊区块链连接器。
1. 从 "**操作**" 选项卡中，选择一个可用的操作。

    ![带有操作属性的逻辑应用设计器](./media/ethereum-logic-app/action-properties.png)

1. 更改或创建与 Azure 区块链服务[的 API 连接](#create-an-api-connection)。
1. 根据所选的操作，提供有关智能协定功能的以下详细信息。

    | 属性 | 说明 |
    |----------|-------------|
    | **协定 ABI** | 协定 ABI 定义智能协定接口。 有关详细信息，请参阅[获取协定 ABI](#get-the-contract-abi)。 |
    | **协定字节码** | 已编译的智能协定字节码。 有关详细信息，请参阅[获取协定字节码](#get-the-contract-bytecode)。 |
    | **智能协定地址** | 协定地址是以太坊区块链上的智能协定目标地址。 有关详细信息，请参阅[获取协定地址](#get-the-contract-address)。 |
    | **智能协定函数名称** | 选择操作的智能协定函数名称。 此列表是根据协定 ABI 中的详细信息填充的。 |

    选择智能协定函数名称后，你可能会看到函数参数所必需的字段。 输入方案所需的值或动态内容。

你现在可以使用逻辑应用。 触发逻辑应用事件后，以太坊区块链操作将运行。 例如，HTTP 请求触发器运行以太坊区块链操作来查询智能协定状态值。 此查询将生成返回值的 HTTP 响应。

## <a name="generate-a-workflow"></a>生成工作流

适用于以太坊 Visual Studio Code 扩展的 Azure 区块链开发工具包可以为常见方案生成逻辑应用工作流。 提供四种方案：

* 将数据发布到 Azure SQL 数据库实例
* 将事件发布到 Azure 事件网格或 Azure 服务总线的实例
* 报表发布
* 基于 REST 的微服务

 Azure 区块链开发工具包使用 Truffle 简化区块链开发。 若要基于智能协定生成逻辑应用，需要智能协定的 Truffle 解决方案。 还需要连接到 Azure 区块链 Service 联合会网络。 有关详细信息，请参阅[使用 Visual Studio Code 连接到 Azure 区块链 Service 联合会网络快速入门](connect-vscode.md)。

例如，以下步骤基于快速入门**HelloBlockchain**智能协定生成基于 REST 的微服务逻辑应用：

1. 在 "Visual Studio Code 资源管理器" 边栏中，展开解决方案中的 "**协定**" 文件夹。
1. 右键单击**HelloBlockchain** ，然后从菜单中选择 "**为智能协定生成微服务**"。

    ![带有为智能协定选择生成微服务的 Visual Studio Code 窗格](./media/ethereum-logic-app/generate-logic-app.png)

1. 在命令面板中，选择 "**逻辑应用**"。
1. 输入**协定地址**。 有关详细信息，请参阅[获取协定地址](#get-the-contract-address)。
1. 选择逻辑应用的 Azure 订阅和资源组。

    逻辑应用配置和代码文件在**generatedLogicApp**目录中生成。

1. 查看**generatedLogicApp/HelloBlockchain**目录。 每个智能协定函数、事件和属性都有一个逻辑应用 JSON 文件。
1. 打开**generatedLogicApp/HelloBlockchain/Service/属性。RequestMessage. 逻辑应用**文件并复制内容。

    ![包含要复制的代码的 JSON 文件](./media/ethereum-logic-app/requestmessage.png)

1. 在逻辑应用中，选择 "**逻辑应用代码视图**"。 将现有 JSON 替换为生成的逻辑应用 JSON。

    ![逻辑应用代码视图替换为替换的新应用代码](./media/ethereum-logic-app/code-view.png)

1. 选择 "**设计器**" 以切换到设计器视图。
1. 逻辑应用包括方案的基本步骤。 但是，需要更新以太坊区块链连接器的配置详细信息。
1. 选择 "**连接**" 步骤，并更改或创建到 Azure 区块链服务[的 API 连接](#create-an-api-connection)。

    ![具有连接选择的设计器视图](./media/ethereum-logic-app/microservice-logic-app.png)

1. 你现在可以使用逻辑应用。 若要测试基于 REST 的微服务，请向逻辑应用请求 URL 发出 HTTP POST 请求。 **当收到 http 请求时**，从复制**http POST URL**内容。

    ![包含 HTTP POST URL 的逻辑应用设计器窗格](./media/ethereum-logic-app/post-url.png)

1. 使用卷创建 HTTP POST 请求。 将 *\<HTTP POST URL\>* 的占位符文本替换为上一步中的 URL。

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    卷曲命令从逻辑应用返回响应。 在这种情况下，响应是**RequestMessage**智能协定函数的输出。

    ![RequestMessage 智能协定函数的代码输出](./media/ethereum-logic-app/curl.png)

有关使用开发工具包的详细信息，请参阅[适用于以太坊 wiki 的 Azure 区块链开发工具包页](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)。

## <a name="create-an-api-connection"></a>创建 API 连接

以太坊区块链连接器需要 API 到区块链的连接。 你可以为多个逻辑应用使用 API 连接器。 某些属性是必需的，其他属性则取决于你的方案。

> [!IMPORTANT]
> 在区块链上创建事务需要私钥或帐户地址和密码。 只需要一种形式的身份验证。 不需要提供私钥和帐户详细信息。 查询协定不需要事务。 如果使用的是查询协定状态的操作，则不需要私钥或帐户地址和密码。

为了帮助你建立与 Azure 区块链服务成员的连接，以下列表根据你的方案，可能需要一些属性。

| 属性 | 说明 |
|----------|-------------|
|**连接名称** | API 连接的名称。 必需。 |
|**以太坊 RPC 终结点** | Azure 区块链 Service transaction 节点的 HTTP 地址。 必需。 有关详细信息，请参阅[获取 RPC 终结点](#get-the-rpc-endpoint)。 |
|**私钥** | 以太坊帐户私钥。 事务需要私钥或帐户地址和密码。 有关详细信息，请参阅[获取私钥](#get-the-private-key)。 |
|**Account address** | Azure 区块链服务成员帐户地址。 事务需要私钥或帐户地址和密码。 有关详细信息，请参阅[获取帐户地址](#get-the-account-address)。 |
|**帐户密码** | 帐户密码是在创建成员时设置的。 有关重置密码的信息，请参阅[以太坊帐户](consortium.md#ethereum-account)。|

## <a name="get-the-rpc-endpoint"></a>获取 RPC 终结点

若要连接到区块链网络，需要 Azure 区块链服务 RPC 终结点地址。 可以使用适用于以太坊的 Azure 区块链开发工具包或 Azure 门户获取终结点地址。

**使用开发工具包：**

1. 在 Visual Studio Code 中的 " **Azure 区块链服务**" 下，右键单击 "联盟"。
1. 选择 "**复制 RPC 终结点地址**"。

    ![显示具有复制 RPC 终结点地址选择的联盟的 Visual Studio Code 窗格](./media/ethereum-logic-app/devkit-rpc.png)

    RPC 终结点将复制到剪贴板。

**使用 Azure 门户：**

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请参阅 Azure 区块链服务成员。 选择“事务节点”和默认事务节点链接。

    ![具有（默认节点）选择的 "事务节点" 页](./media/ethereum-logic-app/transaction-nodes.png)

1. 选择 "**连接字符串**" > **访问密钥**"。
1. 从**https （访问密钥1）** 或**https （访问密钥2）** 复制终结点地址。

    ![使用连接字符串访问密钥 Azure 门户](./media/ethereum-logic-app/connection-string.png)

    RPC 终结点是 HTTPS URL，其中包括 Azure 区块链 Service 成员事务节点的地址和访问密钥。

## <a name="get-the-private-key"></a>获取私钥

在将事务发送到区块链时，可以使用以太坊帐户的私钥进行身份验证。 你的以太坊帐户的公钥和私钥是从12个单词的助记键生成的。 适用于以太坊的 Azure 区块链开发工具包将在连接到 Azure 区块链 Service 联合会成员时生成助记键。 可以使用开发工具包扩展来获取终结点地址。

1. 在 Visual Studio Code 中，打开命令面板（F1）。
1. 选择 " **Azure 区块链：检索私钥**"。
1. 选择在连接到联合会成员时保存的助记键。

    ![命令面板，其中包含用于选择助记键的选项](./media/ethereum-logic-app/private-key.png)

    将私钥复制到剪贴板。

## <a name="get-the-account-address"></a>获取帐户地址

在将事务发送到区块链时，可以使用成员帐户和密码进行身份验证。 密码是在创建成员时设置的。

1. 在 Azure 门户中，请参阅 Azure 区块链服务概述页。
1. 复制**成员帐户**地址。

    ![带有成员帐户地址的 "概述" 页](./media/ethereum-logic-app/member-account.png)

有关帐户地址和密码的详细信息，请参阅[以太坊帐户](consortium.md#ethereum-account)。

## <a name="get-the-contract-abi"></a>获取协定 ABI

协定 ABI 定义智能协定接口。 它介绍了如何与智能协定交互。 可以通过使用适用于以太坊的 Azure 区块链开发工具包来获取协定 ABI。 你还可以从 "密度编译器" 创建的协定元数据文件获取它。

**使用开发工具包：**

如果你使用开发工具包或 Truffle 来构建你的智能协定，则可以使用扩展将协定 ABI 复制到剪贴板。

1. 在 "Visual Studio Code 资源管理器" 窗格中，展开 "密度" 项目的 "**生成"/"协定**" 文件夹。
1. 右键单击协定元数据 JSON 文件。 文件名为智能协定名称，后跟**json**扩展名。
1. 选择 "**复制协定 ABI**"。

    ![带有复制协定 ABI 选择的 Visual Studio Code 窗格](./media/ethereum-logic-app/abi-devkit.png)

    将协定 ABI 复制到剪贴板。

**使用协定元数据文件：**

1. 打开包含在你的密度项目的**生成/协定**文件夹中的协定元数据文件。 文件名为智能协定名称，后跟**json**扩展名。
1. 在 JSON 文件中查找**abi**部分。
1. 复制**abi** JSON 数组。

    ![协定元数据文件中的 ABI 代码](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>获取协定字节码

协定字节码是以太坊虚拟机执行的编译的智能协定。 可以通过使用适用于以太坊的 Azure 区块链开发工具包来获取协定字节码。 你还可以从 "密度编译器" 获取它。

**使用开发工具包：**

如果你使用开发工具包或 Truffle 来构建你的智能协定，则可以使用扩展将协定字节码复制到剪贴板。

1. 在 "Visual Studio Code 资源管理器" 窗格中，展开 "密度" 项目的 "**生成"/"协定**" 文件夹。
1. 右键单击协定元数据 JSON 文件。 文件名为智能协定名称，后跟**json**扩展名。
1. 选择 "**复制协定字节码**"。

    ![带有复制协定字节码选择的 Visual Studio Code 窗格](./media/ethereum-logic-app/bytecode-devkit.png)

    协定字节码将被复制到剪贴板。

**使用协定元数据文件：**

1. 打开包含在你的密度项目的**生成/协定**文件夹中的协定元数据文件。 文件名为智能协定名称，后跟**json**扩展名。
1. 查找 JSON 文件中的**字节码**元素。
1. 复制 "**字节码**" 值。

    ![元数据中包含字节码的 Visual Studio Code 窗格](./media/ethereum-logic-app/bytecode-metadata.png)

**使用密度编译器：**

使用命令 `solc --bin <smart contract>.sol` 生成协定字节码。

## <a name="get-the-contract-address"></a>获取协定地址

协定地址是以太坊区块链上的智能协定目标地址。 使用此地址可发送智能协定的事务或查询状态。 可以从 Truffle 迁移输出或协定元数据文件获取协定地址。

**使用 Truffle 迁移输出：**

Truffle 在部署智能协定后显示协定地址。 从输出复制**合同地址**。

![Truffle Visual Studio Code 中具有协定地址的迁移输出](./media/ethereum-logic-app/contract-address-truffle.png)

**使用协定元数据文件：**

1. 打开包含在你的密度项目的**生成/协定**文件夹中的协定元数据文件。 文件名为智能协定名称，后跟**json**扩展名。
1. 在 JSON 文件中查找 "**网络**" 部分。
1. 专用网络由整数网络 ID 标识。 查找 "网络" 部分中的 "地址" 值。
1. 复制**地址**值。

![地址值在 Visual Studio Code 中的元数据](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>后续步骤

观看视频中的常见方案[，对逻辑应用执行更多操作](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)。
