---
title: 什么是 Azure 区块链服务的区块链数据管理器
description: 区块链数据管理器来捕获、转换区块链数据并将其传送到事件网格主题。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: e5fc38767d6127e341e257c23411d23b739d0362
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518237"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Azure 区块链服务的区块链数据管理器是什么？

区块链数据管理器捕获、转换 Azure 区块链服务事务数据，并将其传送到 Azure 事件网格主题，提供与 Azure 服务的可靠、可缩放的区块链分类帐集成。

在大多数企业区块链方案中，区块链分类帐是解决方案的一部分。 例如，若要将资产从一个实体转移到另一个实体，则需要一种用于提交事务的机制。 然后，你需要一种机制来读取分类帐数据，以确保事务发生、被接受，并将结果状态更改与端到端解决方案集成。 在此示例中，如果你编写了一个用于传输资产的智能协定，则可以使用区块链数据管理器将离链应用程序和数据存储集成在一起。 对于资产传输示例，在区块链上传输资产时，事件和属性状态更改通过事件网格由区块链数据管理器提供。 然后，可以使用事件网格的多个可能的事件处理程序将区块链的数据离线或响应状态更改。

区块链数据管理器执行三个主要功能：捕获、转换和传递。

![区块链数据管理器函数](./media/data-manager/functions.png)

## <a name="capture"></a>捕获

每个区块链数据管理器实例连接到一个 Azure 区块链服务成员事务节点。 只有有权访问事务节点的用户才能创建连接，确保对客户数据进行适当的访问控制。 区块链数据管理器实例可靠地从事务节点捕获所有原始块和原始事务数据，并可进行扩展以支持企业工作负荷。

## <a name="transform"></a>转换

可以通过在区块链数据管理器中配置智能协定应用程序，使用区块链数据管理器对事件和属性状态进行解码。 若要添加智能协定，请提供协定 ABI 和字节码。 区块链数据管理器使用智能协定项目来解码和发现协定地址。 将区块链应用程序添加到实例后，当智能协定部署到联合会并将解码的事件和属性状态发送到配置的目标时，区块链数据管理器会动态发现智能协定地址。

## <a name="deliver"></a>传送

对于任何给定的区块链数据管理器实例，区块链数据管理器支持多个事件网格主题出站连接。 可以将区块链数据发送到单个目标，或将区块链数据发送到多个目标。 使用区块链数据管理器，你可以为任何区块链部署构建基于事件的可扩展数据发布解决方案。

## <a name="configuration-options"></a>配置选项

你可以配置区块链数据管理器以满足你的解决方案的需求。 例如，你可以预配：

* Azure 区块链服务成员的单个区块链数据管理器实例。
* 数据管理器区块链每个 Azure 区块链 Service transaction 节点的实例。 例如，private transaction 节点可以有自己的区块链数据管理器实例来维护机密性。
* 区块链数据管理器实例可支持多个输出连接。 可以使用一个区块链数据管理器实例管理 Azure 区块链服务成员的所有数据发布集成点。

## <a name="next-steps"></a>后续步骤

尝试为 Azure 区块链服务成员[创建区块链数据管理器实例](data-manager-portal.md)。
