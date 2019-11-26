---
title: 使用 Azure CLI 配置区块链数据管理器-Azure 区块链服务
description: 使用 Azure CLI 创建和管理 Azure 区块链服务的区块链数据管理器
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455938"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>使用 Azure CLI 配置区块链数据管理器

配置 Azure 区块链服务的区块链数据管理器，以捕获区块链数据将其发送到 Azure 事件网格主题。

若要配置区块链数据管理器实例，你需要：

* 创建区块链 Manager 实例
* 创建 Azure 区块链 Service transaction 节点的输入
* 创建 Azure 事件网格主题的输出
* 添加区块链应用程序
* 启动实例

## <a name="prerequisites"></a>先决条件

* 使用 `az login`安装最新的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)并登录。
* 完成[快速入门：使用 Visual Studio Code 连接到 Azure 区块链 Service 联合会网络](connect-vscode.md)
* 创建[事件网格主题](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件网格中的事件处理程序](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0.51 版或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>创建实例

区块链数据管理器实例监视 Azure 区块链服务事务节点。 实例从事务节点捕获所有原始块和原始事务数据。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| 参数 | 说明 |
|-----------|-------------|
| resource-group | 要在其中创建区块链数据管理器实例的资源组名称。 |
| name | 区块链数据管理器实例的名称。 |
| 资源类型 | 区块链数据管理器实例的资源类型是**区块链/观察**程序。 |
| is-full-object | 指示属性包含观察程序资源的选项。 |
| properties | JSON 格式的字符串，其中包含观察程序资源的属性。 可以作为字符串或文件传递。  |

### <a name="create-instance-examples"></a>创建实例示例

用于在**美国东部**区域中创建区块链 Manager 实例的 JSON 配置示例。

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| 元素 | 说明 |
|---------|-------------|
| location | 要在其中创建观察程序资源的区域 |
| properties | 创建观察程序资源时要设置的属性 |

使用用于配置的 JSON 字符串创建名为*mywatcher*的区块链数据管理器实例。

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

使用 JSON 配置文件创建名为*mywatcher*数据管理器实例的区块链。

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>创建输入

输入将区块链数据管理器连接到 Azure 区块链 Service transaction 节点。 只有有权访问事务节点的用户才能创建连接。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| 参数 | 说明 |
|-----------|-------------|
| resource-group | 要在其中创建输入资源的资源组名称。 |
| name | 输入的名称。 |
| 命名空间 | 使用**区块链**提供程序命名空间。 |
| 资源类型 | 区块链数据管理器输入的资源类型为**输入**。 |
| 父级 (parent) | 与输入关联的观察程序的路径。 例如，**观察程序/mywatcher**。 |
| is-full-object | 指示属性包含用于输入资源的选项。 |
| properties | JSON 格式的字符串，它包含输入资源的属性。 可以作为字符串或文件传递。 |

### <a name="input-examples"></a>输入示例

配置 JSON 示例，用于在 "*美国东部*" 区域中创建连接到 \<区块链成员\>的输入资源。

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| 元素 | 说明 |
|---------|-------------|
| location | 要在其中创建输入资源的区域。 |
| #a2 | Azure 区块链服务成员的分类帐类型。 目前支持**以太坊**。 |
| resourceId | 输入连接到的事务节点。 将 \<订阅 ID 替换\>、\<资源组\>，并将 \<区块链成员\> 替换为事务节点资源的值。 输入连接到 Azure 区块链服务成员的默认事务节点。 |

使用用于配置的 JSON 字符串为*mywatcher*创建名为*myInput*的输入。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

使用 JSON 配置文件为*mywatcher*创建名为*myInput*的输入。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>创建输出

出站连接将区块链数据发送到 Azure 事件网格。 可将区块链数据发送到一个或多个目标。 区块链数据管理器支持对任意给定的区块链数据管理器实例使用多个事件网格主题出站连接。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| 参数 | 说明 |
|-----------|-------------|
| resource-group | 要在其中创建输出资源的资源组名称。 |
| name | 输出的名称。 |
| 命名空间 | 使用**区块链**提供程序命名空间。 |
| 资源类型 | 区块链数据管理器输出的资源类型为**输出**。 |
| 父级 (parent) | 与输出关联的观察程序的路径。 例如，**观察程序/mywatcher**。 |
| is-full-object | 指示属性包含用于输出资源的选项。 |
| properties | JSON 格式的字符串，其中包含输出资源的属性。 可以作为字符串或文件传递。 |

### <a name="output-examples"></a>输出示例

配置 JSON 示例，用于在*美国东部*区域中创建连接到名为 \<事件网格主题\>的事件网格主题的输出资源。

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| 元素 | 说明 |
|---------|-------------|
| location | 要在其中创建输出资源的区域。 |
| outputType | 输出类型。 目前支持**EventGrid** 。 |
| resourceId | 输出连接到的资源。 将 \<订阅 ID 替换\>、\<资源组\>，并将 \<区块链成员\> 替换为事件网格资源的值。 |

创建一个名为*myoutput*的输出 *，该输出*使用 JSON 配置字符串连接到事件网格主题。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

使用 JSON 配置文件为*mywatcher*创建连接到事件网格主题的名为*myoutput*的输出。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>添加区块链应用程序

如果添加区块链应用程序，则区块链数据管理器对应用程序的事件和属性状态进行解码。 否则，只会发送原始块和原始事务数据。 区块链数据管理器还会在部署协定时发现协定地址。 可以将多个区块链应用程序添加到区块链数据管理器实例。


> [!IMPORTANT]
> 目前，不完全支持声明密度[数组类型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays)或[映射类型](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)的区块链应用程序。 声明为数组或映射类型的属性将不会在*ContractPropertiesMsg*或*DecodedContractEventsMsg*消息中进行解码。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| 参数 | 说明 |
|-----------|-------------|
| resource-group | 要在其中创建应用程序资源的资源组名称。 |
| name | 应用程序的名称。 |
| 命名空间 | 使用**区块链**提供程序命名空间。 |
| 资源类型 | 区块链数据管理器应用程序的资源类型是**项目**。 |
| 父级 (parent) | 与应用程序关联的观察程序的路径。 例如，**观察程序/mywatcher**。 |
| is-full-object | 指示属性包含应用程序资源的选项。 |
| properties | JSON 格式的字符串，其中包含应用程序资源的属性。 可以作为字符串或文件传递。 |

### <a name="blockchain-application-examples"></a>区块链应用程序示例

用于在 "*美国东部*" 区域中创建应用程序资源的配置 JSON 示例，该资源监视由协定 ABI 和字节码定义的智能协定。

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| 元素 | 说明 |
|---------|-------------|
| location | 要在其中创建应用程序资源的区域。 |
| artifactType | 应用程序类型。 目前支持**EthereumSmartContract** 。 |
| abiFileUrl | 智能协定 ABI JSON 文件的 URL。 若要详细了解如何获取协定 ABI 和创建 URL，请参阅[获取协定 abi 和字节码](data-manager-portal.md#get-contract-abi-and-bytecode)和[创建协定 abi 和字节码 URL](data-manager-portal.md#create-contract-abi-and-bytecode-url)。 |
| bytecodeFileUrl | 已部署的智能协定字节码 JSON 文件的 URL。 若要详细了解如何获取智能协定部署的字节码和创建 URL，请参阅[获取协定 abi 和字节码](data-manager-portal.md#get-contract-abi-and-bytecode)，并[创建协定 abi 和字节码 URL](data-manager-portal.md#create-contract-abi-and-bytecode-url)。 注意：区块链数据管理器需要**部署的字节码**。 |
| queryTargetTypes | 已发布消息类型。 指定**ContractProperties**将发布*ContractPropertiesMsg*消息类型。 指定**ContractEvents**将发布*DecodedContractEventsMsg*消息类型。 注意：始终发布*RawBlockAndTransactionMsg*和*RawTransactionContractCreationMsg*消息类型。 |

为*mywatcher*创建一个名为*myApplication*的应用程序，该应用程序监视 JSON 字符串定义的智能协定。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

为*mywatcher*创建一个名为*myApplication*的应用程序，该应用程序监视使用 JSON 配置文件定义的智能协定。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>启动实例

运行时，区块链 Manager 实例从定义的输入中监视区块链事件，并将数据发送到定义的输出。

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 参数 | 说明 |
|-----------|-------------|
| action | 使用 "**开始**" 运行观察程序。 |
| ids | 观察程序资源 ID。 将 \<订阅 ID\>、\<资源组\>和 \<观察程序名称\> 替换为观察程序资源的值。|

### <a name="start-instance-example"></a>启动实例示例

启动名为*mywatcher*的区块链数据管理器实例。

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>停止实例

停止区块链数据管理器实例。

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 参数 | 说明 |
|-----------|-------------|
| action | 使用**stop**停止观察程序。 |
| ids | 观察程序的名称。 将 \<订阅 ID\>、\<资源组\>和 \<观察程序名称\> 替换为观察程序资源的值。 |

### <a name="stop-watcher-example"></a>停止观察程序示例

停止名为*mywatcher*的实例。

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>删除实例

删除区块链数据管理器实例。

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| 参数 | 说明 |
|-----------|-------------|
| resource-group | 要删除的观察程序的资源组名称。 |
| name | 要删除的观察程序的名称。 |
| 资源类型 | 区块链数据管理器观察程序的资源类型是**区块链/观察**程序。 |

### <a name="delete-instance-example"></a>删除实例示例

在*myRG*资源组中删除名为*mywatcher*的实例。

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>后续步骤

尝试下一教程使用区块链数据管理器和 Azure Cosmos DB 创建区块链事务消息资源管理器。

> [!div class="nextstepaction"]
> [使用区块链数据管理器向 Azure Cosmos DB 发送数据](data-manager-cosmosdb.md)
