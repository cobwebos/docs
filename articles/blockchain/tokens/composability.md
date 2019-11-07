---
title: Azure 区块链令牌可组合性
description: Azure 区块链令牌可组合性为高级方案提供创建令牌的灵活性。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: a82d7ba606eac5dcafc26b1a8527810a5a21840d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577114"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure 区块链令牌可组合性

[!INCLUDE [Preview note](./includes/preview.md)]

令牌可组合性为高级方案提供创建令牌的灵活性。 可能有一个复杂的方案，不能使用[四个预建的标记模板](templates.md#base-token-types)实现。 令牌可组合性使你可以通过添加或删除定义的行为构建你自己的令牌模板来设计自己的令牌模板。 创建新的令牌模板时，Azure 区块链令牌将验证所有令牌语法规则。 组合模板保存在 Azure 区块链令牌服务中，用于在连接的区块链网络上发出。

您可以使用以下部分中的[令牌行为](templates.md#token-behaviors)设计标记模板。

## <a name="burnable-b"></a>Burnable （b）

能够从提供中删除令牌。

例如，当你兑换礼品卡的联机信用卡点时，信用卡点将被刻录。

## <a name="delegable-g"></a>可委派（g）

能够委托对所拥有的令牌执行的操作。

委托可以作为令牌所有者执行操作。 例如，可以使用可委派令牌来实现投票。 可委派令牌允许投票令牌所有者让其他人代表他们投票。

## <a name="logable-l"></a>Logable （l）

记录的功能。

例如，可以向每个显示特定电影的剧院发出电影分发的 logable 令牌。 对于要播放的电影，显示的必须记录每个显示的交易，因为版税付款是在电影的发行版期间显示的。 执行组件生成可以使用影片令牌来验证每个电影在分发中的付款。

## <a name="mint-able-m"></a>Mint （m）

能够 mint 令牌类的其他令牌。 Minter 角色包括 mintable 行为。

例如，要实现会员计划的零售公司可以将 mintable 令牌用于其会员计划。 当客户群增长时，他们可以为其客户 mint 额外的积分。  

## <a name="non-subdividable-or-whole-d"></a>非 subdividable 或整体（~ d）

限制以防止将令牌划分为较小的部分。

例如，单个艺术绘图不能细分为多个较小的部分。 

## <a name="non-transferable-t"></a>不可转让（~ t）

限制以防止初始令牌所有者更改所有权。

例如，大学文凭是不可转让的令牌。 将文凭提供给毕业后，不能将其从毕业生传输到其他人。

## <a name="roles-r"></a>角色（r）

能够在令牌模板类中为特定行为定义角色。

可以提供令牌创建时令牌支持的角色名称列表。 指定角色后，用户可以将角色分配给这些行为。 目前仅支持 minter 角色。

## <a name="singleton-s"></a>单一实例

允许一个令牌的提供的限制。

例如，博物馆项目是单独标记。 博物馆项目是唯一的。 表示项目的令牌仅在提供中具有单个项目。

## <a name="subdividable-d"></a>Subdividable （d）

能够将令牌划分为较小的部分。

例如，可以将美元细分为美分。

## <a name="transferable-t"></a>可转换（t）

能够转让令牌的所有权。

例如，属性标题是一个可转让标记，在销售属性时，可以将其从一个人传输到另一个。

## <a name="next-steps"></a>后续步骤

了解[Azure 区块链令牌帐户管理](account-management.md)。
