---
title: 管理 Azure 区块链服务联合会成员-PowerShell
description: 了解如何使用 Azure PowerShell 管理 Azure 区块链 Service 联合会成员。
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: 4eb35838394d3324f460b5e83cfbcb47b8114221
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455547"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>使用 PowerShell 管理 Azure 区块链服务中的联合会成员

你可以使用 PowerShell 来管理 Azure 区块链服务的区块链联合会成员。 具有管理员权限的成员可以邀请、添加、删除和更改区块链联合会中所有参与者的角色。 具有用户权限的成员可以查看区块链联合会中的所有参与者并更改其成员显示名称。

## <a name="prerequisites"></a>先决条件

* 使用[Azure 门户](create-member.md)创建区块链成员。
* 有关 consortia、成员和节点的详细信息，请参阅[Azure 区块链 Service 联合会](consortium.md)。

## <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

你还可以通过转到[shell.azure.com/powershell](https://shell.azure.com/powershell)在单独的浏览器选项卡中打开 Cloud Shell。 选择 "**复制**" 以复制代码块，将其粘贴到 Cloud Shell 中，然后选择 " **Enter** " 以运行。

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

从 PowerShell 库安装 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 包。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>设置信息首选项

通过设置信息首选项变量，可以在执行 cmdlet 时获取详细信息。 默认情况下， *$InformationPreference*设置为*SilentlyContinue*。

有关 cmdlet 的详细信息，请按如下所示在 PowerShell 中设置首选项：

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>建立 Web3 连接

若要管理协会成员，请建立到区块链服务成员终结点的 Web3 连接。 您可以使用此脚本设置全局变量，以便调用联合会管理 cmdlet。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

将 *\<成员帐户密码\>* 替换为在创建成员时使用的成员帐户密码。

在 Azure 门户中查找其他值：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请参阅默认区块链服务成员**概述**页。

    ![成员概述](./media/manage-consortium-powershell/member-overview.png)

    将 *\<成员帐户\>* 和 *\<RootContract address\>* 替换为门户中的值。

1. 对于终结点地址，请选择 "**事务节点**"，然后选择 "**默认事务" 节点**。 默认节点的名称与区块链成员的名称相同。
1. 选择“连接字符串”。

    ![连接字符串](./media/manage-consortium-powershell/connection-strings.png)

    将 *\<终结点地址\>* 替换为**https （访问密钥1）** 或**https （访问密钥2）** 中的值。

## <a name="manage-the-network-and-smart-contracts"></a>管理网络和智能协定

使用网络和智能协定 cmdlet 建立与区块链终结点的智能协定（负责联盟管理）的连接。

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

使用此 cmdlet 连接到联盟管理的智能协定。 这些协定用于管理和强制联盟中的成员。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| RootContractAddress | 联盟管理智能协定的根协定地址 | 是 |
| Web3Client | 从 Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

使用此 cmdlet 创建一个对象，用于保存远程节点的管理帐户的信息。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 区块链成员帐户地址 | 是 |
| ManagedAccountPassword | 帐户地址密码 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>新-Web3Connection

使用此 cmdlet 建立与事务节点的 RPC 终结点的连接。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 区块链成员终结点地址 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>管理协会成员

使用联合会成员管理 cmdlet 管理联盟中的成员。 可用操作取决于你的联盟角色。

### <a name="get-blockchainmember"></a>Get-BlockchainMember

使用此 cmdlet 可获取联盟的成员详细信息或列表成员。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| 名称 | 要检索其详细信息的区块链服务成员的名称。 输入名称后，将返回成员的详细信息。 省略名称时，它将返回所有 "联盟" 成员的列表。 | 否 |
| 成员 | 从 ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Client | 从 Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 变量。

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

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

使用此 cmdlet 可删除区块链成员。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| 名称 | 要删除的成员名称 | 是 |
| 成员 | 从 ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

使用此 cmdlet 设置区块链成员属性，包括显示名称和联盟角色。

联盟管理员可以为所有成员设置**DisplayName**和**Role** 。 具有用户角色的联合会成员只能更改自己的成员的显示名称。

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| 名称 | 区块链成员的名称 | 是 |
| DisplayName | 新显示名称 | 否 |
| AccountAddress | 帐户地址 | 否 |
| 成员 | 从 ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client |  从 Web3Connection 中获取的 Web3Client 对象| 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>管理协会成员的邀请

使用 "联合会成员邀请管理" cmdlet 来管理联合会成员邀请。 可用操作取决于你的联盟角色。

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

使用此 cmdlet 将新成员邀请到联盟。

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID | 是 |
| 角色 | 联盟角色。 值可以是 "管理员" 或 "用户"。 管理员是联盟管理员角色。 USER 是联合会成员角色。 | 是 |
| 成员 | 从 ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

使用此 cmdlet 可检索或列出联合会成员的邀请状态。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID。 如果提供了订阅 ID，它将返回订阅 ID 的邀请详细信息。 如果省略订阅 ID，它将返回所有成员邀请的列表。 | 否 |
| 成员 | 从 ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Client | 从 Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 变量。

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>示例输出

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

使用此 cmdlet 可撤消联合会成员的邀请。

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要撤消的成员的 Azure 订阅 ID | 是 |
| 成员 | 从 ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

使用此 cmdlet 设置现有邀请的**角色**。 只有联合会管理员才能更改邀请。

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID | 是 |
| 角色 | 邀请的新联盟角色。 值可以是 " **USER** " 或 " **ADMIN**"。 | 是 |
| 成员 |  从 ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>后续步骤

有关 consortia、成员和节点的详细信息，请参阅[Azure 区块链 Service 联合会](consortium.md)
