---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "68966356"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果数据存储是以下方式之一配置的，则需要设置[自托管集成运行时](../articles/data-factory/create-self-hosted-integration-runtime.md)才能连接到此数据存储：

- 数据存储位于本地网络内部、Azure 虚拟网络内部或 Amazon 虚拟私有云内。
- 数据存储是一种托管云数据服务，在其中访问权限限制为列入防火墙规则允许列表的 IP。
