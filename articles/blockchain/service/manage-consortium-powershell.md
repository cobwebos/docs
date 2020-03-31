---
title: 管理 Azure 区块链服务联盟成员 - PowerShell
description: 了解如何使用 Azure PowerShell 管理 Azure 区块链服务联盟成员。
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505986"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>使用 PowerShell 管理 Azure 区块链服务中的联盟成员

您可以使用 PowerShell 管理 Azure 区块链服务的区块链联盟成员。 具有管理员权限的成员可以为区块链联盟中的所有参与者邀请、添加、删除和更改角色。 具有用户权限的成员可以查看区块链联盟的所有参与者并更改其成员显示名称。

## <a name="prerequisites"></a>先决条件

* 使用[Azure 门户](create-member.md)创建区块链成员。
* 有关联营、成员和节点的详细信息，请参阅[Azure 区块链服务联合体](consortium.md)。

## <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

您还可以[通过shell.azure.com/powershell在](https://shell.azure.com/powershell)单独的浏览器选项卡中打开云外壳。 选择 **"复制"** 以复制代码块，将其粘贴到云壳体中，然后选择"**输入**"以运行它。

## <a name="install-the-powershell-module"></a>安装 Powershell 模块

从 PowerShell 库中安装Microsoft.AzureBlockchainService.ConsortiumManagement.PS包。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>设置信息首选项

通过设置信息首选项变量，在执行 cmdlet 时，可以获取更多信息。 默认情况下 *，$InformationPreference*设置为 *"静默继续*"。

有关 cmdlet 的更多详细信息，在 PowerShell 中设置首选项，如下所示：

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>建立 Web3 连接

要管理联盟成员，请建立与区块链服务成员终结点的 Web3 连接。 您可以使用此脚本设置全局变量来调用联合体管理 cmdlet。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

将*\<成员帐户密码\>* 替换为创建成员时使用的会员帐户密码。

在 Azure 门户中查找其他值：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 转到默认区块链服务成员**概述**页面。

    ![成员概述](./media/manage-consortium-powershell/member-overview.png)

    将*\<成员帐户\>* 和*\<RootContract\>地址*替换为门户中的值。

1. 对于终结点地址，选择**事务节点**，然后选择**默认事务节点**。 默认节点的名称与区块链成员相同。
1. 选择**连接字符串**。

    ![连接字符串](./media/manage-consortium-powershell/connection-strings.png)

    将*\<终结点地址\>* 替换为**HTTPS（访问密钥 1）** 或**HTTPS（访问密钥 2）中**的值。

## <a name="manage-the-network-and-smart-contracts"></a>管理网络和智能合约

使用网络和智能合约 cmdlet 建立与负责联合管理的区块链端点智能合约的连接。

### <a name="import-consortiummanagementcontracts"></a>进口-联合管理合同

使用此 cmdlet 连接到联合体管理层的智能合约。 这些合同用于管理和强制执行联合体内的成员。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| 根合同地址 | 联营管理智能合同的根合同地址 | 是 |
| Web3客户端 | 从新 Web3连接获取的 Web3客户端对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>导入-Web3帐户

使用此 cmdlet 创建对象以保存远程节点的管理帐户的信息。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| 托管帐户地址 | 区块链成员账户地址 | 是 |
| 托管帐户密码 | 帐户地址密码 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>新-Web3连接

使用此 cmdlet 建立与事务节点的 RPC 终结点的连接。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| 远程RPC端点 | 区块链成员端点地址 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>管理联营成员

使用联营体成员管理 cmdlet 管理联合体内的成员。 可用的操作取决于您的联合体角色。

### <a name="get-blockchainmember"></a>获取区块链成员

使用此 cmdlet 获取联合体的成员详细信息或列表成员。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| “属性” | 要检索详细信息的区块链服务成员的姓名。 输入名称时，它将返回成员的详细信息。 省略名称时，它将返回所有联合体成员的列表。 | 否 |
| 成员 | 从导入-联合管理合同获取的成员对象 | 是 |
| Web3客户端 | 从新 Web3连接获取的 Web3客户端对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置$ContractConnection变量。

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>示例输出

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>删除-区块链成员

使用此 cmdlet 删除区块链成员。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| “属性” | 要删除的会员名称 | 是 |
| 成员 | 从导入-联合管理合同获取的成员对象 | 是 |
| Web3帐户 | 从导入 Web3 帐户获取的 Web3帐户对象 | 是 |
| Web3客户端 | 从新 Web3连接获取的 Web3客户端对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置$ContractConnection和$MemberAccount变量。

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>设置-区块链成员

使用此 cmdlet 设置区块链成员属性，包括显示名称和联合体角色。

联盟管理员可以为所有成员设置 **"显示名称**"和 **"角色**"。 具有用户角色的联合体成员只能更改其自己的成员的显示名称。

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| “属性” | 区块链成员名称 | 是 |
| DisplayName | 新的显示名称 | 否 |
| 帐户地址 | Account address | 否 |
| 成员 | 从导入-联合管理合同获取的成员对象 | 是 |
| Web3帐户 | 从导入 Web3 帐户获取的 Web3帐户对象 | 是 |
| Web3客户端 |  从新 Web3连接获取的 Web3客户端对象| 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置$ContractConnection和$MemberAccount变量。

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>管理联营成员的邀请

使用联营体成员邀请管理 cmdlet 管理联营成员的邀请。 可用的操作取决于您的联合体角色。

### <a name="new-blockchainmemberinvitation"></a>新区块链会员邀请

使用此 cmdlet 邀请新成员加入联合体。

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID | 是 |
| 角色 | 联合体角色。 值可以是 ADMIN 或 USER。 ADMIN 是联合管理人角色。 USER 是联合体成员角色。 | 是 |
| 成员 | 从导入-联合管理合同获取的成员对象 | 是 |
| Web3帐户 | 从导入 Web3 帐户获取的 Web3帐户对象 | 是 |
| Web3客户端 | 从新 Web3连接获取的 Web3客户端对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置$ContractConnection和$MemberAccount变量。

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>获取区块链成员邀请

使用此 cmdlet 检索或列出联合体成员的邀请状态。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID。 如果提供了订阅 ID，它将返回订阅 ID 的邀请详细信息。 如果省略订阅 ID，它将返回所有成员邀请的列表。 | 否 |
| 成员 | 从导入-联合管理合同获取的成员对象 | 是 |
| Web3客户端 | 从新 Web3连接获取的 Web3客户端对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置$ContractConnection变量。

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>示例输出

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>删除区块链成员邀请

使用此 cmdlet 撤销联合体成员的邀请。

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要撤销的成员的 Azure 订阅 ID | 是 |
| 成员 | 从导入-联合管理合同获取的成员对象 | 是 |
| Web3帐户 | 从导入 Web3 帐户获取的 Web3帐户对象 | 是 |
| Web3客户端 | 从新 Web3连接获取的 Web3客户端对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置$ContractConnection和$MemberAccount变量。

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>设置-区块链成员邀请

使用此 cmdlet 可设置现有邀请**的角色**。 只有联合管理人才能更改邀请。

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID | 是 |
| 角色 | 邀请的新联合体角色。 值可以是**USER**或**ADMIN**。 | 是 |
| 成员 |  从导入-联合管理合同获取的成员对象 | 是 |
| Web3帐户 | 从导入 Web3 帐户获取的 Web3帐户对象 | 是 |
| Web3客户端 | 从新 Web3连接获取的 Web3客户端对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置$ContractConnection和$MemberAccount变量。

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>后续步骤

有关联营、成员和节点的详细信息，请参阅[Azure 区块链服务联盟](consortium.md)
