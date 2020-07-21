---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544447"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果数据存储位于本地网络、Azure 虚拟网络或 Amazon 虚拟私有云内部，需要设置[自承载集成运行时](../articles/data-factory/create-self-hosted-integration-runtime.md)以连接到该虚拟网络。

如果你的数据存储是托管的云数据服务，而该服务的访问权限仅限于防火墙规则中的允许列表，则可以使用 Azure 集成运行时并将[其 ip](../articles/data-factory/azure-integration-runtime-ip-addresses.md)添加到允许列表中。 

有关数据工厂支持的网络安全机制（一般访问数据存储）的信息，请参阅[数据访问策略](../articles/data-factory/data-access-strategies.md)。
