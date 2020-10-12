---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91672122"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果数据存储位于本地网络、Azure 虚拟网络或 Amazon Virtual Private Cloud 内部，则需要配置[自承载集成运行时](../articles/data-factory/create-self-hosted-integration-runtime.md)才能连接到该数据存储。

另外，如果数据存储是托管的云数据服务，可以使用 Azure 集成运行时。 如果访问范围限制为防火墙规则中允许的 IP，你可以选择将 [Azure Integration Runtime IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) 添加到允许列表。 

要详细了解网络安全机制和数据工厂支持的选项，请参阅[数据访问策略](../articles/data-factory/data-access-strategies.md)。
