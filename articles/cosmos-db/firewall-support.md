---
title: Azure Cosmos 帐户的 IP 防火墙
description: 了解如何使用支持防火墙的 IP 访问控制策略来保护 Azure Cosmos DB 数据。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241080"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 IP 防火墙

为了保护帐户中存储的数据，Azure Cosmos DB 支持基于机密的授权模型，该模型利用强大的基于哈希的消息身份验证代码 (HMAC)。 此外，Azure Cosmos DB 支持使用基于 IP 的访问控制来提供入站防火墙支持。 此模型与传统数据库系统的防火墙规则类似，并且为帐户提供额外级别的安全性。 利用防火墙，可以配置为仅允许从一组已批准的计算机和/或云服务访问你的 Azure Cosmos 帐户。 从这些已批准的计算机和服务访问 Azure Cosmos 数据库中存储的数据仍需调用方提供有效的授权令牌。

## <a id="ip-access-control-overview"></a>IP 访问控制概述

默认情况下，只要请求附有有效的授权令牌，即可从 Internet 访问你的 Azure Cosmos 帐户。 若要配置基于 IP 策略的访问控制，用户必须提供 CIDR（无类域间路由）格式的、要作为客户端 IP 允许列表包含的 IP 地址或 IP 地址范围集，这样才能访问给定的 Azure Cosmos 帐户。 应用此配置后，源自此允许列表外部的计算机的所有请求将收到 403（禁止访问）响应。 使用 IP 防火墙时，我们建议允许 Azure 门户访问你的帐户。 需有相应的访问权限才能允许使用数据资源管理器，以及检索 Azure 门户中显示的帐户指标。 使用数据资源管理器时，除了要允许 Azure 门户访问你的帐户，还需更新防火墙设置，将当前 IP 地址添加到防火墙规则中。 请注意，防火墙更改可能需要长达 15 分钟的时间进行传播。 

可将基于 IP 的防火墙与子网和 VNET 访问控制结合使用。 将这两种策略相结合，可以限制访问具有某个公共 IP 的任何源，和/或从 VNET 中的特定子网访问任何源。 若要详细了解如何使用基于子网和 VNET 的访问控制，请参阅[从虚拟网络访问 Azure Cosmos DB 资源](vnet-service-endpoint.md)。

总而言之，始终需要提供授权令牌才能访问 Azure Cosmos 帐户。 如果未设置 IP 防火墙和 VNET 访问控制列表 (ACL)，可以使用授权令牌访问 Azure Cosmos 帐户。 在 Azure Cosmos 帐户中设置 IP 防火墙和/或 VNET ACL 后，只有源自指定的源（并且具有授权令牌）的请求才能收到有效的响应。 

## <a name="next-steps"></a>后续步骤

接下来，可参考以下文档为帐户配置 IP 防火墙或 VNET 服务终结点：

* [如何为 Azure Cosmos 帐户配置 IP 防火墙](how-to-configure-firewall.md)
* [从虚拟网络访问 Azure Cosmos DB 资源](vnet-service-endpoint.md)
* [如何为 Azure Cosmos 帐户配置虚拟网络服务终结点](how-to-configure-vnet-service-endpoint.md)




