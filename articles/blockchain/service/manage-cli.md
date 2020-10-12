---
title: 使用 Azure CLI 管理 Azure 区块链服务
description: 如何 Azure CLI 管理 Azure 区块链服务
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87170864"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>使用 Azure CLI 管理 Azure 区块链服务

除了 Azure 门户之外，还可以使用 Azure CLI 管理 Azure 区块链服务的区块链成员和事务节点。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

如果你想要在本地安装并使用 CLI，请参阅 [安装 Azure CLI](/cli/azure/install-azure-cli)。

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

## <a name="create-blockchain-member"></a>创建区块链成员

示例在 Azure 区块链服务中创建一个在新联盟中运行仲裁分类帐协议的 [区块链成员](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) 。

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 |
| name | 用于标识 Azure 区块链服务区块链成员的唯一名称。 此名称用于公共终结点地址。 例如，`myblockchainmember.blockchain.azure.com`。 |
| **location** | 在其中创建区块链成员的 Azure 区域。 例如，`eastus` 。 选择最靠近用户或其他 Azure 应用程序的位置。 功能在某些地区可能不可用。 |
| password | 成员的默认事务节点的密码。 连接到区块链成员的默认事务节点公共终结点时，请使用密码进行基本身份验证。 密码必须满足以下四个条件中的三个要求：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号 ( # ) ，% (% ) ，逗号 (，) ，星号 ( * ) ，后跟引号 () ，双引号 ( ) ， ( \`|
| **protocol** | 区块链协议。 目前支持 Quorum 协议。 |
| **consortium** | 要加入或创建的联盟的名称。 有关联盟的详细信息，请参阅 [Azure 区块链服务联盟](consortium.md)。 |
| **consortium-management-account-password** | 联盟帐户密码也称为成员帐户密码。 成员帐户密码用于加密为你的成员创建的 Ethereum 帐户的私钥。 你使用成员帐户和成员帐户密码进行联盟管理。 |
| **sku** | 层类型。 标准或基本 。 使用“基本”层进行开发、测试和概念证明。 使用“标准”层进行生产级部署。 如果使用区块链数据管理器或发送大量专用事务，还应使用“标准”层。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。 |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>更改区块链成员密码或防火墙规则

示例 [更新区块链成员](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)的密码、联合会管理密码和防火墙规则。

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 |
| name | 标识 Azure 区块链服务成员的名称。 |
| password | 成员的默认事务节点的密码。 连接到区块链成员的默认事务节点公共终结点时，请使用密码进行基本身份验证。 密码必须满足以下四个条件中的三个要求：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号 ( # ) ，% (% ) ，逗号 (，) ，星号 ( * ) ，后跟引号 () ，双引号 ( ) ， ( \`|
| **consortium-management-account-password** | 联盟帐户密码也称为成员帐户密码。 成员帐户密码用于加密为你的成员创建的 Ethereum 帐户的私钥。 你使用成员帐户和成员帐户密码进行联盟管理。 |
| **防火墙-规则** | IP 允许列表的起始和结束 IP 地址。 |

## <a name="create-transaction-node"></a>创建事务节点

在现有的区块链成员中[创建事务节点](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create)。 通过添加事务节点，可以增加安全性隔离和分发负载。 例如，您可以为不同的客户端应用程序创建一个事务节点终结点。

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 |
| **location** | 区块链成员的 Azure 区域。 |
| **成员名称** | 标识 Azure 区块链服务成员的名称。 |
| password | 事务节点的密码。 连接到事务节点公共终结点时，请使用密码进行基本身份验证。 密码必须满足以下四个条件中的三个要求：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号 ( # ) ，% (% ) ，逗号 (，) ，星号 ( * ) ，后跟引号 () ，双引号 ( ) ， ( \`|
| name | 事务节点名称。 |

## <a name="change-transaction-node-password"></a>更改事务节点密码

示例 [更新事务节点](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) 密码。

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **成员名称** | 标识 Azure 区块链服务成员的名称。 |
| password | 事务节点的密码。 连接到事务节点公共终结点时，请使用密码进行基本身份验证。 密码必须满足以下四个条件中的三个要求：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号 ( # ) ，% (% ) ，逗号 (，) ，星号 ( * ) ，后跟引号 () ，双引号 ( ) ， ( \`|
| name | 事务节点名称。 |

## <a name="list-api-keys"></a>列出 API 密钥

API 密钥可用于节点访问，类似于用户名和密码。 提供两个 API 密钥来支持密钥轮换。 使用以下命令 [列出 API 密钥](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key)。

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| name | Azure 区块链 Service 区块链成员的名称 |

## <a name="regenerate-api-keys"></a>重新生成 API 密钥

使用以下命令 [重新生成 API 密钥](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)。

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| name | Azure 区块链 Service 区块链成员的名称。 |
| **keyName** | 替换 \<keyValue\> 为 key1、key2 或 both。 |

## <a name="delete-a-transaction-node"></a>删除事务节点

示例 [删除区块链成员事务节点](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete)。

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **成员名称** | 还包括要删除的事务节点名称的 Azure 区块链 Service 区块链成员的名称。 |
| name | 要删除的事务节点名称。 |

## <a name="delete-a-blockchain-member"></a>删除区块链成员

示例 [删除区块链成员](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete)。

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| 参数 | 说明 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| name | 要删除的 Azure 区块链 Service 区块链成员的名称。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>为 Azure AD 用户授予访问权限

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 参数 | 说明 |
|---------|-------------|
| **role** | Azure AD 角色的名称。 |
| **assignee** | Azure AD 用户 ID。 例如 `user@contoso.com` |
| **作用域** | 角色分配的范围。 可以是区块链成员或事务节点。 |

**示例：**

为 Azure AD 用户授予对区块链 **成员**的节点访问权限：

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**示例：**

授予 Azure AD user to 区块链 **transaction 节点**的节点访问权限：

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>授予对 Azure AD 组或应用程序角色的节点访问权限

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 参数 | 说明 |
|---------|-------------|
| **role** | Azure AD 角色的名称。 |
| **工作负责人-对象 id** | Azure AD 组 ID 或应用程序 ID。 |
| **作用域** | 角色分配的范围。 可以是区块链成员或事务节点。 |

**示例：**

授予**应用程序角色**的节点访问权限

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>删除 Azure AD 节点访问权限

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 参数 | 说明 |
|---------|-------------|
| **role** | Azure AD 角色的名称。 |
| **assignee** | Azure AD 用户 ID。 例如 `user@contoso.com` |
| **作用域** | 角色分配的范围。 可以是区块链成员或事务节点。 |

## <a name="next-steps"></a>后续步骤

了解如何 [配置 Azure 门户的 Azure 区块链 Service transaction 节点](configure-transaction-nodes.md)。
