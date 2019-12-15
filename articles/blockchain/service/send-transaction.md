---
title: 创建、生成和部署智能合同教程 - Azure 区块链服务
description: 此教程介绍如何在 Visual Studio Code 中使用适用于 Ethereum 的 Azure 区块链开发工具包扩展，在 Azure 区块链服务上创建、生成和部署智能合同。
ms.date: 12/06/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 5b901ab904425a22d2fe9643ffa75a4e978efa88
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972764"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>教程：在 Azure 区块链服务中创建、生成和部署智能合同

此教程介绍如何在 Visual Studio Code 中使用适用于 Ethereum 的 Azure 区块链开发工具包扩展，在 Azure 区块链服务上创建、生成和部署智能合同。 还可以使用开发工具包通过事务执行智能合同函数。

你将使用适用于 Ethereum 的 Azure 区块链开发工具包执行以下操作：

> [!div class="checklist"]
> * 创建智能合同
> * 部署智能合同
> * 通过事务执行智能合同函数

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完整[快速入门：使用 Visual Studio Code 连接到 Azure 区块链服务联盟网络](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [适用于 Ethereum 的 Azure 区块链开发工具包扩展](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x 或更高版本](https://nodejs.org/download)
* [Git 2.10.x 或更高版本](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) 将 python.exe 添加到路径中。 Azure 区块链开发工具包需要路径中的 Python 版本 2.7.15。
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

在 Windows 上，node-gyp 模块需要使用已安装的 C++ 编译器。 可以使用 MSBuild 工具：

* 如果安装了 Visual Studio 2017，则将 npm 配置为通过命令 `npm config set msvs_version 2017 -g` 使用 MSBuild 工具
* 如果安装了 Visual Studio 2019，则为 npm 设置 MSBuild 工具路径。 例如： `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* 如果都不是，则在提升的*以管理员身份运行*命令外壳中，使用 `npm install --global windows-build-tools` 安装独立的 VS Build 工具。

有关 node-gyp 的详细信息，请参阅 [GitHub 上的 node-gyp 存储库](https://github.com/node-gyp)。

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

    ![选择“生成合同”菜单 ](./media/send-transaction/build-contracts.png)

Azure 区块链开发工具包使用 Truffle 来编译智能合同。

![Truffle 编译器输出](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>部署智能合同

Truffle 使用迁移脚本将合同部署到 Ethereum 网络。 迁移是项目的 **migrations** 目录中的 JavaScript 文件。

1. 若要部署智能合同，请右键单击“HelloBlockchain.sol”并从菜单中选择“部署合同”。  
1. 在命令面板中选择 Azure 区块链联盟网络。 在创建项目时，该联盟区块链网络已添加到项目的 Truffle 配置文件中。
1. 选择“生成助记键”。  选择文件名，并将助记键文件保存到项目文件夹中。 例如，`myblockchainmember.env` 。 助记键文件用于为区块链成员生成 Ethereum 私钥。

Azure 区块链开发工具包使用 Truffle 执行迁移脚本，以将合同部署到区块链。

![已成功部署合同](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>调用合同函数

**HelloBlockchain** 合同的 **SendRequest** 函数将更改 **RequestMessage** 状态变量。 可通过事务更改区块链网络的状态。 可以使用 Azure 区块链开发工具包智能合同交互页通过事务调用 **SendRequest** 函数。

1. 若要与智能合同交互，请右键单击“HelloBlockchain.sol”，并从菜单中选择“显示智能合同交互页”。  

    ![从菜单中选择“显示智能合同交互页”](./media/send-transaction/contract-interaction.png)

1. 在交互页中可以选择已部署的合同版本、调用函数、查看当前状态和查看元数据。

    ![示例智能合同交互页](./media/send-transaction/interaction-page.png)

1. 若要调用智能合同函数，请选择合同操作并传递参数。 选择 **SendRequest** 合同操作，并为 **requestMessage** 参数 并输入 **Hello, Blockchain!** 。 选择“执行”以通过事务调用 **SendRequest** 函数。 

    ![执行 SendRequest 操作](./media/send-transaction/sendrequest-action.png)

处理该事务后，interaction 节会反映状态更改。

![合同状态更改](./media/send-transaction/contract-state.png)

SendRequest 函数设置 **RequestMessage** 和 **State** 字段。 **RequestMessage** 的当前状态是传递的参数 **Hello, Blockchain**。 **State** 字段值保留为 **Request**。

## <a name="clean-up-resources"></a>清理资源

如果不再需要，可以通过删除*创建区块链成员*先决条件快速入门中创建的 `myResourceGroup` 资源组来删除资源。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户中，导航至左侧导航窗格中的“资源组”  ，然后选择要删除的资源组。
1. 选择“删除资源组”  。 输入资源组名称确认删除并选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Azure 区块链开发工具包创建了一个示例 Solidity 项目。 你生成并部署了一个智能合同，然后通过托管在 Azure 区块链服务中的区块链联盟网络上的事务调用了一个函数。

> [!div class="nextstepaction"]
> [使用 Azure 区块链服务开发区块链应用程序](develop.md)
