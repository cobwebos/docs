---
title: 使用 Azure PowerShell 管理 Azure 区块链服务联盟中的成员
description: 了解如何使用 Azure PowerShell 管理 Azure 区块链服务联盟成员。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66493681"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>使用 PowerShell 管理 Azure 区块链服务中的联盟成员

可以使用 PowerShell 为 Azure 区块链服务管理区块链联盟成员。 具有管理员权限的成员可以邀请、 添加、 删除和更改的区块链联盟中的所有参与者角色。 拥有用户权限的成员可以查看区块链联盟中的所有参与者并更改其成员的显示名称。

## <a name="prerequisites"></a>必备组件

* 使用创建区块链成员[Azure 门户](create-member.md)。
* 有关财团、 成员和节点的详细信息，请参阅[Azure 区块链服务联盟](consortium.md)。

## <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

您也可以打开 Cloud Shell 中单独的浏览器选项卡上通过转到[shell.azure.com/powershell](https://shell.azure.com/powershell)。 选择**副本**若要复制的代码块，将其粘贴到 Cloud Shell 中，然后选择**Enter**来运行它。

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

从 PowerShell 库安装 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 包。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>将信息首选项设置

通过设置信息首选项变量执行 cmdlet 时，可以获取详细信息。 默认情况下 *$InformationPreference*设置为*SilentlyContinue*。

有关从 cmdlet 的更详细信息，将首选项在 powershell 中设置，如下所示：

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>建立 Web3 连接

若要管理联盟成员，建立 Web3 连接到区块链服务成员终结点。 此脚本可用于设置全局变量来调用联盟管理 cmdlet。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

替换 *\<成员帐户密码\>* 创建成员时使用的成员帐户密码。

在 Azure 门户中找到其他值：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 转到您的默认区块链服务成员**概述**页。

    ![成员概述](./media/manage-consortium-powershell/member-overview.png)

    替换 *\<成员帐户\>* 并 *\<RootContract 地址\>* 在门户中的值。

1. 终结点地址中，选择**事务节点**，然后选择**默认事务节点**。 默认的节点具有区块链成员相同的名称。
1. 选择“连接字符串”  。

    ![连接字符串](./media/manage-consortium-powershell/connection-strings.png)

    替换 *\<终结点地址\>* 中的值**HTTPS （访问键 1）** 或者**HTTPS （访问键 2）** 。

## <a name="manage-the-network-and-smart-contracts"></a>管理网络和智能协定

使用网络和智能协定 cmdlet 建立与负责联盟管理的区块链终结点的智能协定。

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

使用此 cmdlet 连接到联盟管理智能协定。 这些协定用于管理和实施联盟中的成员。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| RootContractAddress | 根协定地址的联盟管理智能协定 | 是 |
| Web3Client | 获取从新建 Web3Connection Web3Client 对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

使用此 cmdlet 以创建对象以保存远程节点的管理帐户的信息。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 区块链成员帐户地址 | 是 |
| ManagedAccountPassword | 帐户地址密码 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

使用此 cmdlet 来连接到 RPC 终结点的事务节点。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 参数 | 描述 | 需要 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 区块链成员终结点地址 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>管理联盟成员

联盟成员管理 cmdlet 用于管理联盟中的成员。 可用操作取决于你联盟的角色。

### <a name="get-blockchainmember"></a>Get-BlockchainMember

使用此 cmdlet 可获取成员的详细信息或列出的联盟的成员。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 描述 | 需要 |
|-----------|-------------|:--------:|
| Name | 你想要检索有关其详细信息的区块链服务成员的名称。 输入一个名称，它将返回该成员的详细信息。 当省略名称时，它将返回所有联盟成员的列表。 | 否 |
| Members | 获取从导入 ConsortiumManagementContracts 成员对象 | 是 |
| Web3Client | 获取从新建 Web3Connection Web3Client 对象 | 是 |

#### <a name="example"></a>示例

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

使用此 cmdlet 删除区块链成员。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| Name | 若要删除的成员名称 | 是 |
| Members | 获取从导入 ConsortiumManagementContracts 成员对象 | 是 |
| Web3Account | 获取从导入 Web3Account Web3Account 对象 | 是 |
| Web3Client | 获取从新建 Web3Connection Web3Client 对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

使用此 cmdlet 设置成员属性，包括显示名称和联盟角色的区块链。

联盟管理员可以设置**DisplayName**并**角色**所有成员。 用户角色的联盟成员可以更改仅自己成员的显示名称。

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 参数 | 描述 | 需要 |
|-----------|-------------|:--------:|
| Name | 区块链成员的名称 | 是 |
| DisplayName | 新的显示名称 | 否 |
| AccountAddress | 帐户地址 | 否 |
| Members | 获取从导入 ConsortiumManagementContracts 成员对象 | 是 |
| Web3Account | 获取从导入 Web3Account Web3Account 对象 | 是 |
| Web3Client |  获取从新建 Web3Connection Web3Client 对象| 是 |

#### <a name="example"></a>示例

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>管理联盟成员的邀请

联盟成员邀请管理 cmdlet 用于管理联盟成员邀请。 可用操作取决于你联盟的角色。

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

此 cmdlet 用于邀请到联盟的新成员。

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 azure 订阅 ID | 是 |
| Role | 联盟角色中。 值可以是管理员或用户。 管理员是联盟管理员角色。 用户是联盟成员角色。 | 是 |
| Members | 获取从导入 ConsortiumManagementContracts 成员对象 | 是 |
| Web3Account | 获取从导入 Web3Account Web3Account 对象 | 是 |
| Web3Client | 获取从新建 Web3Connection Web3Client 对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

使用此 cmdlet 来检索或列出联盟成员邀请状态。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID。 如果所在的订阅 ID 提供，它将返回订阅 ID 邀请的详细信息。 如果省略的订阅 ID，则返回所有成员邀请的列表。 | 否 |
| Members | 获取从导入 ConsortiumManagementContracts 成员对象 | 是 |
| Web3Client | 获取从新建 Web3Connection Web3Client 对象 | 是 |

#### <a name="example"></a>示例

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

使用此 cmdlet 来撤消联盟成员的邀请。

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要撤消的成员的 azure 订阅 ID | 是 |
| Members | 获取从导入 ConsortiumManagementContracts 成员对象 | 是 |
| Web3Account | 获取从导入 Web3Account Web3Account 对象 | 是 |
| Web3Client | 获取从新建 Web3Connection Web3Client 对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

使用此 cmdlet 来设置**角色**现有邀请。 只有联盟管理员可以更改的邀请。

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| 参数 | 描述 | 必选 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 azure 订阅 ID | 是 |
| Role | 邀请新联盟的角色。 值可以是**用户**或**管理员**。 | 是 |
| Members |  获取从导入 ConsortiumManagementContracts 成员对象 | 是 |
| Web3Account | 获取从导入 Web3Account Web3Account 对象 | 是 |
| Web3Client | 获取从新建 Web3Connection Web3Client 对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>后续步骤

有关财团、 成员和节点的详细信息，请参阅：

> [!div class="nextstepaction"]
> [Azure 区块链服务联盟](consortium.md)
