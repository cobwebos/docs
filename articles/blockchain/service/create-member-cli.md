---
title: 创建 Azure 区块链服务成员 - Azure CLI
description: 使用 Azure CLI 创建区块链联盟的 Azure 区块链服务成员。
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 2514447eaceb83da0bee81c1475a3137f0d1af07
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170646"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>快速入门：通过 Azure CLI 创建 Azure 区块链服务区块链成员

本快速入门介绍如何使用 Azure CLI 在 Azure 区块链服务中部署新的区块链成员和联盟。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

无。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0.51 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prepare-your-environment"></a>准备环境

1. 登录。

    如果使用的是 CLI 的本地安装，请使用 [az login](/cli/azure/reference-index#az-login) 命令登录。

    ```azurecli
    az login
    ```

    遵循终端中显示的步骤完成身份验证过程。

1. 安装 Azure CLI 扩展。

    使用 Azure CLI 的扩展引用时，必须先安装该扩展。  借助 Azure CLI 扩展，可访问尚未在核心 CLI 中提供的试验性和预发布的命令。  若要详细了解包含更新和卸载的扩展，请参阅[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)。

    通过运行以下命令安装 [Azure 区块链服务扩展](/cli/azure/ext/blockchain/blockchain)：

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. 创建资源组。

    与所有 Azure 资源一样，Azure 区块链服务也必须部署到资源组中。 使用资源组可以组织和管理相关的 Azure 资源。

    在本快速入门中，使用以下 [az group create](/cli/azure/group#az-group-create) 命令在 eastus 位置中创建一个名为“myResourceGroup”的资源组 ：

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>创建区块链成员

Azure 区块链服务成员是专用联盟区块链网络中的区块链节点。 预配成员时，可以创建或加入联盟网络。 至少需要一个联盟网络的成员。 参与者所需的区块链成员数取决于方案。 联盟参与者可能有一个或多个区块链成员，或者与其他参与者共享成员。 有关联盟的详细信息，请参阅 [Azure 区块链服务联盟](consortium.md)。

需要传递多个参数和属性。 将示例参数替换为你自己的值。

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 请使用在上一部分中创建的资源组。
| name | 用于标识 Azure 区块链服务区块链成员的唯一名称。 此名称用于公共终结点地址。 例如，`myblockchainmember.blockchain.azure.com`。
| **location** | 在其中创建区块链成员的 Azure 区域。 例如，`westus2` 。 选择最靠近用户或其他 Azure 应用程序的位置。 功能在某些地区可能不可用。 Azure 区块链数据管理器在以下 Azure 区域提供：“美国东部”和“西欧”。
| password | 成员的默认事务节点的密码。 连接到区块链成员的默认事务节点公共终结点时，请使用密码进行基本身份验证。
| **protocol** | 区块链协议。 目前支持 Quorum 协议。
| **consortium** | 要加入或创建的联盟的名称。 有关联盟的详细信息，请参阅 [Azure 区块链服务联盟](consortium.md)。
| **consortium-management-account-password** | 联盟帐户密码也称为成员帐户密码。 成员帐户密码用于加密为你的成员创建的 Ethereum 帐户的私钥。 你使用成员帐户和成员帐户密码进行联盟管理。
| **sku** | 层类型。 标准或基本 。 使用“基本”层进行开发、测试和概念证明。 使用“标准”层进行生产级部署。 如果使用区块链数据管理器或发送大量专用事务，还应使用“标准”层。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

创建区块链成员和支持资源需要大约 10 分钟。

## <a name="clean-up-resources"></a>清理资源

你可以将所创建的区块链成员用于下一快速入门或教程。 不再需要这些资源时，可以通过删除为该快速入门创建的 `myResourceGroup` 资源组来删除这些资源。

运行以下命令来删除资源组和所有相关资源。

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一个 Azure 区块链服务成员和一个新的联盟。 请尝试学习下一个快速入门，使用适用于 Ethereum 的 Azure 区块链开发工具包将内容附加到 Azure 区块链服务成员。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 连接到 Azure 区块链服务](connect-vscode.md)
