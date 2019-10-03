---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966356"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果数据存储是通过以下方式之一配置的, 则需要设置[自承载 Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md)以便连接到此数据存储:

- 数据存储位于本地网络内部、Azure 虚拟网络内部或 Amazon 虚拟私有云内。
- 数据存储是一种托管的云数据服务, 其中的访问权限限制为防火墙规则中的 Ip 白名单。
