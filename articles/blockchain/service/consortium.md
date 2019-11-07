---
title: Azure 区块链服务联盟
description: 了解 Azure 区块链服务如何使用专用协会
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ddb3f02662c0c71ebc90e1a740b4068d6fbcded4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577507"
---
# <a name="azure-blockchain-service-consortium"></a>Azure 区块链服务联盟

使用 Azure 区块链服务，可以创建专用协会区块链网络，其中每个区块链网络可限制为网络中的特定参与者。 只有专用联合会区块链网络中的参与者才能查看区块链并与之进行交互。 Azure 区块链服务中的联合会网络可包含两种类型的成员参与者角色：

* **管理员**权限的参与者，可采用 "联盟" 管理操作并可参与区块链事务。

* 不能进行任何联盟管理操作但可以参与区块链事务的**用户**参与者。

联盟网络可以是参与者角色的混合，可以拥有任意数量的角色类型。 至少必须有一个管理员。

下图显示了具有多个参与者的联合会网络：

![专用联盟网络示意图](./media/consortium/network-diagram.png)

通过 Azure 区块链服务中的联合会管理，你可以管理联盟网络中的参与者。 协会的管理基于网络的共识模型。 在当前的预览版中，Azure 区块链服务提供了一种集中式共识模型，以便进行联盟管理。 任何具有管理角色的特权参与者都可以执行联合会管理操作，例如，从网络中添加或删除参与者。

## <a name="roles"></a>角色

联盟中的参与者可以是个人或组织，可以为其分配用户角色或管理员角色。 下表列出了这两个角色之间的高级差异：

| 操作 | 用户角色 | 管理员角色
|--------|:----:|:------------:|
| 创建新成员 | 是 | 是 |
| 邀请新成员 | 否 | 是 |
| 设置或更改成员参与者角色 | 否 | 是 |
| 更改成员显示名称 | 仅适用于自己的成员 | 仅适用于自己的成员 |
| 删除成员 | 仅适用于自己的成员 | 是 |
| 参与区块链事务 | 是 | 是 |

### <a name="user-role"></a>用户角色

用户是没有管理员功能的联盟参与者。 它们不能参与管理与联合会相关的成员。 用户可以更改其成员显示名称，并且可以从联合会删除自己。

### <a name="administrator"></a>管理员

管理员可以管理联盟中的成员。 管理员可以邀请成员、删除成员或更新联合会中的成员角色。
至少必须有一个联盟中的管理员。 在离开联盟之前，最后一个管理员必须将另一个参与者指定为管理员角色。

## <a name="managing-members"></a>管理成员

只有管理员才能邀请其他参与者加入联盟。 管理员使用其 Azure 订阅 ID 邀请参与者。

受邀后，参与者可以通过在 Azure 区块链服务中部署新成员来加入区块链协会。 若要查看并加入受邀的联盟，你必须指定网络管理员在邀请中使用的同一 Azure 订阅 ID。

管理员可以删除联盟中的任何参与者，包括其他管理员。 成员只能从联合会删除自己的成员。

## <a name="consortium-management-smart-contract"></a>联合会管理智能协定

Azure 区块链服务中的联盟管理是通过 "联盟管理" 智能协定实现的。 部署新的区块链成员时，智能协定会自动部署到节点。

可以在 Azure 门户中查看根联合会管理智能协定的地址。 **RootContract 地址**在区块链成员的 "概述" 部分中。

![RootContract 地址](./media/consortium/rootcontract-address.png)

可以使用联合会管理[PowerShell 模块](manage-consortium-powershell.md)、Azure 门户或直接通过使用 Azure 区块链 Service 生成的以太坊帐户通过智能协定与联合会管理智能协定进行交互。

## <a name="ethereum-account"></a>以太坊帐户

创建成员后，将创建以太坊帐户密钥。 Azure 区块链服务使用密钥来创建与联盟管理相关的事务。 以太坊帐户密钥由 Azure 区块链 Service 自动管理。

成员帐户可以在 Azure 门户中查看。 成员帐户在区块链成员的 "概述" 部分中。

![成员帐户](./media/consortium/member-account.png)

你可以通过单击成员帐户并输入新密码来重置你的以太坊帐户。 以太坊帐户地址和密码都将重置。  

## <a name="next-steps"></a>后续步骤

[如何使用 PowerShell 管理 Azure 区块链服务中的成员](manage-consortium-powershell.md)
