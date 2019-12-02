---
title: 使用 Truffle 连接到 Azure 区块链服务
description: 使用 Truffle 连接到 Azure 区块链服务网络
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9ea96aa32072775fe6fb9563442f5e3564d37ea5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455793"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>快速入门：使用 Truffle 连接到 Azure 区块链服务

在本快速入门中，你将使用 Truffle 连接到 Azure 区块链服务事务节点。 然后使用 Truffle 交互式控制台调用 web3 方法，实现与区块链网络的交互  。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完整[快速入门：使用 Azure 门户创建区块链成员](create-member.md)或[快速入门：通过 Azure CLI 创建 Azure 区块链服务区块链成员](create-member-cli.md)
* 安装 [Truffle](https://github.com/trufflesuite/truffle)。 Truffle 要求安装多个工具，包括 [Node.js](https://nodejs.org)、[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
* 安装 [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)。 Web3 需要 Python。

## <a name="create-truffle-project"></a>创建 Truffle 项目

1. 打开 Node.js 命令提示符或 shell。
1. 将目录更改为你要在其中创建 Truffle 项目目录的位置。
1. 为该项目创建一个目录并将路径更改为新目录。 例如，

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. 初始化 Truffle 项目。

    ``` bash
    truffle init
    ```

1. 在项目文件夹中安装 Ethereum JavaScript API web3。 当前需要 web3 版本 1.0.0-beta.37。

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    在安装过程中可能会收到 npm 警告。
    
## <a name="configure-truffle-project"></a>配置 Truffle 项目

若要配置 Truffle 项目，需要从 Azure 门户获取一些事务节点信息。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 转到你的 Azure 区块链服务成员。 选择“事务节点”  和默认事务节点链接。

    ![选择默认事务节点](./media/connect-truffle/transaction-nodes.png)

1. 选择“连接字符串”  。
1. 从“HTTPS(访问密钥 1)”  中复制连接字符串。 下一部分中需要使用此字符串。

    ![连接字符串](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>编辑配置文件

接下来，需要使用事务节点终结点更新 Truffle 配置文件。

1. 在 **truffledemo** 项目文件夹中，通过编辑器打开 Truffle 配置文件 `truffle-config.js`。
1. 将该文件的内容替换为以下配置信息。 添加包含终结点地址的变量。 将尖括号替换为在前面部分中收集的值。

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. 保存对 `truffle-config.js` 的更改。

## <a name="connect-to-transaction-node"></a>连接到事务节点

使用 Web3 连接到事务节点  。

1. 使用 Truffle 控制台连接到默认事务节点。 在命令提示符下或 shell 中运行以下命令：

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle 连接到默认事务节点并提供交互式控制台。

    可以调用该 web3 对象中的方法来与区块链网络进行交互  。

1. 调用 **getBlockNumber** 方法来返回当前的块编号。

    ```bash
    web3.eth.getBlockNumber();
    ```

    示例输出：

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. 退出 Truffle 控制台。

    ```bash
    .exit
    ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用了 Truffle 连接到 Azure 区块链服务默认事务节点，并使用了交互式控制台返回当前区块链块编号。

尝试下一个教程，使用适用于 Ethereum 的 Azure 区块链开发工具包通过事务创建、生成、部署和执行智能合同函数。

> [!div class="nextstepaction"]
> [在 Azure 区块链服务中创建、生成和部署智能合同](send-transaction.md)
