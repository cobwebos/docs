---
title: 使用 Visual Studio Code 通过 Azure 区块链服务创建、生成和部署智能合同
description: 此教程介绍如何在 Visual Studio Code 中使用适用于 Ethereum 的 Azure 区块链开发工具包扩展，在 Azure 区块链服务上创建、生成和部署智能合同。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 09/10/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 96fe4d77efdd1fda309d7da021bcc208edd2dfe9
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935015"
---
# <a name="tutorial-usevisual-studio-code-to-create-buildanddeploysmartcontracts"></a>教程：使用 Visual Studio Code 创建、生成和部署智能合约

此教程介绍如何在 Visual Studio Code 中使用适用于 Ethereum 的 Azure 区块链开发工具包扩展，在 Azure 区块链服务上创建、生成和部署智能合同。 还可以使用 Truffle 通过事务执行智能合同函数。

你将使用适用于 Ethereum 的 Azure 区块链开发工具包执行以下操作：

> [!div class="checklist"]
> * 创建智能合同
> * 部署智能合同
> * 通过事务执行智能合同函数
> * 查询合同状态

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完整[快速入门：使用 Visual Studio Code 连接到 Azure 区块链服务联盟网络](connect-vscode.md)

## <a name="create-a-smart-contract"></a>创建智能合同

适用于 Ethereum 的 Azure 区块链开发工具包使用项目模板和 Truffle 工具来帮助搭建、生成和部署合同。 在开始之前，请完成先决条件[快速入门：使用 Visual Studio Code 连接到 Azure 区块链服务联盟网络](connect-vscode.md)。 此快速入门介绍了如何安装和配置适用于 Ethereum 的 Azure 区块链开发工具包。

1. 在 VS Code 命令面板中选择“Azure 区块链:  新建 Solidity 项目”。
1. 选择“创建基本项目”。 
1. 创建名为 `HelloBlockchain` 的新文件夹，然后选择“创建新的项目路径”。 

Azure 区块链开发工具包将为你创建并初始化一个新的 Solidity 项目。 基本项目包含一个示例 **HelloBlockchain** 智能合同，以及要生成并部署到 Azure 区块链服务中联盟成员的所有必要文件。 创建该项目可能需要几分钟时间。 可以选择 Azure 区块链的输出来监视 VS Code 终端面板中的进度。

项目结构类似于以下示例：

   ![Solidity 项目](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>生成智能合同

智能合同位于项目的 **contracts** 目录中。 需要先编译智能合同，然后才能将其部署到区块链。 使用“生成合同”命令编译项目中的所有智能合同。 

1. 在 VS Code 资源管理器侧栏中，展开项目中的“contracts”文件夹。 
1. 右键单击“HelloBlockchain.sol”并从菜单中选择“生成合同”。  

    ![生成合同](./media/send-transaction/build-contracts.png)

Azure 区块链开发工具包使用 Truffle 来编译智能合同。

![编译输出](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>部署智能合同

Truffle 使用迁移脚本将合同部署到 Ethereum 网络。 迁移是项目的 **migrations** 目录中的 JavaScript 文件。

1. 若要部署智能合同，请右键单击“HelloBlockchain.sol”并从菜单中选择“部署合同”。  
1. 在命令面板中选择 Azure 区块链联盟网络。 在创建项目时，该联盟区块链网络已添加到项目的 Truffle 配置文件中。
1. 选择“生成助记键”。  选择文件名，并将助记键文件保存到项目文件夹中。 例如，`myblockchainmember.env` 。 助记键文件用于为区块链成员生成 Ethereum 私钥。

Azure 区块链开发工具包使用 Truffle 执行迁移脚本，以将合同部署到区块链。

![已成功部署合同](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>调用合同函数

**HelloBlockchain** 合同的 **SendRequest** 函数将更改 **RequestMessage** 状态变量。 可通过事务更改区块链网络的状态。 可以通过事务创建一个用于执行 **SendRequest** 函数的脚本。

1. 在 Truffle 项目的根目录中创建一个新文件，并将其命名为 `sendrequest.js`。 将以下 Web3 JavaScript 代码添加到该文件。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. 当 Azure 区块链开发工具包创建项目时，将使用联盟区块链网络终结点详细信息生成 Truffle 配置文件。 打开项目中的 **truffle-config.js**。 该配置文件将列出两个网络：一个名为 development 的网络，一个与联盟同名的网络。
1. 在 VS Code 的终端窗格中，使用 Truffle 针对联盟区块链网络执行该脚本。 在终端窗格菜单栏中选择“终端”选项卡，并从下拉列表中选择“PowerShell”。  

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    将 \<blockchain network\> 替换为 **truffle-config.js** 中定义的区块链网络名称。

Truffle 将针对区块链网络执行脚本。

![脚本输出](./media/send-transaction/execute-transaction.png)

通过某个事务执行合同的函数时，该事务只会在创建区块之后才得到处理。 要通过事务执行的函数将返回事务 ID 而不是返回值。

## <a name="query-contract-state"></a>查询合同状态

智能合同函数可以返回状态变量的当前值。 让我们添加一个返回状态变量值的函数。

1. 在 **HelloBlockchain.sol** 中，将 **getMessage** 函数添加到 **HelloBlockchain** 智能合同。

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    该函数基于合同的当前状态返回存储在状态变量中的消息。

1. 右键单击“HelloBlockchain.sol”，然后从菜单中选择“生成合同”以编译对智能合同所做的更改。  
1. 若要部署，请右键单击“HelloBlockchain.sol”并从菜单中选择“部署合同”。   出现提示时，在命令面板中选择 Azure 区块链联盟网络。
1. 接下来，创建一个用于调用 **getMessage** 函数的脚本。 在 Truffle 项目的根目录中创建一个新文件，并将其命名为 `getmessage.js`。 将以下 Web3 JavaScript 代码添加到该文件。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. 在 VS Code 的终端窗格中，使用 Truffle 针对区块链网络执行该脚本。 在终端窗格菜单栏中选择“终端”选项卡，并从下拉列表中选择“PowerShell”。  

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    将 \<blockchain network\> 替换为 **truffle-config.js** 中定义的区块链网络名称。

该脚本通过调用 getMessage 函数来查询智能合同。 将返回 **RequestMessage** 状态变量的当前值。

![脚本输出](./media/send-transaction/execute-get.png)

请注意，该值不是 **Hello, blockchain!** 。 返回的值是一个占位符。 更改和部署合同时，合同将获取新的合同地址，并在智能合同构造函数中为状态变量赋值。 Truffle 示例 **2_deploy_contracts.js** 迁移脚本将部署智能合同，并将占位符值作为参数传递。 构造函数将 **RequestMessage** 状态变量设置为占位符值，这正是返回的值。

1. 若要设置 **RequestMessage** 状态变量并查询值，请再次运行 **sendrequest.js** 和 **getmessage.js** 脚本。

    ![脚本输出](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** 将 **RequestMessage** 变量设置为 **Hello, blockchain!** 。 **getmessage.js** 在合同中查询 **RequestMessage** 状态变量的值，并返回 **Hello, blockchain!** 。

## <a name="clean-up-resources"></a>清理资源

如果不再需要，可以通过删除*创建区块链成员*先决条件快速入门中创建的 `myResourceGroup` 资源组来删除资源。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户中，导航至左侧导航窗格中的“资源组”  ，然后选择要删除的资源组。
1. 选择“删除资源组”  。 输入资源组名称确认删除并选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Azure 区块链开发工具包创建了一个示例 Solidity 项目。 你生成并部署了一个智能合同，然后通过托管在 Azure 区块链服务中的区块链联盟网络上的事务调用了一个函数。

> [!div class="nextstepaction"]
> [使用 Azure 区块链服务开发区块链应用程序](develop.md)
