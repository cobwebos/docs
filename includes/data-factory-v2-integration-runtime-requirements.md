---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529376"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果数据存储位于本地网络、Azure 虚拟网络或 Amazon Virtual Private Cloud 内部，则需要设置[自承载集成运行时](../articles/data-factory/create-self-hosted-integration-runtime.md)才能连接到该数据存储。

如果数据存储是托管的云数据服务，则可以使用 Azure 集成运行时。 如果访问范围限制为防火墙规则中列入白名单的 IP，可以选择将 [Azure 集成运行时 IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) 添加到允许列表。 

要详细了解网络安全机制和数据工厂支持的选项，请参阅[数据访问策略](../articles/data-factory/data-access-strategies.md)。
