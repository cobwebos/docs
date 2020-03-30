---
title: Azure 区块链令牌可组合性
description: Azure 区块链令牌可组合性为高级方案创建令牌提供了灵活性。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325119"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure 区块链令牌可组合性

[!INCLUDE [Preview note](./includes/preview.md)]

令牌可组合性为高级方案创建令牌提供了灵活性。 您可能有一个复杂的方案，无法使用[四个预构建的令牌模板](templates.md#base-token-types)实现。 令牌可组合性允许您通过添加或删除定义的行为来构建自己的令牌模板来设计自己的令牌模板。 创建新令牌模板时，Azure 区块链令牌将验证所有令牌语法规则。 组合模板保存在 Azure 区块链令牌服务中，用于在连接的区块链网络上颁发。

您可以在以下部分中使用[令牌行为](templates.md#token-behaviors)来设计令牌模板。

## <a name="burnable-b"></a>可刻录 （b）

能够从电源中删除令牌。

例如，当您为礼品卡兑换在线信用卡积分时，信用卡积分会被烧毁。

## <a name="delegable-g"></a>可委派 （g）

能够委派对您拥有的令牌执行的操作。

委托可以作为令牌的所有者执行操作。 例如，您可以使用可委派的令牌来实现投票。 可委派的令牌允许投票令牌所有者让其他人代表他们投票。

## <a name="logable-l"></a>可日志 （l）

能够登录。

例如，您可以为每个放映特定影片的影院颁发电影分发的可日志令牌。 要播放影片，放映必须记录每个放映的事务，因为在影片上映期间，每次放映的版税支出。 参与者生成可以使用影片令牌来验证发行版中每个影院放映的放映每个影片的支出。

## <a name="mint-able-m"></a>薄荷 （m）

能够为令牌类铸造其他令牌。 混用角色包括可小人的行为。

例如，想要实施忠诚度计划的零售公司可以使用可分明的令牌来实施其忠诚度计划。 随着客户群的增长，他们可以为客户创造额外的忠诚度点。  

## <a name="non-subdividable-or-whole-d"></a>不可分或整体 （*d）

防止令牌被划分为更小的部分的限制。

例如，单个艺术绘画不能细分为多个较小的部分。 

## <a name="non-transferable-t"></a>不可转让 （+t）

防止从初始令牌所有者更改所有权的限制。

例如，大学文凭是不可转让的令牌。 一旦向毕业生颁发文凭，就不能从毕业生转到另一个人。

## <a name="roles-r"></a>角色 （r）

能够为特定行为在令牌模板类中定义角色。

可以提供令牌在令牌创建时支持的角色名称列表。 指定角色后，用户可以为这些行为分配角色。 目前，仅支持 minter 角色。

## <a name="singleton-s"></a>单顿 （s）

允许一个令牌的供应的限制。

例如，博物馆文物是单一标记。 博物馆文物是独一无二的。 表示工件的令牌在供应中只有一个项。

## <a name="subdividable-d"></a>可分div （d）

能够将令牌划分为更小的部分。

例如，一美元可以细分为美分。

## <a name="transferable-t"></a>可转让 （t）

能够转移令牌的所有权。

例如，属性所有权是可转让的令牌，在出售房产时，可以从一个人转移到另一个人。

## <a name="next-steps"></a>后续步骤

了解[Azure 区块链令牌帐户管理](account-management.md)。
