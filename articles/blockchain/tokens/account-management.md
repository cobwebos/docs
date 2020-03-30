---
title: Azure 区块链令牌帐户管理
description: 使用 Azure 区块链令牌帐户管理，您可以创建组并链接区块链帐户，以控制对区块链操作的访问。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326102"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure 区块链令牌帐户管理

[!INCLUDE [Preview note](./includes/preview.md)]

对于区块链解决方案，用户可能需要对使用 Azure 区块链令牌服务创建的令牌进行不同级别的访问。 在大多数区块链方案中，您需要规划和部署分类账上存在的不同区块链账户。 您还需要管理参与者之间的访问。使用 Azure 区块链令牌帐户管理，您可以创建组并链接区块链帐户，以控制对区块链操作的访问。

## <a name="blockchain-networks"></a>区块链网络

Azure 区块链令牌支持跨一组区块链网络部署和管理令牌。 您可以将单个区块链分类账或多个区块链分类账连接到服务。

## <a name="accounts"></a>帐户

对于连接到 Azure 区块链令牌的区块链网络，该服务创建和管理帐户私有-公钥对，并执行事务签名和提交。 Azure 区块链令牌还提供标识映射，以将帐户与分类帐上的公钥标识匹配。

## <a name="groups"></a>组

通过组，您可以跨互联网络管理大量区块链账户。 您可以跟踪和审核目录中哪些应用程序和用户能够通过 Azure 区块链令牌 API 使用帐户。 例如，您可以对一组表示不同业务线或不同角色和访问区块链令牌的帐户进行分组。

还可以将组关联到 Azure 活动目录用户或服务主体，并且此主体对组及其关联帐户具有权限。  

## <a name="next-steps"></a>后续步骤

详细了解可用的 [Azure Blockchain Tokens 模板](templates.md)。
