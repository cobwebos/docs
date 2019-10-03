---
title: 使用 Azure CLI 创建 Azure 区块链服务
description: 通过 Azure CLI 使用 Azure 区块链服务创建区块链成员。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: be5a8151f0de0a33db09194a7159aded6848c78a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416171"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>快速入门：通过 Azure CLI 创建 Azure 区块链服务区块链成员

Azure 区块链服务是一个区块链平台，可以用来在智能合同中执行你的业务逻辑。 本快速入门展示了如何开始使用 Azure CLI 创建区块链成员。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。 

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0.51 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>创建区块链成员

在 Azure 区块链服务中创建一个区块链成员，用以在新联盟中运行仲裁账本协议。 需要传递多个参数和属性。 将示例参数替换为你自己的值。

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 请使用在上一部分中创建的资源组。
| name  | 用于标识 Azure 区块链服务区块链成员的唯一名称。 此名称用于公共终结点地址。 例如，`myblockchainmember.blockchain.azure.com`。
| **位置** | 在其中创建区块链成员的 Azure 区域。 例如，`eastus`。 选择最靠近用户或其他 Azure 应用程序的位置。
| **password** | 成员的默认事务节点的密码。 连接到区块链成员的默认事务节点公共终结点时，请使用密码进行基本身份验证。
| **consortium** | 要加入或创建的联盟的名称。
| **consortiumAccountPassword** | 联盟帐户密码也称为成员帐户密码。 成员帐户密码用于加密为你的成员创建的 Ethereum 帐户的私钥。 你使用成员帐户和成员帐户密码进行联盟管理。
| **skuName** | 层类型。 对于标准层，请使用 S0，对于基本层，请使用 B0。

创建区块链成员和支持资源需要大约 10 分钟。

## <a name="clean-up-resources"></a>清理资源

你可以将所创建的区块链成员用于下一快速入门或教程。 当不再需要这些资源时，可以通过删除使用 Azure 区块链服务创建的 `myResourceGroup` 资源组来删除这些资源。

运行以下命令来删除资源组和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

现在，你已创建了一个区块链成员，请尝试适用于 [Geth](connect-geth.md)、[MetaMask](connect-metamask.md) 或 [Truffle](connect-truffle.md) 的连接快速入门之一。

> [!div class="nextstepaction"]
> [使用 Truffle 连接到 Azure 区块链服务网络](connect-truffle.md)
