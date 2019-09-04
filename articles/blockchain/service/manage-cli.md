---
title: 使用 Azure CLI 管理 Azure 区块链服务
description: 如何创建和管理 Azure CLI 的 Azure 区块链服务
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: ee7e529593960c3a7c62021225122370c122b3c4
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240969"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>使用 Azure CLI 管理 Azure 区块链服务

除了 Azure 门户之外，还可以使用 Azure CLI 管理 Azure 区块链服务的区块链成员和事务节点。

请确保已安装最新的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ，并使用`az login`登录到 Azure 帐户。

在下面的示例中，请`<parameter names>`将示例替换为自己的值。

## <a name="create-blockchain-member"></a>创建区块链成员

示例在 Azure 区块链服务中创建一个在新联盟中运行仲裁分类帐协议的区块链成员。

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 |
| **name** | 用于标识 Azure 区块链服务区块链成员的唯一名称。 此名称用于公共终结点地址。 例如， `myblockchainmember.blockchain.azure.com` 。 |
| **location** | 在其中创建区块链成员的 Azure 区域。 例如， `eastus` 。 选择最靠近用户或其他 Azure 应用程序的位置。 |
| **password** | 成员帐户密码。 成员帐户密码用来使用基本身份验证向区块链成员的公共终结点进行身份验证。 密码必须满足以下四个条件中的三个：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号（#）、百分比（%）、逗号（，）、星号（*）、右引号（\`）、双引号（"）、单引号（'）、破折号（-）和 semicolumn （;)|
| **protocol** | 公共预览版支持仲裁。 |
| **consortium** | 要加入或创建的联盟的名称。 |
| **consortiumManagementAccountPassword** | 联盟管理密码。 密码用于联接联盟。 |
| **ruleName** | IP 地址范围允许列表的规则名称。 防火墙规则的可选参数。|
| **startIpAddress** | 允许列表的 IP 地址范围的开头。 防火墙规则的可选参数。 |
| **endIpAddress** | 允许列表的 IP 地址范围的结尾。 防火墙规则的可选参数。 |
| **skuName** | 层类型。 对于标准层，请使用 S0，对于基本层，请使用 B0。 |

## <a name="change-blockchain-member-password"></a>更改区块链成员密码

示例更改区块链成员的密码。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| 参数 | 描述 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 |
| **name** | 标识 Azure 区块链服务成员的名称。 |
| **password** | 成员帐户密码。 密码必须满足以下四个条件中的三个：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号（#）、百分比（%）、逗号（，）、星号（*）、右引号（\`）、双引号（"）、单引号（'）、破折号（-）和分号（;)。 |

## <a name="create-transaction-node"></a>创建事务节点

在现有的区块链成员中创建事务节点。 通过添加事务节点，可以增加安全性隔离和分发负载。 例如，您可以为不同的客户端应用程序创建一个事务节点终结点。

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 |
| **name** | 还包括新的事务节点名称的 Azure 区块链 Service 区块链成员的名称。 |
| **location** | 在其中创建区块链成员的 Azure 区域。 例如， `eastus` 。 选择最靠近用户或其他 Azure 应用程序的位置。 |
| **password** | 事务节点密码。 密码必须满足以下四个条件中的三个：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号（#）、百分比（%）、逗号（，）、星号（*）、右引号（\`）、双引号（"）、单引号（'）、破折号（-）和分号（;)。 |
| **ruleName** | IP 地址范围允许列表的规则名称。 防火墙规则的可选参数。 |
| **startIpAddress** | 允许列表的 IP 地址范围的开头。 防火墙规则的可选参数。 |
| **endIpAddress** | 允许列表的 IP 地址范围的结尾。 防火墙规则的可选参数。|

## <a name="change-transaction-node-password"></a>更改事务节点密码

示例更改事务节点密码。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **name** | 还包括新的事务节点名称的 Azure 区块链 Service 区块链成员的名称。 |
| **password** | 事务节点密码。 密码必须满足以下四个条件中的三个：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号（#）、百分比（%）、逗号（，）、星号（*）、右引号（\`）、双引号（"）、单引号（'）、破折号（-）和分号（;)。 |

## <a name="change-consortium-management-account-password"></a>更改联合会管理帐户密码

"联盟" 管理帐户用于 "联盟成员身份管理"。 每个成员都由一个联合会管理帐户唯一标识，你可以使用以下命令更改此帐户的密码。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | 在其中创建 Azure 区块链服务资源的资源组名称。 |
| **name** | 标识 Azure 区块链服务成员的名称。 |
| **consortiumManagementAccountPassword** | 联合会管理帐户密码。 密码必须满足以下四个条件中的三个：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号（#）、百分比（%）、逗号（，）、星号（*）、右引号（\`）、双引号（"）、单引号（'）、破折号（-）和分号（;)。 |
  
## <a name="update-firewall-rules"></a>更新防火墙规则

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **name** | Azure 区块链 Service 区块链成员的名称。 |
| **ruleName** | IP 地址范围允许列表的规则名称。 防火墙规则的可选参数。|
| **startIpAddress** | 允许列表的 IP 地址范围的开头。 防火墙规则的可选参数。|
| **endIpAddress** | 允许列表的 IP 地址范围的结尾。 防火墙规则的可选参数。|

## <a name="list-api-keys"></a>列出 API 密钥

API 密钥可用于节点访问，类似于用户名和密码。 提供两个 API 密钥来支持密钥轮换。 使用以下命令列出 API 密钥。

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **name** | 还包括新的事务节点名称的 Azure 区块链 Service 区块链成员的名称。 |

## <a name="regenerate-api-keys"></a>重新生成 API 密钥

使用以下命令重新生成 API 密钥。

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| 参数 | 描述 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **name** | 还包括新的事务节点名称的 Azure 区块链 Service 区块链成员的名称。 |
| **keyName** | 将\<keyValue\>替换为 key1 或 key2。 |

## <a name="delete-a-transaction-node"></a>删除事务节点

示例删除区块链成员事务节点。

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **name** | 还包括要删除的新事务节点名称的 Azure 区块链 Service 区块链成员的名称。 |

## <a name="delete-a-blockchain-member"></a>删除区块链成员

示例删除区块链成员。

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| 参数 | 描述 |
|---------|-------------|
| **resource-group** | Azure 区块链服务资源所在的资源组名称。 |
| **name** | 要删除的 Azure 区块链 Service 区块链成员的名称。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>为 Azure AD 用户授予访问权限

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 参数 | 描述 |
|---------|-------------|
| **role** | Azure AD 角色的名称。 |
| **代理人** | Azure AD 用户 ID。 例如： `user@contoso.com` |
| **scope** | 角色分配的范围。 可以是 transaction 节点的区块链成员。 |

**示例：**

为 Azure AD 用户授予对区块链**成员**的节点访问权限：

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**示例：**

授予 Azure AD user to 区块链**transaction 节点**的节点访问权限：

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>授予对 Azure AD 组或应用程序角色的节点访问权限

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| 参数 | 描述 |
|---------|-------------|
| **role** | Azure AD 角色的名称。 |
| **assignee-object-id** | Azure AD 组 ID 或应用程序 ID。 |
| **scope** | 角色分配的范围。 可以是 transaction 节点的区块链成员。 |

**示例：**

授予**应用程序角色**的节点访问权限

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>删除 Azure AD 节点访问权限

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 参数 | 描述 |
|---------|-------------|
| **role** | Azure AD 角色的名称。 |
| **代理人** | Azure AD 用户 ID。 例如： `user@contoso.com` |
| **scope** | 角色分配的范围。 可以是 transaction 节点的区块链成员。 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置 Azure 区块链 Service transaction 节点 Azure 门户](configure-transaction-nodes.md)
